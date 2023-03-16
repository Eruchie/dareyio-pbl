**POROJECT 12 - ANSIBLE REFACTORING AND STATIC ASSIGNMENTS (IMPORTS AND ROLES)**

---

In this project we will continue working with the `ansible-config-mgt` repository and make some improvements in the code.

We will refactor the Ansible code, create assignments, and learn how to use the `imports` functionality. Imports allow us effectively re-use previously created playbooks in a new playbook – it allows us organize tasks and reuse them when needed.

**CODE REFACTORING**

`Refactoring` is a general term in computer programming. It means making changes to the source code without changing expected behaviour of the software. The main idea of refactoring is to enhance code readability, increase maintainability and extensibility, reduce complexity, add proper comments without affecting the logic.

In this project, we will move things around a little bit in the code, but the overal state of the infrastructure remains the same.

**STEP 1 - JENKINS JOB ENHANCEMENT**
Every new change in the code create a separate directory which is not very convenient when we want to run some commands from one place. Also, it consumes space on `Jenkins` server with each subsequent change. Let us enhance it by introducing a new Jenkins project/job – we will require **Copy Artifact** plugin.

1. ssh to `Jenkins-Ansible` server, cd to the path `/var/lib/jenkins`. Switch account to jenkins user and create a new directory called `ansible-config-artifact` – this is where all the artifacts will be stored after each build.

   - `cd /var/lib/jenkins`
   - `sudo su jenkins`
   - `mkdir ansible-config-artifact`

     ![Pic1](./project12Pictures/step1_p12.JPG)

2. Go to Jenkins web console -> Manage Jenkins -> Manage Plugins -> on `Available plugins` tab search for `Copy Artifact` and install this plugin without restarting Jenkins.

   ![Pic2](./project12Pictures/step2_p12.JPG)

3. Create a new Freestyle project and name it `save_artifacts`. This project will be triggered by completion of your existing `ansible` project. Configure it accordingly:

   - Navigate to `save_artifacts` project, in the **General** section, check **Discard old builds** and ensure `Log Rotaion` is selected under **Strategy**. The **Max# of builds to keep** should be 2. You can configure number of builds to keep in order to save space on the server, for example, you might want to keep only last 2 or 5 build results. You can also make this change to the `ansible` job.

     ![Pic3](./project12Pictures/step3_p12.JPG)

   - In the **Build Triggers** section, check **Build after other projects are built** and ensure `ansible` is selected under **Projects to watch**. Also, select the option **Trigger only if build is stable**.

     ![Pic4](./project12Pictures/step4_p12.JPG)

4. The main idea of `save_artifacts` project is to save artifacts into `/home/ubuntu/ansible-config-artifact` directory. To achieve this, create a `Build` step and choose **Copy artifacts from other project**, specify ansible as a source project and /home/ubuntu/ansible-config-artifact as a target directory. Configure it accordingly:

   - In the **Build Steps** section, select **Copy artifacts from other project**. Under **Artifacts to copy** type `**` and on **Target Directory** type the path `/var/lib/jenkins/ansible-config-artifact` and then click **Save**.

     ![Pic5](./project12Pictures/step5_p12.JPG)

5. Test your set up by making some change in README.MD file inside your `ansible-config-mgt` repository (right inside `main` branch).

   ![Pic6](./project12Pictures/step6_p12.JPG)

6. If both Jenkins jobs have completed one after another – you shall see your files inside `/var/lib/jenkins/ansible-config-artifact` directory and it will be updated with every commit to your `main` branch.

   ![Pic7](./project12Pictures/step7_p12.JPG)

**STEP 2 - REFACTOR ANSIBLE CODE BY IMPORTING OTHER PLAYBOOKS INTO SITE.YML**

Before starting to `refactor` the codes, ensure that you have pulled down the latest code from `main` branch, and created a new branch, name it `refactor`. Refactoring is one of the techniques that can be used to effect constant iterative improvement for better efficiency.

   ![Pic8a](./project12Pictures/step8a_p12.JPG)

   ![Pic8b](./project12Pictures/step8b_p12.JPG)


In this project, we will learn how to organize complex sets of tasks by breaking them up into different files and reuse them.

Let see **code re-use** in action by importing other playbooks:

1. Create a new file within `playbooks` folder and name it `site.yml` - This file will now be considered as an entry point into the entire infrastructure configuration. `site.yml` will become a parent to all other playbooks that will be developed, they will be included here as a reference.

   ![Pic9](./project12Pictures/step9_p12.JPG)

