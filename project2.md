## WEB STACK IMPLEMENTATION (LEMP STACK) - PROJECT 2

1. **Installing the NGINX Web Server**: After creating and logging into my instance via ssh, I ran the following commands:

   a. Update a list of packages in package manager

   - `sudo apt update`

     ![step1](./projectPictures/step1_p2.JPG)

   b. Install NGINX

   - `sudo apt install nginx`

     ![alt text](./projectPictures/step2_p2.JPG)

   c. Verify the status of Apache2 to see if it is running.

   - `sudo systemctl status nginx`

     ![step3](./projectPictures/step3_p2.JPG)

   d. Check local access to web through the local host

   - `curl http://localhost:80`

     ![step4](./projectPictures/step4_p2.JPG)

   e. Check web access from internet through the firewall

   - `http://3.138.188.10:80`

     ![step5](./projectPictures/step5_p2.JPG)

1. **Installing mysql**: After installing Apache, install mysql to be able to store and manage data for your site in a relational database:

   a. Install mysql

   - `sudo apt install mysql-server`

     ![step6](./projectPictures/step6_p1.JPG)

   b. Log into mysql console

   - `sudo mysql`

     ![step7](./projectPictures/step7_p1.JPG)

   c. Run a script to remove some insecure default settings. Set user’s password as PassWord.1.

   - `ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';`

     ![step8](./projectPictures/step8_p1.JPG)

   d. Start the interactive script by running the command below

   - `sudo mysql_secure_installation`

     ![step9](./projectPictures/step9_p1.JPG)

   e. Test login to mysql-server using the command below

   - `sudo mysql -p`

     ![step10](./projectPictures/step10_p1.JPG)
