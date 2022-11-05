## Documentation of my First Project

1. **Installing Apache and updating the firewall**: After creating and logging into my instance via ssh, I ran the following commands:

    a. Update a list of packages in package manager

   - `sudo apt update`
     
     ![step1](./projectPictures/step1_p1.JPG)

    b. Install Apache
   - `sudo apt install apache2`
     
      ![alt text](./projectPictures/step2_p1.JPG)

    c. Verify the status of Apache2 to see if it is running.
   - `sudo systemctl status apache2`
   
      ![alt text](./projectPictures/step3_p1.JPG)

   d. Check local access to web through the local host
   - `curl http://localhost:80`
  
      ![alt text](./projectPictures/step4_p1.JPG)

   e. Check web access from internet through the firewall

   - `http://3.22.169.27:80`

      ![alt text](./projectPictures/step5_p1.JPG)

1. **Installing mysql**: After installing Apache, install mysql to be able to store and manage data for your site in a relational database:

    a. Install mysql

   - `sudo apt install mysql-server`
     
     ![step1](./projectPictures/step6_p1.JPG)

    b. Log into mysql console

   - `sudo mysql-server`
     
     ![step1](./projectPictures/step7_p1.JPG)

   c. Run a script to remove some insecure default settings. Set user’s password as PassWord.1.

   - `ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';`
     
     ![step1](./projectPictures/step8_p1.JPG)

   d. Start the interactive script by running the command below

   - `sudo mysql_secure_installation`
     
     ![step1](./projectPictures/step8_p1.JPG)









