## DOCUMENTATION OF LAMP STACK IMPLEMENTATION - PROJECT 1
---
1. **Installing Apache and updating the firewall**: After creating and logging into my instance via ssh, I ran the following commands:

   a. Update a list of packages in package manager

   - `sudo apt update`

     ![step1](./projectPictures/step1_p1.JPG)

   b. Install Apache

   - `sudo apt install apache2`

     ![alt text](./projectPictures/step2_p1.JPG)

   c. Verify the status of Apache2 to see if it is running.

   - `sudo systemctl status apache2`

     ![step3](./projectPictures/step3_p1.JPG)

   d. Check local access to web through the local host

   - `curl http://localhost:80`

     ![step4](./projectPictures/step4_p1.JPG)

   e. Check web access from internet through the firewall

   - `http://3.22.169.27:80`

     ![step5](./projectPictures/step5_p1.JPG)

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

1. **Installing php**: After installing Apache to serve your content and MySql installed to store and manage data, install php which is the componet required to process code to display dynamic content to the end user.

   a. Install the dependencies required for php to communicate with mysql and apache

   - `sudo apt install php libapache2-mod-php php-mysql`

     ![step11](./projectPictures/step11_p1.JPG)

    b. Confirm php version

   - `php -v`

     ![step12](./projectPictures/step12_p1.JPG)


1. **Creating a virtual host**: Create a virtual host for your website using apache using aufora as your domain name.

   a. Create directory for aufora using 'mkdir' command as follows

   - `sudo mkdir /var/www/aufora`

     ![step13](./projectPictures/step13_p1.JPG)

   b. Assign ownership of directory with the current user

   - `sudo chown -R $USER:$USER /var/www/aufora`

     ![step14](./projectPictures/step14_p1.JPG)

   c. Create and open a new config file in Apache's `sites available` directory using vim editor

   - `sudo vi /etc/apache2/sites-available/aufora.conf`

   - Paste the folowing in the snippet below

     ![step15](./projectPictures/step15_p1.JPG)

   d. Confirm the new file in the sites-available directory

   - `sudo ls /etc/apache2/sites-available`

     ![step16](./projectPictures/step16_p1.JPG)

   e. Enable the virtual host using the `a2ensite` command

   - `sudo a2ensite aufora`

     ![step17](./projectPictures/step17_p1.JPG)

   f. Disable Apache default website using the `a2dissite` command

   - `sudo a2dissite 000-default`

     ![step18](./projectPictures/step18_p1.JPG)

   g. To ensure the configuration file does not contain syntax errors, run:

   - `sudo a2dissite 000-default`

     ![step19](./projectPictures/step19_p1.JPG)

   h. Reload Apache for changes to take effect

   - `sudo systemctl reload apache2`

     ![step20](./projectPictures/step20_p1.JPG)

   i. Create an index.html file in that location in order to test the virtual host

   - `sudo echo 'Hello LAMP from hostname created by Mervin' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/aufora/index.html`

     ![step21](./projectPictures/step21_p1.JPG)

   j. Launch the new website using the public IP

   - `http://18.223.23.80`

     ![step22](./projectPictures/step22_p1.JPG)

   j. Launch the new website using the public DNS

   - `http://ec2-18-223-23-80.us-east-2.compute.amazonaws.com/`

     ![step23](./projectPictures/step23_p1.JPG)

1. **Enable PHP on the website**: With the default DirectoryIndex settings on Apache, a file named `index.html` will always take precedence over an `index.php` file. `index.html` is useful for temporary/maintenance page. 

   a. Change the behavior by editing the `/etc/apache2/mods-enabled/dir.conf` file and change the order in which the `index.php` file is listed within the DirectoryIndex directive:

   - `sudo vim /etc/apache2/mods-enabled/dir.conf`
   - Replace information in the below snippet with information in the next 

     ![step24a](./projectPictures/step24a_p1.JPG)
      ![step24b](./projectPictures/step24b_p1.JPG)

   b. Reload Apache for changes to take effect

   - `sudo systemctl reload apache2`

     ![step25](./projectPictures/step20_p1.JPG)

   c. Create index.php file in the web root folder

   - `sudo vim /var/www/aufora/index.php`

   d. Add the following PHP code and save

   - `<?php`

     `phpinfo();`  

     ![step25](./projectPictures/step25_p1.JPG)

   e. Refresh the webpages opened using public IP and public DNS

   - `http://18.223.23.80` and `http://ec2-18-223-23-80.us-east-2.compute.amazonaws.com/`

     ![step26](./projectPictures/step26_p1.JPG)
