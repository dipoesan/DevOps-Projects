# SIMPLE TO-DO APPLICATION ON MERN WEB STACK

The MERN web stack consists of the following components - 
* [MongoDB](https://www.mongodb.com/): A document-based, No-SQL database used to store application data in a form of documents.
* [ExpressJS](https://expressjs.com/): A server side Web Application framework for Node.js.
* [ReactJS](https://reactjs.org/): A frontend framework developed by Facebook. It is based on JavaScript, used to build User Interface (UI) components.
* [Node.js](https://nodejs.org/en/): A JavaScript runtime environment. It is used to run JavaScript on a machine rather than in a browser.

![image](https://user-images.githubusercontent.com/22638955/132836227-0f6c15e2-784d-418e-acac-d18d19592b57.png)

A user interacts with the ReactJS UI components at the application front-end residing in the browser. This frontend is served by the application backend residing in a server, through ExpressJS running on top of NodeJS.

Any interaction that causes a data change request is sent to the NodeJS based Express server, which grabs data from the MongoDB database if required, and returns the data to the frontend of the application, which is then presented to the user.

