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




