## WEB STACK IMPLEMENTATION (MERN STACK)
---
1. **Backend Configuration**: 

   a. Update a list of packages in package manager

   - `sudo apt update`
   ![step1](./project3Pictures/step1_p3.JPG)

   b. Upgrade Ubuntu

   - `sudo apt upgrade`
   ![step2](./project3Pictures/step2_p3.JPG)

   c. Get the location of `node.js` software from Ubuntu repositories.
   
   - `curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -`

     ![step3](./project3Pictures/step3_p3.JPG)

   d. Install `node.js` on the Server. The command below will install both `nodejs` and `npm`. NPM is a special package for `nodejs` just like `apt` for ubuntu.
   
   - `sudo apt-get install -y nodejs`

     ![step4](./project3Pictures/step4_p3.JPG)
