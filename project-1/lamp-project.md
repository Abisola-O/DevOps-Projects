# WEB STACK IMPLEMENTATION IN AWS

## LAMP STACK 


In this Project, we will be implementing Linux, Apache, MySQL, and PHP (LAMP) in six easy steps. 

## Prerequisites 
- An AWS Account 
- Basic understanding of Linux (chown & chmod commands)
- basic text editing skills in vi

### Step 1 - Launch an EC2 Instance

- Launch a new EC2 Instance with t2 micro family and with Ubuntu Server 20.04 LTS

- Create a new keypair - * It is important to save your private key (.pem file) securely *

- ensure you allow ssh traffic into your instance from ** Anywhere ** 

## Instance picture 

###### Connect to your EC2 Instance (using MAC/Linux)

- open your terminal and cd into the directory where your .pem file is saved.
- to avoid a "bad permission" error, change the permissions for the private key file by running:

` sudo chmod 0400 <private-key-name>. pem `

- connect to your instance by running:

` ssh -i <private-key-name>. pem ubuntu@<Public-IP-address> `

Great! We've just launched our instance and connected to it, our set up looks like this now: 



### Step 2 - Installing Apache and updating the firewall

> Install Apache using Ubuntu’s package manager * apt *

- update a list of packages in package manage by running

` sudo apt update `

- run apache2 package installation 

` sudo apt install apache2 `

- To verify that apache2 is running as a Service in our OS, use following command

`sudo systemctl status apache2`

*if it is green and running, then you did everything correctly*

## Image 

## Step 3 - Installing MySQL

## Step 4 - Installing PHP 

## Step 5 - Creating a virtual host for our website using apache

## Step 6 - Enabling PHP on the website 


