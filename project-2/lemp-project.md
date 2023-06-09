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

![Alt text](images/connected%20instance.png)

Great! We've just launched our instance and connected to it.

### STEP 2 - INSTALLING THE NGINX WEB SERVER

We'll be using the *apt* package manager to install our Nginx package. 

- firstly, we'll start off by updating our server's package index with: `sudo apt update`
- Now we can install Nginx with: `sudo apt install nginx`
- When prompted, enter Y to confirm that you want to install Nginx

![Alt text](images/sudo%20install%20nginx.png)

Once the installation is finished, the Nginx web server will be active and running on your Ubuntu 20.04 server.

- To verify that nginx was successfully installed and is running as a service in Ubuntu, run:

`sudo systemctl status nginx`

If it is green and running, then you did everything correctly – you have just launched your first Web Server in the Clouds!

![Alt text](images/systemctl.png)

However, before we can receive any traffic by our Web Server, we need to open TCP port 80 which is default port that web browsers use to access web pages in the Internet.

- To do this, we need to go back to our AWS console and add a rule to our EC2 instance configuration to open inbound connection through port 80.

![Alt text](images/inbound%20rules.png)

Our server is running and we can access it locally and from the Internet

- First, let us try to check how we can access it locally in our Ubuntu shell, run:

```
curl http://localhost:80
or
curl http://127.0.0.1:80
```

Either of the command should output this: 

![Alt text](images/curl-80.png)

Now it is time for us to test how our Nginx server can respond to requests from the Internet.
- Open a web browser of your choice and input the following url:

`http://<Public-IP-Address>:80`

![Alt text](images/nginx%20welcome%20page.png)

*Another way to retrieve your Public IP address, other than to check it in AWS Web console, is to use following command:*

`curl -s http://169.254.169.254/latest/meta-data/public-ipv4`

![Alt text](images/curl-meta-data.png)

Great! Our web server is now correctly installed and accessible through our firewall.

### STEP 3 - INSTALLING MYSQL

Now that we have a web server up and running, we need to install a Database Management System (DBMS) to be able to store and manage data for our site in a relational database. 

MySQL is a popular relational database management system used within PHP environments, so we will use it in our project.

- We will use *apt* to acquire and install this software. Run : `sudo apt install mysql-server`

- When prompted, confirm installation by typing Y, and then hit ENTER.

![Alt text](images/sudo%20install.png)

- When the installation is finished, log in to the MySQL console by typing: `sudo mysql`

This will connect to the MySQL server as the administrative database user root, which is inferred by the use of sudo when running this command. 

You should see an output like this:

![Alt text](images/sudo%20mysql.png)

*It’s recommended that you run a security script that comes pre-installed with MySQL. This script will remove some insecure default settings and lock down access to your database system. Before running the script, you will set a password for the root user, using mysql_native_password as default authentication method. We’re defining this user’s password as PassWord.1.*

- Run: `ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';`

- Exit the MySQL shell with: `exit`

![Alt text](images/alter%20user.png)

- Start the interactive script by running: `sudo mysql_secure_installation`

This will ask if you want to configure the VALIDATE PASSWORD PLUGIN.

**Note:** *Enabling this feature is something of a judgment call. If enabled, passwords which don’t match the specified criteria will be rejected by MySQL with an error. It is safe to leave validation disabled, but you should always use strong, unique passwords for database credentials.*

- Answer Y for yes, or anything else to continue without enabling.

![Alt text](images/secure%20installation.png)

*If you answer “yes”, you’ll be asked to select a level of password validation.*

-  If you enabled password validation, you’ll be shown the password strength for the root password you just entered and your server will ask if you want to continue with that password. If you are happy with your current password, enter Y for “yes” at the prompt:

![Alt text](images/password%20validation.png)

- For the rest of the questions, press Y and hit the ENTER key at each prompt. This will prompt you to change the root password, remove some anonymous users and the test database, disable remote root logins. Load these new rules so that MySQL immediately respects the changes you have made.

![Alt text](images/reloading%20.png)

