## Documentation of my First Project

1. **Installing Apache and updating the firewall**: After creating and logging into my instance via ssh, I ran the following commands:

    a. Update a list of packages in package manager

   - `sudo apt update`
     ![step1](./dareyio-pbl/projectPictures/step1_p1.JPG)

    b. Install Apache
   - `sudo apt install apache2`
     ![alt text](/https://github.com/Eruchie/dareyio-pbl/blob/main/dareyio-pbl/projectPictures/step2_p1.JPG)

    c. Verify the status of Apache2 to see if it is running.
   - `sudo systemctl status apache2`
   ![alt text](/dareyio-pbl/projectPictures/step3_p1.JPG)

   d. Check local access to web through the local host
   - `curl http://localhost:80`
   ![alt text](/dareyio-pbl/projectPictures/step4_p1.JPG)

   e. Check web access from internet through the firewall
   - `http://3.22.169.27:80`
   ![alt text](/dareyio-pbl/projectPictures/step5_p1.JPG)