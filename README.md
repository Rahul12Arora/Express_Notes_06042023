# Express_Notes_06042023
Express Notes

<h2>Get started</h2>

```
//node should be pre installed
npm init
npm i express
npm install --save-dev nodemon
npm i dotenv

------------------------------
hoow to use
const dotenv = require('dotenv').config()
const port = process.env.PORT;
------------------------------
//add this too
"scripts": {
    "devtest": "nodemon index.js",            //npm run devtest
    "test": "echo \"Error: no test specified\" && exit 1"
  },

//run the file
npm run devtest
```

<h3>Start express server</h3>

```
const express =  require('express');
const app = express();

app.get('/', (req, res) => {
    res.sendStatus(200);
    res.send('ching chong');
----------------------------------------------
    res.status(200).send('ching chong');
----------------------------------------------
    res.status(200).json({message:"chong ching")}
    res.json() sends a default success code as we eliminated the status request
----------------------------------------------
    res.download('index.js'); //can be used to send data to user that he can download, provide file path as argument
})

app.listen(3000);
```

**NOTE- JSON.stringify() && JSON.parse(), convert from obj=>json string & from json string=>obj**

```
npm i ejs//run in terminal to install view engine to facilitate rendering of html from server

app.set('view engine', 'ejs')  //outside app.get()

app.render('script') // by default create a folder views & have .ejs files in it(you dont have to specify path then)
```
**Looks like this**
```
const express =  require('express');
const app = express();

app.set('view engine', 'ejs')

app.get('/', (req, res) => {
    res.render('script', {text: "world"})
})

app.listen(3000);

-------------------------------------------------------
in script.ejs use like this
<body>
    hello <%= locals.text || 'default' %>   //locals is an object, now if text is passed in express file it will be rendered & if it's not present 'default' will be
</body>
```

<h2>Middleware</h2>
Note-
<ul>
<li>
app.use() is an Express method used to mount middleware functions at a specified path. Middleware functions are functions that have access to the req (request) and res (response) objects, and can perform operations on them, modify them, or pass them on to the next middleware function in the stack.
</li>
<li>const express = require('express');
const router = express.Router();</li>
</ul>

 
<h2>Express Routing</h2>

```
const express =  require('express');
const app = express();

app.set('view engine', 'ejs')

const userRouter = require('./Routes/users');
app.use('/users',userRouter) //first argument is common path, i.e Any url that starts with /users add all of these routes to the end of it
app.listen(3000);
-----------------------------------------------------------------------------------------------------------------------------------------------------

//user file in routes folder

const express = require('express');
const router = express.Router();

router.get('/', (req,res)=>{
    res.send('user home');
})
router.get('/new', (req,res)=>{
    res.send('new user')
})
router.get('/data', (req,res)=>{
    res.send('user data')
})
--------------------------------------------------------
router.post('/', (req,res)=>{
    res.send('user home post data');
})
--------------------------------------------------------
router.get('/:id', (req,res)=>{         //any paramter after colon is dynamic & we can access it 
    
    res.send(`get user with id ${req.params.id}`);
})

module.exports = router;

// we did not use the path /user, /user/new & /user/data here because router understands that it's common & there's no need to repeat it.
```

<h3>Chaining get,post & other methods for a url in express</h3>

```
router.route('/:id').get((req,res)=>{
    res.send(`get user with id ${req.params.id}`);
}).post((req,res)=>{
    res.send(`post user with id ${req.params.id}`);
}).delete((req,res)=>{
    res.send(`delete user with id ${req.params.id}`);
})

Above code is replacement for code below
----------------------------------------------------------

router.get('/:id', (req,res)=>{
    
    res.send(`get user with id ${req.params.id}`);
})
router.post('/:id', (req,res)=>{
    
    res.send(`post user with id ${req.params.id}`);
})
router.delete('/:id', (req,res)=>{
    
    res.send(`delete user with id ${req.params.id}`);
})
```

<h3>Middleware runs before rest of the code in the file</h3>

