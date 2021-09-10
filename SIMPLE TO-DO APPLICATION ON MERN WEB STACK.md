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

## STEP 1 – BACKEND CONFIGURATION

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

