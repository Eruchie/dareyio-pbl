**CONTINOUS INTEGRATION PIPELINE FOR TOOLING WEBSITE - PROJECT 9**
___
In this project we are going to start automating part of our routine tasks with a free and open source automation server – `Jenkins`. It is one of the most popular `CI/CD` tools, created by a former `Sun Microsystems` developer Kohsuke Kawaguchi.

Acording to Circle CI, **Continuous integration (CI)** is a software development strategy that increases the speed of development while ensuring the quality of the code that teams deploy. Developers continually commit code in small increments (at least daily, or even several times a day), which is then automatically built and tested before it is merged with the shared repository.

In this project we are going to utilize `Jenkins` CI capabilities to make sure that every change made to the source code in GitHub https://github.com/eruchie/tooling will be automatically be updated to the Tooling Website.

**TASK**

The architecture prepared in **Project 8** will be enhanced by adding a `Jenkins` server and also, a job will be configured to automatically deploy source code changes from `Git` to `NFS` server.

The updated architecture will look like this upon completion of this project:

![Pic0](./project9Pictures/step0_p9.JPG)

**STEP 1 - INSTALL AND CONFIGURE JENKINS SERVER**

1. Create an Ubuntu Server 20.04 EC2 instance and name it `Jenkins`.

2. Install `JDK` (since Jenkins is a Java-based application)

   - `sudo apt update`
   - `sudo apt install default-jdk-headless`

     ![Pic1](./project9Pictures/step1_p9.JPG)

3. Install `Jenkins` 

   - `wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
      sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
      /etc/apt/sources.list.d/jenkins.list'` 

     ![Pic2](./project9Pictures/step2_p9.JPG)

    - `sudo apt update`

      ![Pic3](./project9Pictures/step3_p9.JPG)

    - `sudo apt-get install jenkins`

      ![Pic4](./project9Pictures/step4_p9.JPG)
    
4. Confirm that `Jenkins` is up and running. 

   - `sudo systemctl status jenkins` 

     ![Pic5](./project9Pictures/step5_p9.JPG)

5. By default `Jenkins` server uses TCP port `8080` – open it by creating a new Inbound Rule in your EC2 Security Group.

    ![Pic05](./project9Pictures/step05_p9.JPG)

6. Perform initial Jenkins setup.
From the browser access http://`<Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>`:8080. You will be prompted to provide a default admin password.

   - `http://`<Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>`:8080` 

     ![Pic6](./project9Pictures/step6_p9.JPG)

7. Retrieve the password from the `Jenkins` server using the command below.

   - `sudo cat /var/lib/jenkins/secrets/initialAdminPassword` 

     ![Pic7](./project9Pictures/step7_p9.JPG)

8. You will be prompted to install a plugin– choose `Install suggested plugins`. 

     ![Pic8](./project9Pictures/step8_p9.JPG)

9. Once plugins installation is complete – create an `admin` user. 

     ![Pic9](./project9Pictures/step9_p9.JPG)


**STEP 2 - CONFIGURE JENKINS TO RETRIEVE SOURCE CODES FROM GITHUB USING WEBHOOKS**

In this step, we will learn how to configure a simple `Jenkins` job/project (these two terms can be used interchangeably). This job will will be triggered by GitHub `webhooks` and will execute a `‘build’` task to retrieve codes from GitHub and store it locally on Jenkins server.

1. Enable `webhooks` in your GitHub repository settings.

   - On the `tooling` repository click settings.

     ![Pic10a](./project9Pictures/step10a_p9.JPG)
   
   - Click `Webhooks` below on the left pane. 

     ![Pic10b](./project9Pictures/step10b_p9.JPG)

   - Click `Add webhook`.

     ![Pic10c](./project9Pictures/step10c_p9.JPG)
   
   - On **Payload** field, add http://`<Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>`:8080/github-webhook. 

   - Select `application/json` on **Content type**. 

   - Click `Add webhook`.

     ![Pic10d](./project9Pictures/step10d_p9.JPG)

2. Go to Jenkins web console.

   - Click `New Item`, give it a name `tooling_github`.
   
   - Select `Freestyle project` and click `ok`.

     ![Pic11](./project9Pictures/step11_p9.JPG)

3. To connect GitHub repository, you will need to provide its URL, you can copy from the `tooling` repository itself.

    ![Pic12](./project9Pictures/step12_p9.JPG) 

4. In configuration of `Jenkins freestyle` project choose `Git repository`, provide there the link to your Tooling GitHub repository and credentials (user/password) so `Jenkins` could access files in the repository. 

    ![Pic13](./project9Pictures/step13_p9.JPG) 

   - Click `New Item`, give it a name `tooling_github`.

5. `Save` the configuration and try to run the build. For now we can only do it manually. Click `Build Now` button.  

    ![Pic14](./project9Pictures/step14_p9.JPG) 

6. If everything was configured correctly, the build will be `successfull` and you will see it under `#1`.

     ![Pic15a](./project9Pictures/step15a_p9.JPG)

7. Open the `build` and check in `Console Output` if it has run successfully. If so – congratulations! You have just made your very first `Jenkins build`!

     ![Pic15b](./project9Pictures/step15b_p9.JPG)

     ![Pic15c](./project9Pictures/step15c_p9.JPG)

8. This build does not produce anything and it runs only when we trigger it manually. Let us fix it.

   - Click `Configure` your job/project and add these two configurations.

     ![Pic16a](./project9Pictures/step16a_p9.JPG)

    - To configure triggering the job from GitHub webhook, goto `Build Triggers` and select `GitHub hook trigger for GITScm polling`.

      ![Pic16b](./project9Pictures/step16b_p9.JPG)

    - Configure **Post-build Actions** to archive all the files – files resulted from a build are called `artifacts`. Select `Archive the artifacts`

      ![Pic16c](./project9Pictures/step16c_p9.JPG)

    - Type `**` under **Files to archive** field and click `Save`.

      ![Pic16d](./project9Pictures/step16d_p9.JPG)

9. Make some change in any file in your GitHub repository (e.g. `README.MD` file) and push the changes to the master branch.

   You will see that a new build has been launched automatically (by webhook) and you can see its results – artifacts, saved on Jenkins server.
   Also check the status of the build number, `#2` in our case.

     ![Pic17a](./project9Pictures/step17a_p9.JPG)

      ![Pic17b](./project9Pictures/step17b_p9.JPG)


