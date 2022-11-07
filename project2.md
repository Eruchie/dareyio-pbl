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

   e. Test login to mysql-server using the command below (enter the new password)

   - `sudo mysql -p`

     ![step10](./projectPictures/step10_p1.JPG)

1. **Install php**: After installing Apache to serve your content and MySql installed to store and manage data, install php which is the componet required to process code to display dynamic content to the end user.

   a. Install the dependencies required for php to communicate with mysql and apache

   - `sudo apt install php-fpm php-mysql`

     ![step11](./projectPictures/step11_p2.JPG)

1. **Configuring NGINX to use PHP processor**: When using the Nginx web server, we can create server blocks (similar to virtual hosts in Apache) to encapsulate configuration details and host more than one domain on a single server. We will use aufora as the domain name.

   a. Create directory structure for `aufora` website using 'mkdir' command as follows

   - `sudo mkdir /var/www/aufora`

     ![step12](./projectPictures/step13_p2.JPG)

   b. Assign ownership of directory with the current  using `$USER` which will reference the current system user.

   - `sudo chown -R $USER:$USER /var/www/aufora`

     ![step13](./projectPictures/step14_p2.JPG)

   c. Create and open a new config file in nginx's `sites available` directory using nano editor

   - `sudo nano /etc/nginx/sites-available/aufora`

   - Paste the folowing in the snippet below

     ![step14](./projectPictures/step15_p2.JPG)

   d. Activate your configuration by linking to the config file from Nginx’s `sites-enabled` directory

   - `sudo ln -s /etc/nginx/sites-available/aufora /etc/nginx/sites-enabled/`

     ![step15](./projectPictures/step16_p2.JPG)

   e. Test the configuration for syntax errors 

   - `sudo unlink /etc/nginx/sites-enabled/default`

     ![step16](./projectPictures/step17_p2.JPG)

   f. Disable the default nginx host that is currently configured to listen on port 80 

   - `sudo nginx -t`

     ![step17](./projectPictures/step18_p2.JPG)

   g. Reload nginx to apply changes 

   - `sudo systemctl reload nginx`

     ![step18](./projectPictures/step19_p2.JPG)

   h. Create an `index.html` file in the web root location `/var/www/aufora` so that we can test if the new server block works as expected 

   - `sudo echo 'Hello LEMP from AUFORA' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/aufora/index.html`

     ![step19](./projectPictures/step20_p2.JPG)

   i. Launch the new website using the public IP

   - `http://3.145.13.48`

     ![step20](./projectPictures/step21_p2.JPG)

   j. Launch the new website using the public DNS

   - `http://ec2-3-145-13-48.us-east-2.compute.amazonaws.com/`

     ![step21](./projectPictures/step22_p2.JPG)
