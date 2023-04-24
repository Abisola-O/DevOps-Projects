# WEB STACK IMPLEMENTATION IN AWS

## MERN STACK
In this project, we will be implementing a web solution based on MERN stack in AWS Cloud.

### MAERN Web stack consists of the following compenents:

- MongoDB
- ExpressJS
- ReactJS
- Node.js

## Prerequisites 

To do this project, we need the following: 

- An AWS Account 
- Basic understanding of Linux (chown & chmod commands)
- Basic text editing skills in vi

### STEP 1 - LAUNCH A NEW EC2 INSTANCE 

- Launch a new EC2 Instance with t2 micro family and with Ubuntu Server 20.04 LTS
- Create a new keypair - *It is important to save your private key (.pem file) securely*
- ensure you allow ssh traffic into your instance from **Anywhere**

#### Connect to your EC2 Instance (MAC/Linux)

- open your terminal and cd into the directory where your .pem file is saved.
- to avoid a "bad permission" error, change the permissions for the private key file by running:

`sudo chmod 0400 <private-key-name>.pem`

- connect to your instance by running:

`ssh -i <private-key-name>.pem ubuntu@<Public-IP-address>`

Great! We've just launched our instance and connected to it.

## BACKEND CONFIGURATION
### STEP 2 - INSTALLING NODE.JS

- firstly, we need to update and upgrade ubuntu by running the following commands:

`sudo apt update`

`sudo apt upgrade`

- Now, let's get the location of Node.js software from Ubuntu repositories. Run:

`curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -`

![Alt text](images/location%20of%20nodejs.png)

- Install Node.js and npm with the following command:

`sudo apt-get install -y nodejs`

*Note - The command above installs both nodejs and npm.*

*NPM is a package manager for Node like apt for Ubuntu, it is used to install Node modules & packages and to manage dependency conflicts.*

- Verify the package installations by running `node -v` and `npm -v`

![Alt text](images/node-v.png)

##### Nowe we can proceed to our Application Code Setup

- Create a new directory for the To-Do project: `mkdir Todo`

- Run `ls` to confirm that the Todo directory has been created. 

![Alt text](images/ls.png)

- Change your current directory to the newly created directory: `cd Todo`

- Next, we will need to initialise our project, so that a new fiile 'package.json' will be created. *Follow the prompts after running the command. You can press Enter several times to accept default values, then accept to write out the package.json file by typing yes.*

`npm init`

![Alt text](images/npm%20init.png)

![Alt text](images/npm%20init%202.png)


### STEP 3 - INSTALLING EXPRESSJS

- Install expressjs by using npm: 

`npm install express`

![Alt text](images/install%20express.png)

- Now create a file index.js: `touch index.js`

- Install the *dotenv* module by running:

`npm install dotenv`

![Alt text](images/install%20dotenv.png)

- Open the index.js file: `vi index.js`

- Paste the code below into it and save: 

```
const express = require('express');
require('dotenv').config();

const app = express();

const port = process.env.PORT || 5000;

app.use((req, res, next) => {
res.header("Access-Control-Allow-Origin", "\*"); res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
next();
});

app.use((req, res, next) => {
res.send('Welcome to Express');
});

app.listen(port, () => {
console.log(`Server running on port ${port}`)
});
````

*Note - We have specified to use port 5000 in the code as this will be required later when we go on the browser*

- Now it is time to start our server to see if it works. Open your terminal in the same directory as your index.js file and type:

`node indexjs`

*if everything goes well, you should see Server running on port 5000 in your terminal*

![Alt text](images/node%20indexjs.png)

- Now we need to go back to our AWS Console and open port 5000 in our EC2 Security Groups. 

*Ensure your inbound rules look like this:*

![Alt text](images/inbound%20rules.png)

- Open your browser and try to access your server's Public IP or Public DNS name followed by port 5000

`http://<PublicIP-or-PublicDNS>:5000`

*You should see this:*

![Alt text](images/welcome.png)

Quick reminder how to get your server’s Public IP and public DNS name:
- You can find it in your AWS web console in EC2 details
- Run `curl -s http://169.254.169.254/latest/meta-data/public-ipv4` for Public IP address or `curl -s http://169.254.169.254/latest/meta-data/public-hostname` for Public DNS name.

### STEP 3 - ROUTES AND MODELS

#### Routes 

There are three actions that our To-Do application needs to be able to do:

- Create a new task
- Display list of all tasks
- Delete a completed task

*Each task will be associated with some particular endpoint and will use different standard HTTP request methods: POST, GET, DELETE.
For each task, we need to createroutesthat will define various endpoints that the To-do app will depend on.*

- Create a folder called routes: `mkdir routes`

- Change directory to routes folder: `cd routes`

- Create a file called api.js: `touch api.js`

- Open the file with the following command: `vi api.js`

- Copy the code below into the api.js file:

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

#### Models

*Since the app is going to make use of Mongodb which is a NoSQL database, we need to create a model.*

*A model is at the heart of JavaScript based applications, and it is what makes it interactive.*

*We will also use models to define the database schema. This is important so that we will be able to define the fields stored in each Mongodb document.*

*Schema is a blueprint of how the database will be constructed, including other data fields that may not be required to be stored in the database. These are known as **virtual properties**.*

*To create a Schema and a model, install mongoose which is a Node.js package that makes working with mongodb easier.*

- Change the directory back to the Todo folder with `cd ..` and install Mongoose by running:

`npm install mongoose`

![Alt text](images/install%20mongoose.png)

- Create a new folder: `mkdir models`

- Change directory into the newly created folder with `cd models`

- Inside the models folder, create a file and name it todo.js `touch todo.js`

*Tip - All three commands above can be defined in one line to be executed consequently with help of && operator `mkdir models && cd models && touch todo.js`*

- Open the file created with `vi todo.js` and paste the following lines of code in the file:

```
const mongoose = require('mongoose');
const Schema = mongoose.Schema;

//create schema for todo
const TodoSchema = new Schema({
action: {
type: String,
required: [true, 'The todo text field is required']
}
})

//create model for todo
const Todo = mongoose.model('todo', TodoSchema);

module.exports = Todo;
```

*Now we need to update our routes from the file api.js in the routes directory to make use of the new model.*

- In Routes directory, open api.js with `vi api.js`

- Delete the code inside the fie with `:%d` and paste the following lines of code into it: 

```
const express = require ('express');
const router = express.Router();
const Todo = require('../models/todo');

router.get('/todos', (req, res, next) => {

//this will return all the data, exposing only the id and action field to the client
Todo.find({}, 'action')
.then(data => res.json(data))
.catch(next)
});

router.post('/todos', (req, res, next) => {
if(req.body.action){
Todo.create(req.body)
.then(data => res.json(data))
.catch(next)
}else {
res.json({
error: "The input field is empty"
})
} });

router.delete('/todos/:id', (req, res, next) => {
Todo.findOneAndDelete({"_id": req.params.id})
.then(data => res.json(data))
.catch(next)
})

module.exports = router;
```

- Save and exit the file. 

### STEP 4 - MONGODB DATABASE 