2. Create a new folder in root of the repository and name it `static-assignments`. The **static-assignments** folder is where all other children playbooks will be stored.

3. Move `common.yml` file into the newly created `static-assignments` folder.

4. Inside `site.yml` file, import `common.yml` playbook.

   ```py
   - hosts: all
   - import_playbook: ../static-assignments/common.yml
   ```
     ![Pic10a](./project12Pictures/step10a_p12.JPG)

     The code above uses built in `import_playbook` Ansible module. The folder structure should look like this;

   ```py
   ├── static-assignments
   │   └── common.yml
   ├── inventory
      └── dev
      └── stage
      └── uat
      └── prod
   └── playbooks
      └── site.yml
   ```
5. Since `wireshark` is already installed, create another playbook under static-assignments and name it `common-del.yml`. In this playbook, configure uninstallation of `wireshark` utility.

   ```
   ---
   - name: update web, nfs and db servers
   hosts: webservers, nfs, db
   remote_user: ec2-user
   become: yes
   become_user: root
   tasks:
   - name: delete wireshark
      yum:
         name: wireshark
         state: removed

   - name: update LB server
   hosts: lb
   remote_user: ubuntu
   become: yes
   become_user: root
   tasks:
   - name: delete wireshark
      apt:
         name: wireshark-qt
         state: absent
         autoremove: yes
         purge: yes
         autoclean: yes
   ```
   ![Pic10b](./project12Pictures/step10b_p12.JPG)

6. Update `site.yml` with `- import_playbook: ../static-assignments/common-del.yml` instead of `common.yml`. 

   ![Pic10c](./project12Pictures/step10c_p12.JPG)


7. Merge, push and pull the necessary repositories run it against `dev` servers:

   ```
   cd /home/ubuntu/ansible-config-mgt/

   ansible-playbook -i inventory/dev.yml playbooks/site.yaml
   ```

   ![Pic12a](./project12Pictures/step12a_p12.JPG)

8. Ensure that `wireshark` is deleted on all the servers by running `wireshark --version`.

   ![Pic12b](./project12Pictures/step12b_p12.JPG)


**STEP 3 - CONFIGURE UAT WEBSERVERS WITH A ROLE 'WEBSERVER'**

configure 2 new Web Servers as `uat`. We could write tasks to configure Web Servers in the same playbook, but it would be too messy, instead, we will use a dedicated `role` to make our configuration reusable.

1. Launch 2 fresh EC2 instances using RHEL 8 image, we will use them as our `uat` servers, so give them names accordingly – `Web1-UAT` and `Web2-UAT`.

2. Create a role by creating a directory called `roles` relative to the playbook file or in /`etc/ansible/ `directory.

   There are two ways how you can create this folder structure:

   - Use an Ansible utility called `ansible-galaxy` inside `ansible-config-mngt/`roles directory (you need to create roles directory upfront)

      ```
      mkdir roles
      cd roles
      ansible-galaxy init webserver
      ```

   - Create the directory/files structure manually. You can choose either way, but since all the codes are stored in GitHub, it is recommended to create folders and files there rather than locally on `Jenkins-Ansible` server.

   The entire folder structure should look like below, but if you create it manually – you can skip creating `tests`, `files`, and `vars` or remove them if you used `ansible-galaxy`.

   ```
   └── webserver
      ├── README.md
      ├── defaults
      │   └── main.yml
      ├── files
      ├── handlers
      │   └── main.yml
      ├── meta
      │   └── main.yml
      ├── tasks
      │   └── main.yml
      ├── templates
      ├── tests
      │   ├── inventory
      │   └── test.yml
      └── vars
         └── main.yml
   ```
   After removing unnecessary directories and files, the `roles` structure should look like this:

   ```
   └── webserver
      ├── README.md
      ├── defaults
      │   └── main.yml
      ├── handlers
      │   └── main.yml
      ├── meta
      │   └── main.yml
      ├── tasks
      │   └── main.yml
      └── templates
   ```
   ![Pic13a](./project12Pictures/step13a_p12.JPG)

3. Update your inventory `ansible-config-mngt/inventory/uat.yml` file with IP addresses of your 2 UAT Web servers.

   ```
   [uat_webservers]
   <Web1-UAT-Server-Private-IP-Address> ansible_ssh_user='ec2-user' 

   <Web2-UAT-Server-Private-IP-Address> ansible_ssh_user='ec2-user'
   ```

   ![Pic13b](./project12Pictures/step13b_p12.JPG)

