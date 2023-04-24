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

