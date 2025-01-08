###  #Intro Welcome to Server-Side Development with NodeJS,Express and MongoDB


[[0-Course-Overview.pdf]]
[[How to Learn.pdf]]

### Full-Stack Web Development: The Big Picture
### Setting up your Development Evironment: Git
### Introduction to Node.js and NPM #Day-4 

In this lesson you will learn the basics of Node.js and NPM. Thereafter, you will install Node.js and NPM on your machine so that you can start writing simple Node applications. At the end of this lesson, you should be able to:

- Download and install Node.js and NPM on your machine
    
- Verify that the installation was successful and your machine is ready for using Node.js and NPM.

Slides : [[1-NodeJS-NPM.pdf]]

### Node Modules #Day-5 

1. Each File in Node is its own module.
2. The module variables give access to the current module definition in a file.
3. The module.exports variable determines the export form the current module.
4. The require function is used to import a module.

<font color="#ff0000">Types of Node module:</font>
- File-based module
- Core module
	- part of core node
	- Example: path, fs, os, util, ...
- External Node module:
	- Third-party module
	- Installed using NPM
	- node-modules folder in your node application.

Demo:
[rectangle.js] #export_module
```
exports.perimeter = (x,y) => (2*(x+y));
```
[index.js] #import_module
```
var rect = require('./rectangle');
```


To Create node Application
- Create Project folder
- Initialize using `npm init` to initialize project folder.
	-  it creates `package.json` file and save all information and dependencies

Node Modules: Callbacks and Error Handling:
[[2-Node-Modules.pdf]]
[[3-Node-Callbacks-Error-Handling.pdf]]

callbacks and Error Handling : Exercise :
rectangle.js
```javascript
module.exports = (x,y,callback)=>{
	if( x<=0 || y<=0){
		setTimeout(() =>
			callback(new Error("this is error"),null)
			,2000
		);	
	}else{
		setTimeout(()=>
			callback(null,{
				perimeter: () => (2*(x+y)),
				area: () => (x*y)
			}),2000
		);
	}
}
```
index.js
```javaScript
var rect = require('./rectangle');

function solveRect(l,b){
	console.log("Solution of rectangle");
	rect(l,b,(err,rectangle) =>{
		if(err){
			console.log("Error",err.message);
		}else{
			console.log("this area is : "+rectangle.area());
			console.log("this perimeter is : "+rectangle.perimeter());
		}
	});
	console.log("This statement after the call to rect().");
}
```
[Node Modules](https://nodejs.org/api/modules.html)
[The Node.js Event Loop, Timers, and process.nextTick()](https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick/)
[How much javaScript To start NodeJS](https://nodejs.org/en/learn/getting-started/how-much-javascript-do-you-need-to-know-to-use-nodejs)

### Node and HTTP
 ppt:
 [[4-Networking-Essentials.pdf]]
 [[5-Node-HTTP.pdf]]


VS code Extension:
postman
RapidAPI Client

Node-http Server:
`http` module has `createServer()` which takes `fun()` as a parameter. We pass `(req,res)` arrow function
```javascript
const http = require('http');

const hostname = 'localhost';
const port = 3000;

const server = http.createServer((req,res)=>{
    console.log("Request for "+req.url+' by method '+req.method);

    res.statusCode = 200;
    res.setHeader("Content-Type",'text/html');
    res.end('<html><body><h1>Hello,world!</h1></body></html>');
});

server.listen(port,hostname, () =>{
    console.log(`Server running at http://${hostname}:${port}`);
})

})
```
node server to serve static file.(index.html)
```javascript
const http = require('http');

const hostname = 'localhost';
const port = 3000;

const fs = require('fs');
const path = require('path');

const server = http.createServer((req, res) => {
    console.log('Request for ' + req.url + ' by method ' + req.method);
	// check req.method is get or not
    if (req.method == 'GET'){
      // checl if request url is root address then index.html
      var fileUrl;
      if(req.url == '/') fileUrl='/index.html';
      else fileUrl=req.url;

      var filePath = path.resolve('./public'+fileUrl); // check file path
      const fileExt = path.extname(filePath); // check file extension.
      
      if (fileExt == '.html'){
        fs.exists(filePath,(exists)=>{
          if(!exists){
            res.statusCode = 404;
            res.setHeader('Content-Type','text/html');
            res.end('<html><body><h1>Error 404: '+fileUrl+' not found</h1></body></html>');
            return;
          }
          res.statusCode = 200;
          res.setHeader('Content-Type','text/html');
          fs.createReadStream(filePath).pipe(res);
        })
      }
      else{
        res.statusCode = 404;
        res.setHeader('Content-Type','text/html');
        res.end('<hmtl><body<h1>Error 404: '+fileUrl+' not an HTML file</h1></body></html>');
        
        return;
      }

    }else{
      res.statusCode = 404;
      res.setHeader('Content-Type', 'text/html');
      res.end('<html><body><h1>ERROR 404: '+req.method+' not supported</h1></body></html>');
      return;
    }
     
  })

