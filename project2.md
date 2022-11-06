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
