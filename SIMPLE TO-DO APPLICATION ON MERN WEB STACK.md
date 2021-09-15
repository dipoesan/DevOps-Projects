# SIMPLE TO-DO APPLICATION ON MERN WEB STACK

This project is going to involve us deploying a simple To-Do application that creates a To-Do list like what we have below:

The MERN web stack consists of the following components - 
* [MongoDB](https://www.mongodb.com/): A document-based, No-SQL database used to store application data in a form of documents.
* [ExpressJS](https://expressjs.com/): A server side Web Application framework for Node.js.
* [ReactJS](https://reactjs.org/): A frontend framework developed by Facebook. It is based on JavaScript, used to build User Interface (UI) components.
* [Node.js](https://nodejs.org/en/): A JavaScript runtime environment. It is used to run JavaScript on a machine rather than in a browser.

![image](https://user-images.githubusercontent.com/22638955/132836227-0f6c15e2-784d-418e-acac-d18d19592b57.png)

A user interacts with the ReactJS UI components at the application front-end residing in the browser. This frontend is served by the application backend residing in a server, through ExpressJS running on top of NodeJS.

Any interaction that causes a data change request is sent to the NodeJS based Express server, which grabs data from the MongoDB database if required, and returns the data to the frontend of the application, which is then presented to the user.

## STEP 1 - SETTING UP OUR AWS INSTANCES

Launch a new EC2 instance of the t2.micro family with the Ubuntu Server 20.04 LTS (HVM) AMI under the free tier, and then SSH into the instance.

## STEP 2 – BACKEND CONFIGURATION

Update ubuntu
```
sudo apt update
```
Upgrade ubuntu
```
sudo apt upgrade
```
Lets get the location of Node.js software from Ubuntu repositories.
```
curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
```
Install Node.js with the command below
```
sudo apt-get install -y nodejs
```
The command above installs both `nodejs` and `npm`. NPM is a package manager for Node like `apt` for Ubuntu, it is used to install Node modules & packages and to manage dependency conflicts.

Verify the node installation with the command below - 
```
node -v
```

Verify the node installation with the command below
```
npm -v 
```
![image](https://user-images.githubusercontent.com/22638955/132845933-51dfed1f-cef3-4015-bbb9-7351f447220a.png)

Create a new directory for your To-Do project:
```
mkdir Todo
```
Run the command below to verify that the Todo directory is created with ls command
![image](https://user-images.githubusercontent.com/22638955/132888031-9dd8fb77-f095-4aff-bf26-236c2d39189e.png)

`cd` into the `Todo` directory
```
cd Todo
```

We will use the command `npm init` to initialise our project, so that a new file named `package.json` will be created. This file will normally contain information about our application and the dependencies that it needs to run. We would follow the prompts after running the command. You can press `Enter` several times to accept default values, then accept to write out the `package.json` file by typing `yes`.
```
npm init
```
![image](https://user-images.githubusercontent.com/22638955/132889103-19384da6-5dcd-4da4-a5c0-37883134ceea.png)

Run the command `ls` to confirm that we have `package.json` file created.

## STEP 3 INSTALL EXPRESSJS

To use express, we have to install it using npm -
```
npm install express
```
Install the dotenv module
```
npm install dotenv
```
Open the index.js file with the command below
```
vi index.js
```

Type the code below into it and save - 

```
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

In the same directory as the index.js file, type - 
```
node index.js
```
If every thing goes well, we should see **Server running on port 5000** in the terminal.

![image](https://user-images.githubusercontent.com/22638955/133156945-b9b90c48-16d2-4bb4-814c-6032172e839e.png)

We would need to open **port 5000** in our security group.

Open up your browser and try to access the server’s Public IP or Public DNS name followed by port 5000 (Ensure that node is running in your terminal) - 

![image](https://user-images.githubusercontent.com/22638955/133157324-5308bd44-4adc-4b8a-88f3-15c3a38f05b8.png)

## STEP 4 SETUP ROUTES
For every task our Todo app would do, we need to create `routes` that will define various endpoints that the `To-do` app will depend on. Our Todo app will be carrying out the following actions - 
* Create a new task
* Display a list of all tasks
* Delete a completed task

Create a `routes` folder
```
mkdir routes
cd routes
touch api.js
```
Paste the below code into the `api.js` file - 
```
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
![image](https://user-images.githubusercontent.com/22638955/133169891-1ac90167-3eeb-42c1-a95a-0ce61cc906b4.png)

## STEP 5 SETUP MODELS
Since the app is going to make use of Mongodb which is a NoSQL database, we need to create a model. A model is at the heart of JavaScript based applications, and it is what makes it interactive. We will also use models to define the database schema . This is important so that we will be able to define the fields stored in each Mongodb document.

To create a Schema and a model, we would need to install `mongoose` which is a Node.js package that makes working with mongodb easier.
We would create a models folder in our Todo directory, in our models folder we would create a file called `todo.js` - 
```
npm install mongoose
mkdir models && cd models && touch todo.js
```
Paste the following code into `todo.js`
```
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
We now have to update our routes from the file `api.js` in ‘routes’ directory to make use of the new model.
Open up the `api.js` file amd paste in the below code - 
```
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

## STEP 6 SETUP MONGODB
We need a database where we will store our data. For this we will make use of `mLab`. mLab provides a MongoDB database as a service solution (DBaaS), so to make life easy, we will need to sign up for a shared cluster free account, which is ideal for our use case. [Sign up here](https://www.mongodb.com/atlas-signup-from-mlab). Follow the sign up process, select AWS as the cloud provider, and choose a region near you.

Complete a get started checklist and allow access to the MongoDB database from anywhere (Not secure, but it is ideal for testing)

Create a MongoDB database and collection inside mLab

In the `index.js` file that was created previously, we specified `process.env` to access environment variables, but we have not created this file so we need to do that now.
```
vi .env
```
Add the connection string into it to access the database in it. To get the connection string, click on clusters -> connect -> connect your application -> make sure the driver is `node.js` -> copy the coonection string

Update the `index.js` file to reflect the use of the `.env` file so that `node.js` can connect to the DB.
```
vi index.js
**press escape**
:%d
```
Paste the below code into the now empty file - 
```
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

Using environment variables to store information is considered more secure and best practice to separate configuration and secret data from the application, instead of writing connection strings directly inside the `index.js` application file.

Start the server using the below command - 
```
node index.js
```

![image](https://user-images.githubusercontent.com/22638955/133348257-b8bf83d7-1013-4707-9b8b-a479ef8d68e1.png)

If you see a message like what we have in the screenshot above, then the backend has been configured successfully.


## STEP 7 TESTING BACKEND CODE WITHOUT FRONTEND USING RESTFUL API

Open Postman, create a POST request to the API `http://<PublicIP-or-PublicDNS>:5000/api/todos`. This request sends a new task to the To-Do list so the application can store it in the database. **NB**: Make sure you set the header key `content-type` as `application/json`

![image](https://user-images.githubusercontent.com/22638955/133348751-4a1224e8-e417-447d-8c60-dd721f6c40e5.png)

![image](https://user-images.githubusercontent.com/22638955/133348896-4af29884-735a-404d-8243-53e71cf8c2c8.png)

From the above, we can see that the `post` request was successful as it showed up at the bottom of the page and an ID was also generated for the `post` request. All records that were posted now exist in our Todo application.

Create a GET request to the API on `http://<PublicIP-or-PublicDNS>:5000/api/todos`. This request retrieves all existing records from the To-do application (backend requests these records from the database and sends it us back as a response to GET request).

![image](https://user-images.githubusercontent.com/22638955/133349228-d57e3d43-5200-4be2-a723-51efb9acc600.png)

## STEP 8 FRONTEND CREATION

In the `todo` directory, run -
```
npx create-react-app client
```

This will create a new folder in the `Todo` directory called `client`, where we will add all the react code.

Before testing the react app, we would have to install some dependencies. These dependencies are `concurrently` and `nodemon`.
`Concurrently` helps us to run more than one command simultaneously from the same terminal window, and `nodemon` is used to run and monitor our server. If there is any change in the server code, nodemon would restart it automatically and load up the new changes.
```
npm install concurrently --save-dev
npm install nodemon --save-dev
```

In the `Todo` folder, look for the code below and swap it with the other code below too - 

```
swap 
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
with
"scripts": {
"start": "node index.js",
"start-watch": "nodemon index.js",
"dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
},
```

We now have to configure proxy in `package.json`
```
cd client 
vi package.json
```

Add the key value pair below into the `package.json` file
```
"proxy": "http://localhost:5000"
```
The purpose of adding the proxy configuration is to make it possible to access the application directly from the browser by simply calling the server url like `http://localhost:5000` rather than always including the entire path like `http://localhost:5000/api/todos`

CD back into the `Todo` directory and run the command below - 
```
npm run dev
```