- When you’re finished, test if you’re able to log in to the MySQL console by typing: `sudo mysql -p`

- Exit the MySQL console

![Alt text](images/mysql-p.png)

Our MySQL server is now installed and secured. Next, we will install PHP, the final component in the LEMP stack.


### STEP 4 - INSTALLING PHP

We now have Nginx installed to serve our content and MySQL installed to store and manage our data. Now we can install PHP to process code and generate dynamic content for the web server.

We’ll need to install php-fpm, which stands for “PHP fastCGI process manager”, and tell Nginx to pass PHP requests to this software for processing. Additionally, we’ll need php-mysql - a PHP module that allows PHP to communicate with MySQL-based databases. Core PHP packages will automatically be installed as dependencies.

- To install these 2 packages at once, run:

`sudo apt install php-fpm php-mysql`

- When prompted, type Y and press ENTER to confirm installation.

![Alt text](images/install%20packages.png)

We now have our PHP components installed. Next, we will configure Nginx to use them.

### STEP 5 - CONFIGURING NGINX TO USE PHP PROCESSOR

*When using the Nginx web server, we can create server blocks (similar to virtual hosts in Apache) to encapsulate configuration details and host more than one domain on a single server. In this guide, we will use **project LEMP** as an example domain name.*

*On Ubuntu 20.04, Nginx has one server block enabled by default and is configured to serve documents out of a directory at /var/www/html. While this works well for a single site, it can become difficult to manage if you are hosting multiple sites. Instead of modifying /var/www/html, we’ll create a directory structure within /var/www for our domain website, leaving /var/www/html in place as the default directory to be served if a client request does not match any other sites.*



- Create the root web directory for your domain as follows:

    `sudo mkdir /var/www/projectLEMP`

- Assign ownership of the directory with the $USER environment variable, which will reference your current system user:

    `sudo chown -R $USER:$USER /var/www/projectLEMP`

- Now, open a new configuration file in Nginx’s sites-available directory using your preferred command-line editor. Here, we’ll use vi/vim:

    `sudo vi /etc/nginx/sites-available/projectLEMP`

- This will create a new blank file. Paste in the following bare-bones configuration:

```
#/etc/nginx/sites-available/projectLEMP
 
server {
	listen 80;
	server_name projectLEMP www.projectLEMP;
	root /var/www/projectLEMP;
 
	index index.html index.htm index.php;
 
	location / {
    	try_files $uri $uri/ =404;
	}
 
	location ~ \.php$ {
    	include snippets/fastcgi-php.conf;
    	fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
 	}
 
	location ~ /\.ht {
    	deny all;
	}
 
}
```

- Activate your configuration by linking to the config file from Nginx’s sites-enabled directory:

    `sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/`

- You can test your configuration for syntax errors by typing: `sudo nginx -t`

You should see the following messages: 

![Alt text](images/nginx%20-t.png)

- We also need to disable default Nginx host that is currently configured to listen on port 80, for this run:

    `sudo unlink /etc/nginx/sites-enabled/default`

-  Reload Nginx to apply the changes: `sudo systemctl reload nginx`

- Our new website is now active, but the web root /var/www/projectLEMP is still empty. Create an index.html file in that location so that we can test that the new server block works as expected:

    `sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html`

- Now go to your browser and try to open your website URL using IP address:

    `http://<Public-IP-Address>:80`

![Alt text](images/echo%20hello%20LEMP.png)

- You can also access your website in your browser by public DNS name, the result is the same: 

    `http://<Public-DNS-Name>:80`

![Alt text](images/public%20dns.png)

*If you see the text from ‘echo’ command you wrote to index.html file, then it means your Nginx site is working as expected.*

*We are going to leave this file in place as a temporary landing page for our application until we set up an index.php file to replace it*


### STEP 6 - TESTING PHP WITH NGINX

**Great! Our LEMP Stack is completely installed and fully operational**

We can test it to validate that Nginx can correctly hand *.php* files off to your PHP processor. We can do this by creating a test PHP file in our document root. 