```
router.route('/:id').get((req,res)=>{
    console.log(req.user)                   //used here to console log
    res.send(`get user with id ${req.params.id}`);
}).post((req,res)=>{
    res.send(`post user with id ${req.params.id}`);
}).delete((req,res)=>{
    res.send(`delete user with id ${req.params.id}`);
})


const users = [{name:'user1'}, {name:"user2"}]
-------------------------------------------------------------
router.param("id", (req,res,next,id)=>{
    req.user = users[id];                   // we added a property to the req object
    next();                                 //next() wil allow the application to proceed further
})
```

<h3>Custom Middleware</h3>

<li>
app.use() is an Express method used to mount middleware functions at a specified path. Middleware functions are functions that have access to the req (request) and res (response) objects, and can perform operations on them, modify them, or pass them on to the next middleware function in the stack.
</li>

```
app.use(logger)

function logger(req,res,next){
    console.log(req.originalUrl);
    next();
}
----------------------------------------

router.get('/', logger, (req,res)=>{
    res.send('user home');
})
```

<h3>Middleware for static page</h3>

```
//public folder m jo .html file h that will be rendered when this is called
app.use(express.static("public"))
```

<h4>Form submission(Not middleware related)</h4>

```
<!DOCTYPE html>
<html>
  <head>
    <title>My Form</title>
  </head>
  <body>
    <h1>My Form</h1>
    <form method="POST" action="/users">
      <label>
        Name:
        <input type="text" value=<% locals.name%> />
      </label><br />
      <button type="submit">Submit</button>
    </form>
  </body>
</html>
```

<h3>URL encoded Middleware</h3>
**Note**
<ul>
<li>
The express.urlencoded middleware parses incoming data and converts it into an object that can be accessed using req.body. The extended option of this middleware allows parsing of nested objects and arrays.
</li>
<li>express.urlencoded({ extended: false }) is a middleware function in the Express framework used to parse incoming data from HTML forms in the request body. It reads the form data from the request body, and then adds the parsed data to the req object as req.body.</li>
<li>It can be used to parse URL encoded data as well- URL-encoded data is a way of encoding key-value pairs, where each key-value pair is separated by an & symbol and the key and value are separated by an = symbol. This format is commonly used in HTML form submissions, but it can also be used in other contexts.</li>
</ul>


```
app.use(express.urlencoded({ extended: false }));

app.post('/login', (req, res) => {
  console.log(req.body.username);
  console.log(req.body.password);
  res.send('Login Successful!');
});
```

```
key1=value1&key2=value2
-------------------------------------------------------------------
app.post('/api/data', (req, res) => {
  const key1 = req.body.key1;
  const key2 = req.body.key2;
  console.log(key1, key2);
  res.send('Received data: ' + key1 + ', ' + key2);
});
```

<h3>multer() MiddlewareHow to handle formdata</h3>

**Note**
<ul>
<li>app.use(multer().single('photo')) is a middleware function in Express that handles file uploads and attaches the uploaded file to the req object as req.file.
The 'photo' parameter specifies the name of the field that the file should be attached to in the form data.</li>
</ul>

<h4>HTML</h4>

```
<form id="myForm" enctype="multipart/form-data">
  <input type="text" name="name" />
  <input type="file" name="photo" />
  <button type="submit">Submit</button>
</form>
```
<h4>JS</h4>

```
const form = document.getElementById('myForm');

form.addEventListener('submit', async (e) => {
  e.preventDefault();
  const formData = new FormData(form);
  
  const response = await fetch('/upload', {
      method: 'POST',
      body: formData
    });
    
    console.log(await response.json());
});
```

<h4>Express</h4>

```
const express = require('express');
const multer = require('multer');
const app = express();

app.use(express.json());
app.use(express.urlencoded({ extended: false }));
// It is a middleware function that parses incoming URL-encoded data (such as form data) and adds it to the req.body object.
The { extended: false } option tells the middleware function to use the querystring library to parse the data, and to not parse nested objects.


app.use(multer().single('photo'));

app.post('/upload', (req, res) => {
  const { name } = req.body;
  const { filename, mimetype, size } = req.file;
  
  res.json({
    name,
    photo: {
      filename,
      mimetype,
      size
    }
  });
});

app.listen(5000, () => {
  console.log('Server started');
});
```