4. Locate ansible.cfg file via the path `/etc/ansible/ansible.cfg`, uncomment `roles_path` string and provide a full path to the `roles` directory i.e. `roles_path = /home/ubuntu/ansible-config-mngt/roles`, so Ansible could know where to find configured `roles`.

   **Note**: In some cases, the path `/etc/ansible/ansible.cfg` may not exist. You will have to create `ansible` directory and `ansible.cfg` file in `/etc` directory and add the string path to the `roles` directory. 

    ![Pic14a](./project12Pictures/step14a_p12.JPG)

    ![Pic14b](./project12Pictures/step14b_p12.JPG)

5. It is time to start adding some logic to the webserver role. Go into `tasks` directory, and within the `main.yml` file, start writing configuration tasks to do the following:
   - Install and configure Apache (`httpd` service).
   - Clone **Tooling website** from GitHub `https://github.com/<your-name>/tooling.git`.
   - Ensure the tooling website code is deployed to `/var/www/html` on each of 2 UAT Web servers.
   - Make sure `httpd` service is started.

   The main.yml may consist of following tasks:

   ```py
   ---
   - name: install apache
   become: true
   ansible.builtin.yum:
      name: "httpd"
      state: present

   - name: install git
   become: true
   ansible.builtin.yum:
      name: "git"
      state: present

   - name: clone a repo
   become: true
   ansible.builtin.git:
      repo: https://github.com/<your-name>/tooling.git
      dest: /var/www/html
      force: yes

   - name: copy html content to one level up
   become: true
   command: cp -r /var/www/html/html/ /var/www/

   - name: Start service httpd, if not started
   become: true
   ansible.builtin.service:
      name: httpd
      state: started

   - name: recursively remove /var/www/html/html/ directory
   become: true
   ansible.builtin.file:
      path: /var/www/html/html
      state: absent
   ```
   ![Pic15](./project12Pictures/step15_p12.JPG)

**STEP 4 - REFERENCE WEB SERVER ROLE**

Within the `static-assignments` folder, create a new assignment for **uat_webservers** `uat_webservers.yml`. This is where the role will be referenced.

   ```
   ---
   - hosts: uat_webservers
   roles:
      - webserver
   ```
   Remember that the entry point to our ansible configuration is the `site.yml` file. Therefore, you need to refer your `uat_webservers.yml` role inside `site.yml`.

   So, we should have this in `site.yml`

   ```py
   ---
   - hosts: all
   - import_playbook: ../static-assignments/common.yml

   - hosts: uat-webservers
   - import_playbook: ../static-assignments/uat_webservers.yml
   ```
   You may disabla the first role.

   ![Pic16a](./project12Pictures/step16a_p12.JPG)

**STEP 5 - COMMIT AND TEST**

1. Commit the changes where necessary, create a Pull Request and merge them to `main` branch, make sure webhook triggered two consequent `Jenkins` jobs, they ran successfully and copied all the files to your `Jenkins-Ansible` server into `/home/ubuntu/ansible-config-mngt/` directory.

    ![Pic16b](./project12Pictures/step16b_p12.JPG)

2. Create a directory key and store the pem file created in aws which was used in creating the `EC2` instances. Update ansible.cfg file with the path to the pem file. `private_key_file = /home/ubuntu/key/projectKey.pem`

   ```
   [defaults]
   roles_path = /home/ubuntu/ansible-config-mngt/roles

   private_key_file = /home/ubuntu/key/projectKey.pem
   ```
   ![Pic17](./project12Pictures/step17_p12.JPG)

3. Run the playbook against the `uat` inventory.

   ```
   sudo ansible-playbook -i /home/ubuntu/ansible-config-mngt/inventory/uat.yml /home/ubuntu/ansible-config-mngt/playbooks/site.yml
   ```
   ![Pic18](./project12Pictures/step18_p12.JPG)

4. You should be able to see both of your UAT Web servers configured and you can try to reach them from your browser:

   `http://<Web1-UAT-Server-Public-IP-or-Public-DNS-Name>/index.php`

   ![Pic19a](./project12Pictures/step19a_p12.JPG)

   or

   `http://<Web1-UAT-Server-Public-IP-or-Public-DNS-Name>/index.php`

   ![Pic19b](./project12Pictures/step19b_p12.JPG)

5. The Ansible architecture now looks like this:

   ![Pic20](./project12Pictures/step20_p12.JPG)