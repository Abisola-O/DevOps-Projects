# WEB STACK IMPLEMENTATION IN AWS

## LEMP STACK 

In this Project, we will be implementing Linux, Nginx, MySQL, and PHP (LEMP) in seven easy steps. 

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

### STEP 1 - INSTALLING THE NGINX WEB SERVER

### STEP 2 - INSTALLING MYSQL

### STEP 3 - INSTALLING PHP

### STEP 4 - CONFIGURING NGINX TO USE PHP PROCESSOR

### STEP 5 - TESTING PHP WITH NGINX

### STEP 6 - RETRIEVING DATA FROM MYSQL DATABASE WITH PHP