<h2>Text middleware & JSON middleware</h2>


Note
<ul>
<li>the middleware functions express.json(), express.text() express.urlencoded() have been integrated into the core of Express. These middleware functions can handle JSON and URL-encoded data without the need for an external middleware like body-parser.<li>
</ul>

<h3>express.json()</h3>

<ul>
<li>express.json() is a middleware function that is used to parse incoming JSON data from the request body. It reads the JSON data from the request body, parses it, and then adds the parsed JSON data to the req object as req.body.</li>

<li>res.json() is a method that is used to send JSON data in the response body. It takes an object or an array as an argument, and sends it as a JSON string in the response body with the appropriate Content-Type header.</li>
</ul>

```
app.use(express.json());                    //DATA RECIEVE KARTA H AUR REQUEST BODY M DAAL DETA H

app.post('/', (req, res) => {
  const { name, email } = req.body;
  const newUser = {
    id: Math.random().toString(),
    name,
    email
  };
  res.send(newUser)                        //JSON M CONVERT KARKE BHEJ DETA H
//res.send(JSON.Stringify(newUser))        //MANUALLY AISE KARTE H
//res.send({"id1": id1})                   //This also works
```

<h3>express.text()</h3>

<ul>
<li>express.text() is a middleware function in the Express framework used to parse incoming plain text data from the request body. It reads the text data from the request body, and then adds the parsed text data to the req object as req.body.</li>
</ul>

```
app.use(express.text());                   //INCOMING TEXT DATA KO BODY SE UTHA KE REQ.BODY M ADD KARDIYA

app.post('/text', (req, res) => {
  const data = req.body;                  // YAHAN PE USE USE KAR LIYA
  res.send(`Received: ${data}`);                 //recieving this - "hello text data"
});
```

<h3>Express.static()</h3>
<ul>
<li>express.static() is a built-in middleware function in the Express framework for serving static files, such as HTML, CSS, JavaScript, and images. It is commonly used to serve client-side assets like web pages, stylesheets, and client-side scripts.</li>
</ul>

```
// Serve static files from the "public" directory
app.use(express.static('public'))
```

<h3>Image downloader Module</h3>

```
npm i image-downloader
const download = require('image-downloader');

const options = {
  url: 'http://someurl.com/image.jpg',
  dest: '/path/to/dest',               // will be saved to /path/to/dest/image.jpg
};

download.image(options)
  .then(({ filename }) => {
    console.log('Saved to', filename); // saved to /path/to/dest/image.jpg
  })
  .catch((err) => console.error(err));
-----------------------------------------------------------------------
USE CASE
NOTE - DEST BY DEFAULT NODE MODULES M HOTA H, YOU'LL HAVE TO GO BACK TWICE TO REACH ROOT FOLDER
const options = {
      url: image_url,
      dest: '../../loader',               // will be saved to /path/to/dest/image.jpg
    };
    console.log(options.dest);
    
    download.image(options)
      .then(({ filename }) => {
        console.log('Saved to', filename); // saved to /path/to/dest/image.jpg
      })
      .catch((err) => console.error(err));
```

<h1>Axios</h1>

**Axios is a popular JavaScript library for making HTTP requests. It provides an easy-to-use API that supports a wide range of request and response types. It works in both browser and Node.js environments and has built-in support for features like request and response interception, cancellation, and automatic handling of request and response data in various formats like JSON, XML, FormData, etc.**

<ul>

<li>Provides an easy-to-use API for making HTTP requests.</li>

<li>Supports request and response interception, allowing you to modify requests and responses.</li>

<li>Supports automatic handling of request and response data in various formats such as JSON, XML, FormData, etc.</li>