server.listen(port,hostname,() => {
    console.log(`Server running at http://${hostname}:${port}`);
})
```

### Introduction to Express.

[[6-Intro-Express.pdf]]
what is express
Fast, Unopinionated, minimalist web framework for nodejs ( from expressjs.com)
Express middleware:
- morgan for logging
	var morgan = require('morgan');
	app.use(morgan('dev'));
- Serving static web resources:
	app.use(express.static(_____dirname+'/public'));
	- Note:____filename and __dirname give you the path to the file and  directory for current module

Express Server, Serve static File and Morgan logger
```javascript
const express = require('express');
const http = require('http');
const morgan = require('morgan');

const hostname = 'localhost'
const port = 3000;

const app = express();
app.use(morgan('dev'));

app.use(express.static(__dirname+'/public')) // this serve public folder index.html as root page

app.use((req,res,next)=>{
    res.statusCode = 200;
    res.setHeader('Content-Type','text/html');
    res.end('<html><body><h1>This is Express Server.</h1></body></html>');
});

const server = http.createServer(app);

server.listen(port, hostname, ()=>{
    console.log(`Server running at http://${hostname}:${port}`);
});
```

Brief Representational State Transfer (REST):
[[7-REST.pdf]]
```javascript
const express = require('express');
const http = require('http');
const morgan = require('morgan');
const bodyParser = require('body-parser');

const hostname = 'localhost'
const port = 3000;

const app = express();
app.use(morgan('dev'));
app.use(bodyParser.json());

app.all('/dishes', (req,res,next) =>{
    res.statusCode = 200;
    res.setHeader('Content-Type','text/plain');
    next();
});

app.get('/dishes', (req,res,next)=>{
    res.end('Will send all the dishes to you!');
});

app.post('/dishes',(req,res,next) =>{
    res.end('Will add the dish:'+ req.body.name +' with details: '+ req.body.description);
});

app.put('/dishes', (req,res,next) =>{
    res.statusCode = 403;
    res.end('PUT operation not supported on /dishes');
});

app.delete('/dishes', (req,res,next)=>{
    res.end('Deleting all the dishes!');
});

app.get('/dishes/:dishId', (req,res,next)=>{
    res.end('Will send details of the dish: '+req.params.dishId+' to you!');
});

app.post('/dishes/:dishId',(req,res,next) =>{
    res.end('POST operation not supported on /dishes.'+ req.params.dishId);
});

app.put('/dishes/:dishId', (req,res,next) =>{
    res.write('Updating the dish: ' + req.params.dishId);
    res.end('Will update the dish: '+req.body.name+' with details: '+ req.body.description);
});

app.delete('/dishes/:dishId', (req,res,next)=>{
    res.end('Deleting dish: '+ req.params.dishId);
});

app.use(express.static(__dirname+'/public'))

app.use((req,res,next)=>{
    res.statusCode = 200;
    res.setHeader('Content-Type','text/html');
    res.end('<html><body><h1>This is Express Server.</h1></body></html>');
});

const server = http.createServer(app);

server.listen(port, hostname, ()=>{
    console.log(`Server running at http://${hostname}:${port}`);
});
```


#### Express Router
Express Router is ==a class in Express.js that helps organize routes and handlers==, and is often referred to as a "mini-app":

[[8-Express-Router.pdf]]
	Express Router creates a mini-Express application:
	`var dishRouter = express.Router();`
	`dishRouter.use(bodyParser.json());`

	dishRouter.route('/')
		.all(..);
		.get(..);
		...
 Create folder called routes and create file dishRouter.js 
 dishRouter.js
 ```javascript
 const express = require('express');
const bodyParser = require('body-parser');

const dishRouter = express.Router();

dishRouter.use(bodyParser.json());

dishRouter.route('/')
.all((req,res,next) =>{
    res.statusCode = 200;
    res.setHeader('Content-Type','text/plain');
    next();
})
.get((req,res,next)=>{
    res.end('Will send all the dishes to you!');
})
.post((req,res,next) =>{
    res.end('Will add the dish:'+ req.body.name +' with details: '+ req.body.description);
})
.put((req,res,next) =>{
    res.statusCode = 403;
    res.end('PUT operation not supported on /dishes');
})
.delete((req,res,next)=>{
    res.end('Deleting all the dishes!');
});

dishRouter.route('/:dishId')
.all((req,res,next) =>{
    res.statusCode = 200;
    res.setHeader('Content-Type','text/plain');
    next();
})
.get((req,res,next)=>{
    res.end('Will send details of the dish: '+req.params.dishId+' to you!');
})
.post((req,res,next) =>{
    res.end('POST operation not supported on /dishes.'+ req.params.dishId);
})
.put((req,res,next) =>{
    res.write('Updating the dish: ' + req.params.dishId);
    res.end('Will update the dish: '+req.body.name+' with details: '+ req.body.description);
})
.delete((req,res,next)=>{
    res.end('Deleting dish: '+ req.params.dishId);
});

module.exports = dishRouter;
```

index.js
```javascript
const dishRouter = require('./routes/dishRouter);


app.use('/dishes', dishRouter);

```

#### Express Resources
- [ExpressJS](http://expressjs.com/)
- [Connect](https://github.com/senchalabs/connect)
- [Express Wiki](https://github.com/expressjs/express/wiki)
- [morgan](https://github.com/expressjs/morgan)
- [body-parser](https://github.com/expressjs/body-parser)
#### Other Resources
- [Understanding Express.js](http://evanhahn.com/understanding-express/)
- [A short guide to Connect Middleware](https://stephensugden.com/middleware_guide/)