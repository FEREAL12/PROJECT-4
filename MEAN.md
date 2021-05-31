# PROJECT-4
## MEAN STACK DEPLOYMENT TO UBUNTU IN AWS
### Setup an EC2 Instance with Ubuntu OS
### Connect to the Instance and always update and upgrade
```bash
sudo apt update -y
```
```bash
sudo apt upgrade -y
```
### Install Nodejs
```bash
sudo apt install -y nodejs
```
### Install MongoDB
```bash
sudo apt install -y mongodb
```
### start the server
```bash
sudo service mongodb start
```
### Verify that the Server is up and running
```bash
sudo systemctl status mongodb
```
### Install Node Package Manager
```bash
sudo apt install -y npm
```
### Install ‘body-parser package
```bash
sudo npm install body-parser
```
### Create a folder named ‘Books’
```bash
mkdir Books && cd Books
```
### In the Books directory, Initialize npm project
```bash
npm init
```
### Add a file to it named server.js
```bash
vi server.js
```
### Copy and paste the web server code below into the server.js file
```bash
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
## Installing Express and setting up routes
### To work with Mongoose package, a schema based solution for application data to store data of our book register
```bash
sudo npm install express mongoose
```
### In ‘Books’ folder, create a folder named apps
```bash
mkdir apps && cd apps
```
### Create a file named routes.js
```bash
vi routes.js
```
### Paste code below into routes.js
```bash
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
### In the ‘apps’ folder, create a folder named models
```bash
mkdir models && cd models
```

### Create a file named book.js
```bash
vi book.js
```
### Paste the code into book.js
```bash
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

### We wil need to access the routes with Angularjs. It helps with providiong dynamic view in our web application.

### Change the directory back to ‘Books’
```bash
cd ../..
```

### Create a folder named public
```bash
mkdir public && cd public
```
### Add a file named script.js
```bash
vi script.js
```
### paste the Code below (controller configuration defined) into the script.js file.
```bash
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
### In ‘public’ folder, create a file named index.html

```bash
vi index.html
```
### Cpoy and paste the code below into index.html file.
```bash
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

### Change the directory back up to ‘Books’
```bash
cd ..
```
### Start the server
```bash
node server.js
```
### launch a separate Putty or SSH console to test

### open TCP port 3300 in AWS Web Console for the EC2 Instance
### Error experienced while starting the server
![Error-project 4](https://user-images.githubusercontent.com/41236641/120170157-98878300-c1f8-11eb-967d-1fbf87983710.JPG)

### Trace directory and mv route.js routes.js
![resolution-project4](https://user-images.githubusercontent.com/41236641/120170476-e7cdb380-c1f8-11eb-93f1-9f92c4b279bf.JPG)

### Start the Server
```bash
node server.js
```
![project 4-mean](https://user-images.githubusercontent.com/41236641/120170730-2e231280-c1f9-11eb-9166-0cefe2d43866.JPG)
