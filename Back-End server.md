Express ,REST , Express.Router
[[Module1]]

Express Generator, MongoDB, Mongoose ODM
[[Module2]]




---
### Quick Start Express with MongoDB :

#### Install Express require packages :
- Express Server Framework :
	```Shell
	npm install express@4.16.3 --save
	```
- Morgan : logger module
	```shell
	npm install morgan@1.9.0 --save
	```
- Body Parser : 
	```shell
	npm install body-parser@1.18.3 --save
	```
- Express Generator : generates skeleton of express server.
	 ```shell
	 npm install -g express-generator
	 ```

#### Create Express server
You can run the application generator with the `npx` command (available in Node.js 8.2.0).

```console
$ npx express-generator
```

For earlier Node versions, install the application generator as a global npm package and then launch it:

```console
$ npm install -g express-generator
```

Create Project :
```console
express <my-app>
```

####  Install MongoDB Modules and Connect Database
>>Install package :
```console
npm install mongoose@5.1.7 mongoose-currency@0.2.0 --save

// mongoose help connect to mongoDB and Easy query handling servicecs
// mongoose-currency to save surrency
```

>>connect to mongoDB Database.
```javaScript
const mongoose = require('mongoose');

const url = 'mongodb://localhost:27017/consFusion';
const connect = mongoose.connect(url);

connect.then((db)=>{
	console.log('Connected successfully to server.');
	
},err =>{console.log(err);});
```

>> Create Data Schema : name schema
```javaScript
const mongoose = require('mongoose');
const Schema = mongoose.Schema;

var commentSchema = new Schema({
    rating:  {
        type: Number,
        min: 1,
        max: 5,
        required: true
    },
    comment:  {
        type: String,
        required: true
    },
    author:  {
        type: String,
        required: true
    }
}, {
    timestamps: true
});

var dishSchema = new Schema({
    name: {
        type: String,
        required: true,
        unique: true
    },
    description: {
        type: String,
        required: true
    },
    comments:[commentSchema]
}, {
    timestamps: true
});

var Dishes = mongoose.model('Dish',dishSchema);

module.exports = Dishes;
```

#### Ceate RestAPI Express_miniApp using Express Router

```javaScript
const express = require('express');
const bodyParser = require('body-parser');
const mongoose = require('mongoose');

const Dishes = require('../models/dishes');

const dishRouter = express.Router();

dishRouter.use(bodyParser.json());

dishRouter.route('/')
.get((req,res,next) => {
    Dishes.find({})
    .then((dishes) => {
        res.statusCode = 200;
        res.setHeader('Content-Type', 'application/json');
        res.json(dishes);
    }, (err) => next(err))
    .catch((err) => next(err));
})
.post((req, res, next) => {
    Dishes.create(req.body)
    .then((dish) => {
        console.log('Dish Created ', dish);
        res.statusCode = 200;
        res.setHeader('Content-Type', 'application/json');
        res.json(dish);
    }, (err) => next(err))
    .catch((err) => next(err));
})
.put((req, res, next) => {
    res.statusCode = 403;
    res.end('PUT operation not supported on /dishes');
})
.delete((req, res, next) => {
    Dishes.remove({})
    .then((resp) => {
        res.statusCode = 200;
        res.setHeader('Content-Type', 'application/json');
        res.json(resp);
    }, (err) => next(err))
    .catch((err) => next(err));    
});

dishRouter.route('/:dishId')
.get((req,res,next) => {
    Dishes.findById(req.params.dishId)
    .then((dish) => {
        res.statusCode = 200;
        res.setHeader('Content-Type', 'application/json');
        res.json(dish);
    }, (err) => next(err))
    .catch((err) => next(err));
})
.post((req, res, next) => {
    res.statusCode = 403;
    res.end('POST operation not supported on /dishes/'+ req.params.dishId);
})
.put((req, res, next) => {
    Dishes.findByIdAndUpdate(req.params.dishId, {
        $set: req.body
    }, { new: true })
    .then((dish) => {
        res.statusCode = 200;
        res.setHeader('Content-Type', 'application/json');
        res.json(dish);
    }, (err) => next(err))
    .catch((err) => next(err));
})
.delete((req, res, next) => {
    Dishes.findByIdAndRemove(req.params.dishId)
    .then((resp) => {
        res.statusCode = 200;
        res.setHeader('Content-Type', 'application/json');
        res.json(resp);
    }, (err) => next(err))
    .catch((err) => next(err));
});

```

#### Handle Authentication