<li>Supports cancellation of requests.</li>

<li>Works in both browser and Node.js environments.</li>
</ul>

```
npm install axios

----------------------------------------------
GET REQUEST(lISTENING TO OUR OWN SERVER)

axios.get('/api/users')
  .then(response => {
    console.log(response.data);
  })
  .catch(error => {
    console.error(error);
  });

----------------------------------------------
POST REQUEST(LISTENING TO OUR OWN SERVER)

axios.post('/api/users', { name: 'John Doe', age: 30 })
  .then(response => {
    console.log(response.data);
  })
  .catch(error => {
    console.error(error);
  });

----------------------------------------------
UPLOADING FILES TO SERVER

Axios can also be used to upload files to a server. This can be useful in applications that allow users to upload files, such as a file-sharing app or a social media platform.
For example, consider an app that allows users to upload images to a server:</br>

const formData = new FormData();
formData.append('image', file);

axios.post('/api/upload', formData, {
  headers: {
    'Content-Type': 'multipart/form-data'
  }
})
.then(response => {
  console.log(response.data);
})
.catch(error => {
  console.error(error);
});

```

<h3>Axios => making HTTP requests to external APIs or services</h3>

1. Making API requests in a Node.js app</br>

Axios can be used in Node.js to make HTTP requests to external APIs or services. This can be useful in server-side applications that need to fetch data from external sources or make requests to other services.</br>


For example, consider a Node.js app that needs to fetch data from a remote API:</br>

```
const axios = require('axios');

axios.get('https://api.example.com/data')
  .then(response => {
    console.log(response.data);
  })
  .catch(error => {
    console.error(error);
  });

```

<h2>Listening to our own Server & making request to external ones with Axios</h2>

```
const express = require('express');
const axios = require('axios');

const app = express();

app.get('/data', (req, res) => {
  axios.get('https://api.example.com/data')
    .then(response => {
      res.send(response.data);
    })
    .catch(error => {
      console.error(error);
      res.status(500).send('Error fetching data');
    });
});

app.listen(3000, () => {
  console.log('Server started on port 3000');
});

```

<h2>More on Axios</h2>

<h3>Doing all Requests with AXIOS</h3>

```
// POST REQUEST
function addTodo() {
  axios
    .post('https://jsonplaceholder.typicode.com/todos', {
      title: 'New Todo',
      completed: false
    })
    .then(res => showOutput(res))
    .catch(err => console.error(err));
}

// PUT/PATCH REQUEST
function updateTodo() {
  axios
    .patch('https://jsonplaceholder.typicode.com/todos/1', {
      title: 'Updated Todo',
      completed: true
    })
    .then(res => showOutput(res))
    .catch(err => console.error(err));
}

// DELETE REQUEST
function removeTodo() {
  axios
    .delete('https://jsonplaceholder.typicode.com/todos/1')
    .then(res => showOutput(res))
    .catch(err => console.error(err));
}

// SIMULTANEOUS DATA
function getData() {
  axios
    .all([
      axios.get('https://jsonplaceholder.typicode.com/todos?_limit=5'),
      axios.get('https://jsonplaceholder.typicode.com/posts?_limit=5')
    ])
    .then(axios.spread((todos, posts) => showOutput(posts)))
    .catch(err => console.error(err));
}


// INTERCEPTING REQUESTS & RESPONSES
axios.interceptors.request.use(
  config => {
    console.log(
      `${config.method.toUpperCase()} request sent to ${
        config.url
      } at ${new Date().getTime()}`
    );

    return config;
  },
  error => {
    return Promise.reject(error);
  }
);


// CUSTOM HEADERS
function customHeaders() {
  const config = {
    headers: {
      'Content-Type': 'application/json',
      Authorization: 'sometoken'
    }
  };

  axios
    .post(
      'https://jsonplaceholder.typicode.com/todos',
      {
        title: 'New Todo',
        completed: false
      },
      config
    )
    .then(res => showOutput(res))
    .catch(err => console.error(err));
}


```
