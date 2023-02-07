**POROJECT 12 - ANSIBLE REFACTORING AND STATIC ASSIGNMENTS (IMPORTS AND ROLES)**
___
In this project we will continue working with the `ansible-config-mgt` repository and make some improvements in the code.

We will refactor the Ansible code, create assignments, and learn how to use the `imports` functionality. Imports allow us effectively re-use previously created playbooks in a new playbook – it allows us organize tasks and reuse them when needed.

**CODE REFACTORING**

`Refactoring` is a general term in computer programming. It means making changes to the source code without changing expected behaviour of the software. The main idea of refactoring is to enhance code readability, increase maintainability and extensibility, reduce complexity, add proper comments without affecting the logic.

In this project, we will move things around a little bit in the code, but the overal state of the infrastructure remains the same.

**STEP 1 - JENKINS JOB ENHANCEMENT**
Every new change in the code create a separate directory which is not very convenient when we want to run some commands from one place. Also, it consumes space on `Jenkins` server with each subsequent change. Let us enhance it by introducing a new Jenkins project/job – we will require **Copy Artifact** plugin.

1. ssh to `Jenkins-Ansible` server and create a new directory called `ansible-config-artifact` – this is where all the artifacts will be stored after each build. Change permissions to this directory, so Jenkins could save files there.

   - `sudo mkdir /home/ubuntu/ansible-config-artifact`

   - `chmod -R 0777 /home/ubuntu/ansible-config-artifact`

     ![Pic1](./project12Pictures/step1_p12.JPG)

2. Go to Jenkins web console -> Manage Jenkins -> Manage Plugins -> on `Available plugins` tab search for `Copy Artifact` and install this plugin without restarting Jenkins.

   ![Pic2](./project12Pictures/step2_p12.JPG)