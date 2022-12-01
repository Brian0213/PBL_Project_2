# Documentation for Project 2: LEMP STACK IMPLEMENTATION 

## Preparing prerequisites

- Create a new EC2 Instance of t2.nano family with Ubuntu Server 20.04 LTS (HVM) image.

1. **Installing The NGINX Web Server**

- Installing Ngix

`sudo apt update`

![Apt Update Status](./image/apt-update-status.PNG)

`sudo apt install nginx`

![Apt Install Nginx Status](./image/install-nginx-status.PNG)

- To verify that nginx was successfully installed and is running as a service in Ubuntu, run:

`sudo systemctl status nginx`

![Nginx Success Status](./image/systemctl-nginx-status.PNG)

- First, let us try to check how we can access it locally in our Ubuntu shell, run:

`curl http://localhost:80`

`curl http://127.0.0.1:80`

![Curl Host Status](./image/curl-host-status.PNG)

- Now it is time for us to test how our Nginx server can respond to requests from the Internet.
Open a web browser of your choice and try to access following url:

 [Nginx Server Url](http://18.222.189.33:80)

![Nginx Webserver Status](./image/nginx-webserver-success-status.PNG)

- Now it is time for us to test how our Nginx server can respond to requests from the Internet.
- Open a web browser of your choice and try to access following url:

[Nginx Http Url](http://18.222.189.33:80)

[Nginx Http Url](http://18.222.189.33/)

![Nginx Webserver Status](./image/nginx-webserver-success-status.PNG)

- Another way to retrieve your Public IP address, other than to check it in AWS Web console, is to use following command:

`curl -s http://169.254.169.254/latest/meta-data/public-ipv4`

![To Retrieve Public IP Status](./image/to-retrieve-public-ip.PNG)

2.**Installing MySql**

- Again, use ‘apt’ to acquire and install this software:

`sudo apt install mysql-server`

- When prompted, confirm installation by typing Y, and then ENTER.

![Prompt 'Y' Enter](./image/prompt-y-enter.PNG)

![Install MySql Server](./image/install-mysql-server-success.PNG)

- When the installation is finished, log in to the MySQL console by typing:

`sudo mysql`

- This will connect to the MySQL server as the administrative database user root, which is inferred by the use of sudo when running this command. You should see output like this:

![Output MySql Connect Message](./image/output-mysql-connect.PNG)

- It’s recommended that you run a security script that comes pre-installed with MySQL. This script will remove some insecure default settings and lock down access to your database system. Before running the script you will set a password for the root user, using mysql_native_password as default authentication method. We’re defining this user’s password as PassWord.1:

`ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';`

![Password Authentication](./image/password-authentication-status.PNG)

- Exit the MySQL shell with:

`exit`

![Exit MySql Status](./image/exit-status.PNG)

- Start the interactive script by running:

`sudo mysql_secure_installation`

![MySql Secure Installation](./image/mysql-secure-installation-output.PNG)

![MySql Secure Installation](./image/mysql-secure-installation-output2.PNG)

![MySql Secure Installation](./image/mysql-secure-installation-output3.PNG)

- When you’re finished, test if you’re able to log in to the MySQL console by typing:

`sudo mysql -p`

![MySql Secure Installation](./image/mysql-console-login-output.PNG)

- To exit the MySQL console, type:

`exit`

![MySql Exit Output](./image/mysql-exit-output.PNG)

3.**Installing PHP**

- You have Nginx installed to serve your content and MySQL installed to store and manage your data. Now you can install PHP to process code and generate dynamic content for the web server.

- While Apache embeds the PHP interpreter in each request, Nginx requires an external program to handle PHP processing and act as a bridge between the PHP interpreter itself and the web server. This allows for a better overall performance in most PHP-based websites, but it requires additional configuration. You’ll need to install php-fpm, which stands for “PHP fastCGI process manager”, and tell Nginx to pass PHP requests to this software for processing. Additionally, you’ll need php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases. Core PHP packages will automatically be installed as dependencies.

- To install these 2 packages at once, run:

`sudo apt install php-fpm php-mysql`

![Install PHP Output](./image/php-install-output.PNG)

4.**Configuring Nginx to Use PHP Processor**

- When using the Nginx web server, we can create server blocks (similar to virtual hosts in Apache) to encapsulate configuration details and host more than one domain on a single server. In this guide, we will use projectLEMP as an example domain name.

On Ubuntu 20.04, Nginx has one server block enabled by default and is configured to serve documents out of a directory at /var/www/html. While this works well for a single site, it can become difficult to manage if you are hosting multiple sites. Instead of modifying /var/www/html, we’ll create a directory structure within /var/www for the your_domain website, leaving /var/www/html in place as the default directory to be served if a client request does not match any other sites.

- Create the root web directory for your_domain as follows:

`sudo mkdir /var/www/projectLEMP`

![ProjectLEMP Create Directory Output](./image/lemp-directory-create-output.PNG)

- Next, assign ownership of the directory with the $USER environment variable, which will reference your current system user:

`sudo chown -R $USER:$USER /var/www/projectLEMP`

![Owner Directory User Environment Variable](./image/assign-owner-to-directory.PNG)

- Then, open a new configuration file in Nginx’s sites-available directory using your preferred command-line editor. Here, we’ll use nano:

`sudo nano /etc/nginx/sites-available/projectLEMP`

- This will create a new blank file. Paste in the following bare-bones configuration:

![Nginx Configuration Page](./image/nginx-configuration-page.PNG)

- Here’s what each of these directives and location blocks do:

- listen — Defines what port Nginx will listen on. In this case, it will listen on port 80, the default port for HTTP.
root — Defines the document root where the files served by this website are stored.
- index — Defines in which order Nginx will prioritize index files for this website. It is a common practice to list index.html files with a higher precedence than index.php files to allow for quickly setting up a maintenance landing page in PHP applications. You can adjust these settings to better suit your application needs.
- server_name — Defines which domain names and/or IP addresses this server block should respond for. Point this directive to your server’s domain name or public IP address.
- location / — The first location block includes a try_files directive, which checks for the existence of files or directories matching a URI request. If Nginx cannot find the appropriate resource, it will return a 404 error.
- location ~ \.php$ — This location block handles the actual PHP processing by pointing Nginx to the fastcgi-php.conf configuration file and the php7.4-fpm.sock file, which declares what socket is associated with php-fpm.
- location ~ /\.ht — The last location block deals with .htaccess files, which Nginx does not process. By adding the deny all directive, if any .htaccess files happen to find their way into the document root ,they will not be served to visitors.-

- Activate your configuration by linking to the config file from Nginx’s sites-enabled directory:

`sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/`

![Activate Nginx Directory](./image/activate-nginx-directory.PNG)

- This will tell Nginx to use the configuration next time it is reloaded. You can test your configuration for syntax errors by typing:

`sudo nginx -t`

![Nginx Configuration Success Output](./image/test-nginx-configuration-success.PNG)

- We also need to disable default Nginx host that is currently configured to listen on port 80, for this run:

`sudo unlink /etc/nginx/sites-enabled/default`

![Nginx Default Disable](./image/nginx-default-disable-output.PNG)

- When you are ready, reload Nginx to apply the changes:

`sudo systemctl reload nginx`

![Nginx Systemctl Reload](./image/nginx-systemctl-reload.PNG)

- Your new website is now active, but the web root /var/www/projectLEMP is still empty. Create an index.html file in that location so that we can test that your new server block works as expected:

`sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html`

![Create Index Html Output](./image/index-html-output.PNG)

- Now go to your browser and try to open your website URL using IP address:

[Echo Message with IP Address](http://18.222.189.33:80)

[Echo Message with DNS Name](http://ec2-18-222-189-33.us-east-2.compute.amazonaws.com/)

![Website Ip Output](./image/website-url-ip-output.PNG)

5.**Testing PHP with Nginx**

- At this point, your LAMP stack is completely installed and fully operational.

You can test it to validate that Nginx can correctly hand .php files off to your PHP processor.

You can do this by creating a test PHP file in your document root. Open a new file called info.php within your document root in your text editor:

`sudo nano /var/www/projectLEMP/info.php`

Type or paste the following lines into the new file. This is valid PHP code that will return information about your server:

`<?php
phpinfo();`

- You can now access this page in your web browser by visiting the domain name or public IP address you’ve set up in your Nginx configuration file, followed by /info.php:

`sudo nano /var/www/projectLEMP/info.php`

[PHP Ip Url link](http://18.222.189.33/info.php)

![Php Ip Page Output](./image/php-info-7.PNG)

- After checking the relevant information about your PHP server through that page, it’s best to remove the file you created as it contains sensitive information about your PHP environment and your Ubuntu server. You can use rm to remove that file:

`sudo rm /var/www/projectLEMP/info.php`

![Directory Removal Output](./image/directory-remove-output.PNG)

6.**Retrieving data from MySQL database with PHP**

- In this step you will create a test database (DB) with simple "To do list" and configure access to it, so the Nginx website would be able to query data from the DB and display it.

At the time of this writing, the native MySQL PHP library mysqlnd doesn’t support caching_sha2_authentication, the default authentication method for MySQL 8. We’ll need to create a new user with the mysql_native_password authentication method in order to be able to connect to the MySQL database from PHP.

We will create a database named example_database and a user named example_user, but you can replace these names with different values.

First, connect to the MySQL console using the root account:

`sudo mysql`

![Directory Removal Output](./image/mysql-launch-status.PNG)

To create a new database, run the following command from your MySQL console:

`CREATE DATABASE example_database;`

![Database Create Output](./image/database-create-status.PNG)

Now you can create a new user and grant him full privileges on the database you have just created.

The following command creates a new user named example_user, using mysql_native_password as default authentication method. We’re defining this user’s password as password, but you should replace this value with a secure password of your own choosing.

`CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';`

![Mysql User Credentials](./image/mysql-user-password-create-status.PNG)

Now we need to give this user permission over the example_database database:

`GRANT ALL ON example_database.* TO 'example_user'@'%';`

![Mysql User Access](./image/user-access-grant-output.PNG)

- This will give the example_user user full privileges over the example_database database, while preventing this user from creating or modifying other databases on your server.

Now exit the MySQL shell with:

`exit`

![Mysql Exit Output](./image/exit-mysql-output.PNG)

You can test if the new user has the proper permissions by logging in to the MySQL console again, this time using the custom user credentials:

`mysql -u example_user -p`

![Mysql User Access](./image/custom-credentials-output.PNG)

Notice the -p flag in this command, which will prompt you for the password used when creating the example_user user. After logging in to the MySQL console, confirm that you have access to the example_database database:

`SHOW DATABASES;`

![Show Database](./image/show-databases-info.PNG)

- Next, we’ll create a test table named todo_list. From the MySQL console, run the following statement:

`CREATE TABLE example_database.todo_list (
mysql>     item_id INT AUTO_INCREMENT,
mysql>     content VARCHAR(255),
mysql>     PRIMARY KEY(item_id)
mysql> );`

![Todo List Table](./image/todo-list-table-status.PNG)

- Insert a few rows of content in the test table. You might want to repeat the next command a few times, using different VALUES:

`INSERT INTO example_database.todo_list (content) VALUES ("My first important item");`

![Rows Created in the Todo list Table](./image/rows-created-in-table.PNG)

- To confirm that the data was successfully saved to your table, run:

`SELECT * FROM example_database.todo_list;`

![Select Query Output](./image/select-query-output.PNG)

- After confirming that you have valid data in your test table, you can exit the MySQL console:

`exit`

![Mysql Exit Console](./image/exit-mysql-output.PNG)

- Now you can create a PHP script that will connect to MySQL and query for your content. Create a new PHP file in your custom web root directory using your preferred editor. We’ll use vi for that:

`nano /var/www/projectLEMP/todo_list.php`

You can now access this page in your web browser by visiting the domain name or public IP address configured for your website, followed by /todo_list.php:

`http://3.19.232.67/todo_list.php`

![Todo list Ip Address Output](./image/todo-list-ip-output.PNG)
