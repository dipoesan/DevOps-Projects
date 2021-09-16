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

```
sudo npm install express mongoose
```

In the `Books` directory, create a folder called `apps` - 
```
mkdir apps && cd apps
```

Create a file called `routes.js` and then paste the code below into the file - 

```
var Book = require('./models/book');
module.exports = function(app) {
  app.get('/book', function(req, res) {
    Book.find({}, function(err, result) {
      if ( err ) throw err;
      res.json(result);
    });
  }); 
  app.post('/book', function(req, res) {
    var book = new Book( {
      name:req.body.name,
      isbn:req.body.isbn,
      author:req.body.author,
      pages:req.body.pages
    });
    book.save(function(err, result) {
      if ( err ) throw err;
      res.json( {
        message:"Successfully added book",
        book:result
      });
    });
  });
  app.delete("/book/:isbn", function(req, res) {
    Book.findOneAndRemove(req.query, function(err, result) {
      if ( err ) throw err;
      res.json( {
        message: "Successfully deleted the book",
        book: result
      });
    });
  });
  var path = require('path');
  app.get('*', function(req, res) {
    res.sendfile(path.join(__dirname + '/public', 'index.html'));
  });
};
```
Within the apps directory, create another folder called `models`

Within `models`, create a file called `book.js` and paste the code below into it - 

```
var mongoose = require('mongoose');
var dbHost = 'mongodb://localhost:27017/test';
mongoose.connect(dbHost);
mongoose.connection;
mongoose.set('debug', true);
var bookSchema = mongoose.Schema( {
  name: String,
  isbn: {type: String, index: true},
  author: String,
  pages: Number
});
var Book = mongoose.model('Book', bookSchema);
module.exports = mongoose.model('Book', bookSchema);
```

## Step 5 Access the routes with **AngularJS**

We would be using `angularjs` to connect to our web page with `express` and then perform actions on our bookmm register.

Change the directory back to `Books` and then create a folder called `public` - 
```
cd ../..
mkdir public && cd public
```

Add a file called `script.js` and then paste the code below into it - 
```
var app = angular.module('myApp', []);
app.controller('myCtrl', function($scope, $http) {
  $http( {
    method: 'GET',
    url: '/book'
  }).then(function successCallback(response) {
    $scope.books = response.data;
  }, function errorCallback(response) {
    console.log('Error: ' + response);
  });
  $scope.del_book = function(book) {
    $http( {
      method: 'DELETE',
      url: '/book/:isbn',
      params: {'isbn': book.isbn}
    }).then(function successCallback(response) {
      console.log(response);
    }, function errorCallback(response) {
      console.log('Error: ' + response);
    });
  };
  $scope.add_book = function() {
    var body = '{ "name": "' + $scope.Name + 
    '", "isbn": "' + $scope.Isbn +
    '", "author": "' + $scope.Author + 
    '", "pages": "' + $scope.Pages + '" }';
    $http({
      method: 'POST',
      url: '/book',
      data: body
    }).then(function successCallback(response) {
      console.log(response);
    }, function errorCallback(response) {
      console.log('Error: ' + response);
    });
  };
});
```
Also create another file called `index.html` and then add the following code into it -
```
<!doctype html>
<html ng-app="myApp" ng-controller="myCtrl">
  <head>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
    <script src="script.js"></script>
  </head>
  <body>
    <div>
      <table>
        <tr>
          <td>Name:</td>
          <td><input type="text" ng-model="Name"></td>
        </tr>
        <tr>
          <td>Isbn:</td>
          <td><input type="text" ng-model="Isbn"></td>
        </tr>
        <tr>
          <td>Author:</td>
          <td><input type="text" ng-model="Author"></td>
        </tr>
        <tr>
          <td>Pages:</td>
          <td><input type="number" ng-model="Pages"></td>
        </tr>
      </table>
      <button ng-click="add_book()">Add</button>
    </div>
    <hr>
    <div>
      <table>
        <tr>
          <th>Name</th>
          <th>Isbn</th>
          <th>Author</th>
          <th>Pages</th>

        </tr>
        <tr ng-repeat="book in books">
          <td>{{book.name}}</td>
          <td>{{book.isbn}}</td>
          <td>{{book.author}}</td>
          <td>{{book.pages}}</td>

          <td><input type="button" value="Delete" data-ng-click="del_book(book)"></td>
        </tr>
      </table>
    </div>
  </body>
</html>
```

Change the directory back into `Books` and then start the server by running this command - 
```
node server.js
```
