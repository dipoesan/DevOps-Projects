# MEAN STACK DEPLOYMENT TO UBUNTU IN AWS

In this project, we are going to implement a simple Book Register web form using the MEAN stack.

MEAN Stack is a combination of the following components:
1. MongoDB (Document database) – Stores and allows to retrieve data.
2. Express (Back-end application framework) – Makes requests to Database for Reads and Writes.
3. Angular (Front-end application framework) – Handles Client and Server Requests
4. Node.js (JavaScript runtime environment) – Accepts requests and displays results to end user

## STEP 1 SETTING UP OUR AWS INSTANCES
Launch a new EC2 instance of the `t2.micro` family with the Ubuntu Server 20.04 LTS (HVM) AMI under the free tier, and then SSH into the instance.

## STEP 2 Install NodeJs

Update ubuntu
```
sudo apt update
```
Upgrade ubuntu
```
sudo apt upgrade
```
Install NodeJS
```
sudo apt install -y nodejs
```

## Step 3 Install MongoDB

```
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
```
```
echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
```

To install MongoDB, we would run the command below, then start the server right after -
```
sudo apt install -y mongodb
sudo service mongodb start
```
Verify that the MongoDB service is running - 
```
sudo systemctl status mongodb
```
![image](https://user-images.githubusercontent.com/22638955/133694714-c72864cb-2b73-42cd-a0c2-5a335e3c40bd.png)

Install `npm` (Node Package Manager)
```
sudo apt install -y npm
```
We need ‘body-parser’ package to help us process JSON files passed in requests to the server. To install `body-parser`, run the command below - 
```
sudo npm install body-parser
```

Create a directory named `Books` and `cd` into it
```
mkdir Books && cd Books
```

In the Books directory, we would be initializing the npm project - 
```
npm init
```
![image](https://user-images.githubusercontent.com/22638955/133695518-5239c9d2-75b8-4c6c-8506-0e629368259b.png)

Inside the `Books` directory, create a file called `server.js` and copy the following code into it - 
```
var express = require('express');
var bodyParser = require('body-parser');
var app = express();
app.use(express.static(__dirname + '/public'));
app.use(bodyParser.json());
require('./apps/routes')(app);
app.set('port', 3300);
app.listen(app.get('port'), function() {
    console.log('Server up: http://localhost:' + app.get('port'));
});
```

## Step 4 Install Express and set up routes to the server

We will be using `Express` to pass book information to and from our MongoDB database. We will also be using the `Mongoose` packagewhich provides a straight-forward schema-based solution to model our application data. We will use `Mongoose` to to establish a schema for our database to store data of our book register.

