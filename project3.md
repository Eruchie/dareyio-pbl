## WEB STACK IMPLEMENTATION (MERN STACK) PROJECT 3
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
     - Create a new task
     - Display list of tasks
     - Delete a completed task

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

      n. MONGODB Database; We need to create a database to store data and this will be acheived using **mLab** which provies mongoDB database as a solution (DBaaS). 
      
      We will start by creating a shared clusters free account on [MongoDB](https://www.mongodb.com/cloud/atlas/register)

      - On Database Deployments click **Build a Database**.

        ![step21](./project3Pictures/step21_p3.JPG)

      - On Deploy a cloud database click **create** on the free option.

        ![step22](./project3Pictures/step22_p3.JPG)

      - On the next page, click free shared, AWS as the cloud provider,  any suitable region and then click **Create Cluster**.

        ![step23](./project3Pictures/step23_p3.JPG)

      - Create a username and password.

        ![step24](./project3Pictures/step24_p3.JPG)  
      
      - Select **Cloud Enviroment** and allow access from anywhere `0.0.0.0/0`.

        ![step25](./project3Pictures/step25_p3.JPG)

      - Click **Finish and Close**.

        ![step26](./project3Pictures/step26_p3.JPG) 

      - Click **Go to Databases**.  

        ![step27](./project3Pictures/step27_p3.JPG)  

      - Click the newly created cluster, **Cluster0** in this case.  

        ![step28](./project3Pictures/step28_p3.JPG) 

      - Click **Collections**.  

        ![step29](./project3Pictures/step29_p3.JPG)  

      - Click **Add My Own Data**.  

        ![step30](./project3Pictures/step30_p3.JPG)   

      - Give a database and collection name, click **Create**.  

        ![step31](./project3Pictures/step31_p3.JPG)   
  

   o. In the instance created earlier, `process.env` was specified in the `index.js`to access enviroment varibles.
   
   This file has to be created in the `Todo` directory and named `.env`.

     - `touch.env && vi .env`

   Paste the connection string below to access the database.

    -  `DB = mongodb+srv://aufora:IwQGG2gwwjm5d2Zf@cluster0.fwffaed.mongodb.net/auforadb?retryWrites=true&w=majority`

       ![step32](./project3Pictures/step32_p3.JPG)
       ![step32a](./project3Pictures/step32a_p3.JPG)

   **NOTE**: The connection string can be gotten as thus:

    - Click **Connect**.  

        ![step33](./project3Pictures/step33_p3.JPG) 

    - Click **Connect your application**.  

        ![step34](./project3Pictures/step34_p3.JPG) 

     - Copy the application string.  

        ![step35](./project3Pictures/step35_p3.JPG) 


   p. Update the `index.js` to reflect the use of `.env` so that `node.js` can connect to the database.
   
     - To begin, delete existing content in the file using `:%d`, and update it with the entire code below.

       ```py
       const express = require('express');
       const bodyParser = require('body-parser');
       const mongoose = require('mongoose');
       const routes = require('./routes/api');
       const path = require('path');
       require('dotenv').config();

       const app = express();

       const port = process.env.PORT || 5000;

       //connect to the database
       mongoose.connect(process.env.DB, { useNewUrlParser: true, useUnifiedTopology: true })
       .then(() => console.log(`Database connected successfully`))
       .catch(err => console.log(err));

       //since mongoose promise is depreciated, we overide it with node's promise
       mongoose.Promise = global.Promise;

       app.use((req, res, next) => {
       res.header("Access-Control-Allow-Origin", "\*");
       res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
       next();
       });

       app.use(bodyParser.json());

       app.use('/api', routes);

       app.use((err, req, res, next) => {
       console.log(err);
       next();
       });

       app.listen(port, () => {
       console.log(`Server running on port ${port}`)
       });
       ```
       ![step36](./project3Pictures/step36_p3.JPG) 

       Using environment variables to store information is considered more secure and best practice to separate configuration and secret data from the application, instead of writing connection strings directly inside the index.js application file.

    q. Start the server and expect to see the message `Database connected successfully` which signifies the backend was configured properly.

    - `node index.js`

      ![step37](./project3Pictures/step37_p3.JPG)

    r. Test the backend code without frontend using RESTful API.
    
    - The backend code of our `Todo` list application has been written with a database confgured. We will use some API development client to test our code. Postman will be used to test the application. 

    - Open Postman, create a POST request to the API. This request will send a new task to our To-Do list so the application could store it in the database:
    
    - On the header tab, set the key: `Content-Type`, value = `application\json` and POST = `http://52.14.204.42:5000/api/todos`

       ![step38a](./project3Pictures/step38a_p3.JPG) 

    - Modify the body as thus: 
      ```py
      {
        "action":"This is PROJECT 3"
      }
      ```

      ![step38b](./project3Pictures/step38b_p3.JPG) 

    - Click **Send**; the result should be as shown below.

      ![step38c](./project3Pictures/step38_p3.JPG) 

    - Create a GET request to the API on `http://52.14.204.42:5000/api/todos`. This request retrieves all existing records from out To-do application (backend requests these records from the database and sends it us back as a response to GET request).

    - On the header tab, set the key: `Content-Type`, value = `application\json` and GET = `http://52.14.204.42:5000/api/todos`
    
      ![step39](./project3Pictures/step39_p3.JPG)

    - To test the DELETE request add the ID `636e9cb4384eb7e5045e3508` as part of the delete request, set the key: `Content-Type`, value = `application\json` and DELETE = `http://52.14.204.42:5000/api/todos/636e9cb4384eb7e5045e3508` and click send
    
      ![step40](./project3Pictures/step40_p3.JPG)

    The Backend has been configured successfully and all the three operations required have been confirmed. 

    - Display a list of tasks – HTTP GET request
    - Add a new task to the list – HTTP POST request
    - Delete an existing task from the list – HTTP DELETE request  

    
  1. **Frontend Creation**: A user interface will be created for a web client (browser) to interact with the application via API. To scaffold our app, run the command `create-react-app` in the same root directory, `Todo` as the backend code.
  
     - `npx create-react-app client`

       ![step41a](./project3Pictures/step41a_p3.JPG) 
       ![step41b](./project3Pictures/step41b_p3.JPG) 

     There are some dependencies that will be instaled before testing the react app.

     a. Install `concurrently` used to run more than one command simultaneously from the same terminal window

     - `npm install concurrently --save-dev`

       ![step42](./project3Pictures/step42_p3.JPG)

     b. Install `nodemon` used to monitor the server. `nodemon` will restart the server automatically if there is any change and in turn load the new changes.

     - `npm install nodemon --save-dev`

       ![step43](./project3Pictures/step43a_p3.JPG)

     c. In the `Todo` folder vim into `package.json file and change code in the screenshot with the below script:

     ```py
      "scripts": {
      "start": "node index.js",
      "start-watch": "nodemon index.js",
      "dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
      },
     ```
    
       ![step44a](./project3Pictures/step44a_p3.JPG)
       ![step44b](./project3Pictures/step44b_p3.JPG)

       d. Configure Proxy in `package.json` file located in the client directory; this will make possible access to the application directly through the browser by callling on the server url like `http://localhost:5000` rather than always including the entire path like `http://localhost:5000/api/todos`.

     - `cd client`
     - `vim package.json`
     - `"proxy":"http://18.219.89.177:5000"`

     e. Navigate back to `Todo` directory and run the command

     - `npm run dev`

       ![step45a](./project3Pictures/step45a_p3.JPG)

     - `http://18.219.89.177:3000/`

       ![step45b](./project3Pictures/step45b_p3.JPG)

      e. Creating REACT components. REACT makes use of reusable components and it also makes code modular. For the `Todo` app, there will be two stateful components and one stateless component. Run the following in `Todo' directory.

      - `cd client && cd src && mkdir components && cd components`

      Create 3 files inside the components directory
      
      - `Touch Input.js ListTodo.js Todo.js` 

        ![step46](./project3Pictures/step46_p3.JPG)

     f. Copy and paste the script below into `Input.js`. 

     ```py
      import React, { Component } from 'react';
      import axios from 'axios';

      class Input extends Component {

      state = {
      action: ""
      }

      addTodo = () => {
      const task = {action: this.state.action}

          if(task.action && task.action.length > 0){
            axios.post('/api/todos', task)
              .then(res => {
                if(res.data){
                  this.props.getTodos();
                  this.setState({action: ""})
                }
              })
              .catch(err => console.log(err))
          }else {
            console.log('input field required')
          }

      }

      handleChange = (e) => {
      this.setState({
      action: e.target.value
      })
      }

      render() {
      let { action } = this.state;
      return (
      <div>
      <input type="text" onChange={this.handleChange} value={action} />
      <button onClick={this.addTodo}>add todo</button>
      </div>
      )
      }
      }

      export default Input
     ```
     ![step47](./project3Pictures/step47_p3.JPG)

     g. Navigate to `client` directory and install `axios`. This is a promise based HTTP client for the browser and node.js.

     - `npm install axios`

       ![step48](./project3Pictures/step48_p3.JPG)

     h. Navigate to `components` directory

     - `cd src/components`
     - `vi ListTodo.js`

     In the `ListTodo.js` copy and paste the following code and save.

     ```py
      import React from 'react';

      const ListTodo = ({ todos, deleteTodo }) => {

      return (
      <ul>
      {
      todos &&
      todos.length > 0 ?
      (
      todos.map(todo => {
      return (
      <li key={todo._id} onClick={() => deleteTodo(todo._id)}>{todo.action}</li>
      )
      })
      )
      :
      (
      <li>No todo(s) left</li>
      )
      }
      </ul>
      )
      }

      export default ListTodo
     ```
     ![step49](./project3Pictures/step49_p3.JPG)

     i. In the `Todo.js` copy and paste the following code and save.

     ```py
      import React, {Component} from 'react';
      import axios from 'axios';

      import Input from './Input';
      import ListTodo from './ListTodo';

      class Todo extends Component {

      state = {
      todos: []
      }

      componentDidMount(){
      this.getTodos();
      }

      getTodos = () => {
      axios.get('/api/todos')
      .then(res => {
      if(res.data){
      this.setState({
      todos: res.data
      })
      }
      })
      .catch(err => console.log(err))
      }

      deleteTodo = (id) => {

          axios.delete(`/api/todos/${id}`)
            .then(res => {
              if(res.data){
                this.getTodos()
              }
            })
            .catch(err => console.log(err))

      }

      render() {
      let { todos } = this.state;

          return(
            <div>
              <h1>My Todo(s)</h1>
              <Input getTodos={this.getTodos}/>
              <ListTodo todos={todos} deleteTodo={this.deleteTodo}/>
            </div>
          )

      }
      }

      export default Todo;
     ```
     ![step50](./project3Pictures/step50_p3.JPG)

     j. We need to adjust te REACT code. Delete the logo and adjust `App.js` file. To do this navigate to the `src` directory and open `App.js`.

      - `vi App.js`

     Copy and paste the following script and save.

     ```py
      import React from 'react';

      import Todo from './components/Todo';
      import './App.css';

      const App = () => {
      return (
      <div className="App">
      <Todo />
      </div>
      );
      }

      export default App;
     ```
     ![step51](./project3Pictures/step51_p3.JPG)

     k. Still on the `src` directory, open `App.css`.

      - `vi App.css`

     Copy, paste the following script and save.

     ```
      .App {
      text-align: center;
      font-size: calc(10px + 2vmin);
      width: 60%;
      margin-left: auto;
      margin-right: auto;
      }

      input {
      height: 40px;
      width: 50%;
      border: none;
      border-bottom: 2px #101113 solid;
      background: none;
      font-size: 1.5rem;
      color: #787a80;
      }

      input:focus {
      outline: none;
      }

      button {
      width: 25%;
      height: 45px;
      border: none;
      margin-left: 10px;
      font-size: 25px;
      background: #101113;
      border-radius: 5px;
      color: #787a80;
      cursor: pointer;
      }

      button:focus {
      outline: none;
      }

      ul {
      list-style: none;
      text-align: left;
      padding: 15px;
      background: #171a1f;
      border-radius: 5px;
      }

      li {
      padding: 15px;
      font-size: 1.5rem;
      margin-bottom: 15px;
      background: #282c34;
      border-radius: 5px;
      overflow-wrap: break-word;
      cursor: pointer;
      }

      @media only screen and (min-width: 300px) {
      .App {
      width: 80%;
      }

      input {
      width: 100%
      }

      button {
      width: 100%;
      margin-top: 15px;
      margin-left: 0;
      }
      }

      @media only screen and (min-width: 640px) {
      .App {
      width: 60%;
      }

      input {
      width: 50%;
      }

      button {
      width: 30%;
      margin-left: 10px;
      margin-top: 0;
      }
      }
     ```
     ![step52](./project3Pictures/step52_p3.JPG)

     k. On the `src` directory, open `index.css`.

      - `vim index.css`

     Copy, paste the following script and save.

     ```py
      body {
      margin: 0;
      padding: 0;
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen",
      "Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue",
      sans-serif;
      -webkit-font-smoothing: antialiased;
      -moz-osx-font-smoothing: grayscale;
      box-sizing: border-box;
      background-color: #282c34;
      color: #787a80;
      }

      code {
      font-family: source-code-pro, Menlo, Monaco, Consolas, "Courier New",
      monospace;
      }
     ```
     ![step53](./project3Pictures/step53_p3.JPG)

     l. Navigate to the directory `Todo` and run the command below:

      - `npm run dev`

        ![step54](./project3Pictures/step54_p3.JPG)

      - `npm run dev`

      - `http://18.219.89.177:3000/`

        ![step55](./project3Pictures/step55_p3.JPG)