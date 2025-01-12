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

install 
```javaScript
npm install -g nodemon

// Add in package.json file.
"scripts": {
    "start": "node index.js",
    "dev": "nodemon --exec 'npm start'"
}
```
`npm run dev`

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

const url = 'mongodb://localhost:27017/student';
const connect = mongoose.connect(url);

connect.then((db)=>{
	console.log('Connected successfully to server.');
	
},err =>{console.log(err);});
```

>> Create Data Schema : name schema
```javaScript
const mongoose = require('mongoose');
const Schema = mongoose.Schema;

const studentSchema = new Schema({
    firstName:{
        type:String,
        required:true
    },
    lastName:{
        type: String,
        required: true
    },
    phone:{
        type: Number,
        required:true
    },
        timestamps:true
    
});

var student = mongoose.model('Student',studentSchema);

module.exports = student;
```

#### Ceate RestAPI Express_miniApp using Express Router

```javaScript
const express = require('express');
const mongoose = require('mongoose');
const Student = require('../model/student');
const studentRouter = express.Router();

// Middleware to validate studentId
const validateStudentId = (req, res, next) => {
    if (!mongoose.Types.ObjectId.isValid(req.params.studentId)) {
        return res.status(400).json({ error: 'Invalid student ID format' });
    }
    next();
};

// Root route handlers
studentRouter.route('/')
    .get(async (req, res, next) => {
        try {
            const students = await Student.find({});
            res.status(200).json(students);
        } catch (err) {
            next(err);
        }
    })

    .post(async (req, res, next) => {
        try {
            if (!req.body || Object.keys(req.body).length === 0) {
                return res.status(400).json({ error: 'Request body cannot be empty' });
            }

            const student = await Student.create(req.body);
            res.status(201).json(student);  // 201 for resource creation
        } catch (err) {
            next(err);
        }
    })

    .put((req, res) => {
        res.status(405).json({ error: 'PUT operation not supported on /students' });
    })

    .delete(async (req, res, next) => {
        try {
            const result = await Student.deleteMany({});
            res.status(200).json({
                message: 'All students deleted successfully',
                deletedCount: result.deletedCount
            });
        } catch (err) {
            next(err);
        }
    });

// Student ID specific routes
studentRouter.route('/:studentId')
    .all(validateStudentId)  // Validate ID for all routes
    
    .get(async (req, res, next) => {
        try {
            const student = await Student.findById(req.params.studentId);
            if (!student) {
                return res.status(404).json({ error: 'Student not found' });
            }
            res.status(200).json(student);
        } catch (err) {
            next(err);
        }
    })

    .post((req, res) => {
        res.status(405).json({ error: 'POST operation not supported on /students/:studentId' });
    })

    .put(async (req, res, next) => {
        try {
            if (!req.body || Object.keys(req.body).length === 0) {
                return res.status(400).json({ error: 'Request body cannot be empty' });
            }

            const student = await Student.findByIdAndUpdate(
                req.params.studentId,
                { $set: req.body },
                { new: true, runValidators: true }
            );

            if (!student) {
                return res.status(404).json({ error: 'Student not found' });
            }

            res.status(200).json(student);
        } catch (err) {
            next(err);
        }
    })

    .delete(async (req, res, next) => {
        try {
            const deletedStudent = await Student.findByIdAndDelete(req.params.studentId);
            
            if (!deletedStudent) {
                return res.status(404).json({ error: 'Student not found' });
            }

            res.status(200).json({
                message: 'Student deleted successfully',
                deletedStudent
            });
        } catch (err) {
            next(err);
        }
    });

// Error handling middleware
studentRouter.use((err, req, res, next) => {
    console.error(err.stack);
    
    if (err instanceof mongoose.Error.ValidationError) {
        return res.status(400).json({ error: err.message });
    }
    
    if (err.name === 'CastError') {
        return res.status(400).json({ error: 'Invalid ID format' });
    }
    
    res.status(500).json({ error: 'Something went wrong!' });
});

module.exports = studentRouter;
```

#### Handle Authentication