- Open a new file called *info.php* within your document root in your text editor:

    `sudo vi /var/www/projectLEMP/info.php`

- Paste the following lines into the new file:

```
<?php
phpinfo();
```

We can now access this page in our web browser by visiting the domain name or public IP address we’ve set up in our Nginx configuration file, followed by /info.php:

`http://<server_domain_or_IP>/info.php`

You will see a web page containing detailed information about your server:

![Alt text](images/info_php.png)

After checking the relevant information about your PHP server through that page, it’s best to remove the file you created as it contains sensitive information about your PHP environment and your Ubuntu server. You can use rm to remove that file: `sudo rm /var/www/projectLEMP/info.php`

*You can always regenerate this file if you need it later.*


### STEP 7 - RETRIEVING DATA FROM MYSQL DATABASE WITH PHP

In this step, we will create a test database (DB) with simple "To do list" and configure access to it, so the Nginx website would be able to query data from the DB and display it.

We’ll need to create a new user with the mysql_native_password authentication method in order to be able to connect to the MySQL database from PHP.

We will create a database named example_database and a user named example_user, but you can replace these names with different values.

- First, connect to the MySQL console using the root account:

    `sudo mysql` or `sudo mysql -p` (if you have a password)

- To create a new database, run the following command from your MySQL console:

```
CREATE DATABASE `example_database`;
```

Now you can create a new user and grant him full privileges on the database you have just created.

- The following command creates a new user named example_user, using mysql_native_password as default authentication method. We’re defining this user’s password as **password**t , but you should replace this value with a secure password of your own choosing.

```
CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';
```

- Now we need to give this user permission over the example_database database:

```
GRANT ALL ON example_database.* TO 'example_user'@'%';
```

*This will give the example_user user full privileges over the example_database database, while preventing this user from creating or modifying other databases on your server.*

- Now exit the MySQL shell

- You can test if the new user has the proper permissions by logging in to the MySQL console again, this time using the custom user credentials: `mysql -u example_user -p`

- After logging in to the MySQL console, confirm that you have access to the example_database database: `SHOW DATABASES;`

This will give you the following output:

![Alt text](images/show%20database.png)

Next, we’ll create a test table named todo_list. From the MySQL console, run the following statement:

```
CREATE TABLE example_database.todo_list (
    item_id INT AUTO_INCREMENT,
    content VARCHAR(255),
    PRIMARY KEY(item_id)
);
```

![Alt text](images/todo_list.png)

Insert a few rows of content in the test table. You might want to repeat the next command a few times, using different VALUES:

`INSERT INTO example_database.todo_list (content) VALUES ("My first important item");`

![Alt text](images/insert%20lists.png)

To confirm that the data was successfully saved to your table, run:

`SELECT * FROM example_database.todo_list;`

You’ll see the following output:

![Alt text](images/important%20items.png)

- After confirming that you have valid data in your test table, you can exit the MySQL console.

- Now you can create a PHP script that will connect to MySQL and query for your content. Create a new PHP file in your custom web root directory using your preferred editor. We’ll use vi for that:

`vi /var/www/projectLEMP/todo_list.php`

- Copy this content into your todo_list.php script:

```
<?php
$user = "example_user";
$password = "password";
$database = "example_database";
$table = "todo_list";
 
try {
  $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
  echo "<h2>TODO</h2><ol>";
  foreach($db->query("SELECT content FROM $table") as $row) {
	echo "<li>" . $row['content'] . "</li>";
  }
  echo "</ol>";
} catch (PDOException $e) {
	print "Error!: " . $e->getMessage() . "<br/>";
	die();
}
```

- Save and close the file when you are done editing.

You can now access this page in your web browser by visiting the domain name or public IP address configured for your website, followed by /todo_list.php:

`http://<Public_domain_or_IP>/todo_list.php`

You should see a page like this, showing the content you’ve inserted in your test table:

![Alt text](images/to%20do%20on%20browser.png)

**YAAYYY** That means your PHP environment is ready to connect and interact with your MySQL server.

## Now stop or terminate your instance and any other resources to avoid additional charges.



