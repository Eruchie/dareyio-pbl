## DOCUMENTATION ON MEAN STACK DEPLOYMENT - PROJECT 4 ##
---
**MEAN** stack is a combination of the following components:
  - **MongoDB** : This is a document database which stores and allows data retrieval.
  - **Express** : This is a back-end application frame work which makes requests to tke database for read/write.
  - **Angular** : This is a front end application framework which handles client server requests.
  - **Node.js** : This is the JavaScript runtime enviroment which accepts requests and displays it's results to the end user.
  
1. **INSTALL NODE JS**: `node.js` is a JavaScript runtime built on Chrome's v8 JavaScript engine. `node.js` is used in this tutorial to setup the express routes and AngularJS controllers. 

   a. Update and upgrade ubuntu

    - `sudp apt update && sudo apt upgrade`

      ![step1](./project4Pictures/step1_p4.JPG)

   b. Add Certificates

   - `sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates`

     ![step2](./project4Pictures/step2_p4.JPG)

   - `curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -`

     ![step3](./project4Pictures/step3_p4.JPG)

   c. Install `node.js`
    - `sudo apt install -y nodejs`
        
      ![step4](./project4Pictures/step4_p4.JPG)

1. **INSTALL MONGO DB**: MongoDB stores data in flexible `JASON-like` documents. Fields in a database can vary form document to document and the structure can be changed overtime. our example application, we will add book records to MongoDB that contain bppk name, isbn number, author and number of pages. 

   a. Import MongoDB publick key 
   - `wget -qO - https://www.mongodb.org/static/pgp/server-6.0.asc | sudo apt-key add -`
   
     ![step5](./project4Pictures/step5_p4.JPG)


   b. Create the list file using the command for the version of Ubuntu. In our case Ubuntu 22.04 is jammy  
   - `echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list`
   
     ![step6](./project4Pictures/step6_p4.JPG)

   c. Reload the local package database  
   - `sudo apt update`
   
     ![step7](./project4Pictures/step7_p4.JPG)
 

   d. Install MongoDB  
   - `sudo apt install -y mongodb-org`
   
     ![step8](./project4Pictures/step8_p4.JPG)  

   e. In case you get libssl error from the above command 
You can download & install the package from http://archive.ubuntu.com/ubuntu/pool/main/o/openssl/ and reinstall MongoDB  
   - `curl -LO http://archive.ubuntu.com/ubuntu/pool/main/o/openssl/libssl1.1_1.1.1-1ubuntu2.1~18.04.20_amd64.deb`
   
     ![step8a](./project4Pictures/step8a_p4.JPG)  

   - `sudo dpkg -i ./libssl1.1_1.1.1-1ubuntu2.1~18.04.20_amd64.deb`
   
     ![step8b](./project4Pictures/step8b_p4.JPG) 

   - `sudo apt install -y mongodb-org`
   
     ![step8c](./project4Pictures/step8c_p4.JPG)   

   f. Check MongoDB version
   - `mongod -version`
   
     ![step9](./project4Pictures/step9_p4.JPG) 

   g. Start and enable MongoDB and confirm its status
   - `sudo systemctl start mongod`
   - `sudo systemctl status mongod`
   
     ![step10](./project4Pictures/step10_p4.JPG) 