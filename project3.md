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

    e. Verify your installations.
   
   - `node -v`
   - `npm -v`

     ![step5](./project3Pictures/step5_p3.JPG)

   f. Application code setup; Create a new directory `Todo` for the To-Do project.
   
   - `mkdir Todo`
   - `ls`
   - `cd Todo`

     ![step6](./project3Pictures/step6_p3.JPG)

   g. Use the command `npm init` to initialise the project, so that a new file named `package.json` will be created.
   
   - `npm init`

     ![step7](./project3Pictures/step7_p3.JPG)
     ![step7](./project3Pictures/step7b_p3.JPG)

   e. Confirm the `package.json` file has been created.
   
   - `ls`

     ![step8](./project3Pictures/step8_p3.JPG)

1. **Install ExpressJS**: Express is a framework for `node.js`, therefore a lot of things developers would have programmed is already taken care of simplifying development. Express helps define routes of the application based on HTTP methods and URLs. 

   a. Install express using `npm`:

   - `npm install express`

     ![step9](./project3Pictures/step9_p3.JPG)     

   b. Create a file `index.js`

   - `touch index.js`
  
     ![step10](./project3Pictures/step10_p3.JPG) 

   c. Install the `dotenv` module

   - `npm install dotenv`
   
     ![step11](./project3Pictures/step11_p3.JPG) 

   d. Open the `index.js` 
   
   - `vim index.js`

   e. Open the following command

     ```py
    const express = require('express');
    require('dotenv').config();

    const app = express();

    const port = process.env.PORT || 5000;

    app.use((req, res, next) => {
    res.header("Access-Control-Allow-Origin", "\*");
    res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
    next();
    });

    app.use((req, res, next) => {
    res.send('Welcome to Express');
    });

    app.listen(port, () => {
    console.log(`Server running on port ${port}`)
    });

   ``` 
   
    Note that we have specified to use port 5000 in the code. This will be required later when we go on the browser. Use :w to save in vim and use :qa to exit vim

     f. Start the server to see if it works. Open the terminal in the same directory as your index.js file and type:
   
   - `node index.js`
    
     ![step13](./project3Pictures/step13_p3.JPG)

    g. Open up your browser and try to access your server’s Public IP or Public DNS name followed by port 5000
   
   - `http://3.134.116.219:5000`
   
     ![step14](./project3Pictures/step14_p3.JPG)

    h. Routes; There are three actions that the To-Do application needs to be able to do:
     1. Create a new task
     2. Display list of tasks
     3. Delete a completed task

     Each task will be associated with some particular endpoint and will use different standard HTTP request methods: POST GET DELETE.

     For each task, create routes that will define various endpoints that the To-do app will depend on. Carry out the folloing commands
   
   - `mkdir routes`
   - `cd routes`
   - `touch api.js`
   - `vim api.js`
   
     ![step15](./project3Pictures/step15_p3.JPG)

    i. Copy below code in the file. 

     ```py
     const express = require ('express');
     const router = express.Router();

     router.get('/todos', (req, res, next) => {

     });

     router.post('/todos', (req, res, next) => {

     });

     router.delete('/todos/:id', (req, res, next) => {

     })

     module.exports = router;

     ```
     ![step16](./project3Pictures/step16_p3.JPG)

     j. Models; A model will be created since the app will use MongoDB which is a NoSQL database. A model is at the heart of JavaScript based applications, and it is what makes it interactive. 

     We will use a model to define the database schema; a schema is a blueprint on how the database will be constructed.

     To create a Schema and a model, we need to install `mongoose` which is a node.js package whch makes working with mongodb easier.

     - `npm install mongoose`
    
       ![step17](./project3Pictures/step17_p3.JPG)

     k. Create a new folder `models`, change directory into the newly created `models` folder and create a file named todo.js. **Tip**: Use `&&` operator to execute all three commands in one line as thus:

     - `mkdir models && cd models && touch todo.js`
    
       ![step18](./project3Pictures/step18_p3.JPG)

     l. Open the file created with `vim todo.js`, paste the code below in the file and then save and exit:

      ```py
      const mongoose = require('mongoose');
      const Schema = mongoose.Schema;

      //create schema for todo
      const TodoSchema = new Schema({
      action: {
      type: String,
      required: [true, 'The todo text field is required']
      }
      })

      //create model for todo
      const Todo = mongoose.model('todo', TodoSchema);

      module.exports = Todo;
      ```
      ![step19](./project3Pictures/step19_p3.JPG)

    m. To make use of the new model, we need to update the routes from the file `api.js` in routes directory.

    In the routes directory, open `api.js` with `vim` editor, delete the code inside using `:%d` command, paste the code below into it then save and exit.

    ```py
     const express = require ('express');
     const router = express.Router();
     const Todo = require('../models/todo');

     router.get('/todos', (req, res, next) => {

     //this will return all the data, exposing only the id and action field to the client
     Todo.find({}, 'action')
     .then(data => res.json(data))
     .catch(next)
     });

     router.post('/todos', (req, res, next) => {
     if(req.body.action){
     Todo.create(req.body)
     .then(data => res.json(data))
     .catch(next)
     }else {
     res.json({
     error: "The input field is empty"
     })
     }
     });

     router.delete('/todos/:id', (req, res, next) => {
     Todo.findOneAndDelete({"_id": req.params.id})
     .then(data => res.json(data))
     .catch(next)
     })

     module.exports = router;
    ```
    ![step20](./project3Pictures/step20_p3.JPG)



