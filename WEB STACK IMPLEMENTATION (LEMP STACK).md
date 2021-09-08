# WEB STACK IMPLEMENTATION (LEMP STACK)

In this project we will implement a web stack that is similar to the LAMP stack, but instead of using Apache, we will be using an alternative Web Server – NGINX
This web sever is also very popular and widely used by many websites in the Internet. The LEMP stack consists of Linux, NGINX, MySQL, PHP/Python/Perl.

## STEP 1 – CREATING A LINUX INSTANCE (L)

Launch a new EC2 instance of the `t2.micro` family with the Ubuntu Server 20.04 LTS (HVM) AMI under the `free tier`, and then `SSH` into the instance.

![image](https://user-images.githubusercontent.com/22638955/132418515-71877057-02ef-4f45-a96d-d668eb0e1527.png)

![image](https://user-images.githubusercontent.com/22638955/132418712-dddcc93d-53cc-4f01-9f1a-3f939c8aebad.png)

## STEP 2 – INSTALLING THE NGINX WEB SERVER (E)

To install NGINX, we would first have to by update our server’s package index, then install NGINX after.

```
sudo apt update
sudo apt install nginx -y
```
Verify that NGINX was successfully installed and is running as a service in Ubuntu

```
sudo systemctl status nginx
```
![image](https://user-images.githubusercontent.com/22638955/132419224-21264000-502d-405f-b34f-f67c9f9081e5.png)

If it is green and running like what we have above, then we did everything correctly.

Before we can receive any traffic to our Web Server, we need to open `TCP port 80` which is the default port that web browsers use to access web pages on the Internet.

![image](https://user-images.githubusercontent.com/22638955/132419777-12e9ca91-8c08-4b3f-a3ef-4e5feb7ce95f.png)

Let us try to check how we can access it locally in our Ubuntu shell
```
curl http://localhost:80
or
curl http://127.0.0.1:80
```
Both commands above do the same thing and would give us what we see in the image below - 

![image](https://user-images.githubusercontent.com/22638955/132420001-4046356d-ba72-412b-b8c3-dd49c0b8fc83.png)

We would now test how our Nginx server can respond to requests from the Internet
```
http://<Public-IP-Address>:80![image](https://user-images.githubusercontent.com/22638955/132420563-9185555a-c351-4234-be61-99edb8772b83.png)
```

We can either check for our `IP-ADDRESS` within the AWS-CONSOLE or wew can run the command below in our instance -
```
curl -s http://169.254.169.254/latest/meta-data/public-ipv4
```
If you see the following page, then your web server is now correctly installed and accessible through your firewall.
![image](https://user-images.githubusercontent.com/22638955/132420563-9185555a-c351-4234-be61-99edb8772b83.png)

The above image is the same content as what we got when we ran the `curl` command.

## STEP 3 — INSTALLING MYSQL (M)

Just like we did in the LAMP stack project, we need to install a database management system to store and manage our data in a relational database and our choice is `MySQL`.

```
sudo apt install mysql-server
```
It is recommended that we run a security script that comes pre-installed with MySQL. This script will remove some insecure default settings and lock down access to our database system. Start the interactive script by running:

```
sudo mysql_secure_installation
```
We can test to see if we can access our MySQL server by running the command below - 
```
sudo mysql
```
![image](https://user-images.githubusercontent.com/22638955/132584840-f258acba-7bd3-42ef-b913-60925138dcca.png)

## STEP 4 – INSTALLING PHP (P)

We have Nginx installed to serve our content and MySQL installed to store and manage our data. Now we can install PHP to process code and generate dynamic content for the web server.

While Apache embeds the PHP interpreter in each request, NGINX requires an external program to handle PHP processing and act as a bridge between the PHP interpreter itself and the web server. This allows for a better overall performance in most PHP-based websites, but it requires additional configuration.

We need to install `php-fpm`, which stands for “PHP fastCGI process manager”, and tell Nginx to pass PHP requests to this software for processing. Additionally, we’ll need `php-mysql`, a PHP module that allows PHP to communicate with MySQL-based databases. Core PHP packages will automatically be installed as dependencies.

To install these 2 packages at once, we would run - 
```
sudo apt install php-fpm php-mysql -y
```

## STEP 5 — CONFIGURING NGINX TO USE PHP PROCESSOR

When using the Nginx web server, we can create `server blocks` (similar to virtual hosts in Apache) to encapsulate configuration details and host more than one domain on a single server. In this guide, we will use `projectLEMP` as an example domain name.

On Ubuntu 20.04, Nginx has one server block enabled by default and is configured to serve documents out of a directory at `/var/www/html`. While this works well for a single site, it can become difficult to manage if we are hosting multiple sites. Instead of modifying `/var/www/html`, we’ll create a directory structure within `/var/www` for the `your_domain` website, leaving `/var/www/html` in place as the default directory to be served if a client request does not match any other sites.

Let's create the root web directory for `your_domain` as follows - 
```
sudo mkdir /var/www/projectLEMP
```

Assign ownership of the directory with the $USER environment variable, which will reference the current system user (ubuntu) -
```
sudo chown -R $USER:$USER /var/www/projectLEMP
```
![image](https://user-images.githubusercontent.com/22638955/132587062-9f2fdd30-b29d-427b-a81d-ce4368ab21e1.png)

Open a new configuration file in Nginx’s `sites-available` directory using your preferred command-line editor. (I prefer "vi") - 
```
sudo vi /etc/nginx/sites-available/projectLEMP
```
A new blank file is created. Paste in the following bare-bones configuration -
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
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }

}
```

Here’s what each of these directives and location blocks above do - 

* `listen` — Defines what port Nginx will listen on. In this case, it will listen on port `80`, the default port for HTTP.
* `root` — Defines the document root where the files served by this website are stored.
* `index` — Defines in which order Nginx will prioritize index files for this website. It is a common practice to list `index.html` files with a higher precedence than `index.php` files to allow for quickly setting up a maintenance landing page in PHP applications. You can adjust these settings to better suit your application needs.
* `server_name` — Defines which domain names and/or IP addresses this server block should respond for. Point this directive to your server’s domain name or public IP address.
* `location /` — The first location block includes a `try_files` directive, which checks for the existence of files or directories matching a URI request. If Nginx cannot find the appropriate resource, it will return a 404 error.
* `location ~ \.php$` — This location block handles the actual PHP processing by pointing Nginx to the fastcgi-php.conf configuration file and the `php7.4-fpm.sock file`, which declares what socket is associated with `php-fpm`.
* `location ~ /\.ht` — The last location block deals with `.htaccess` files, which Nginx does not process. By adding the deny all directive, if any `.htaccess` files happen to find their way into the document root ,they will not be served to visitors.

We would activate the configuration by linking to the config file from Nginx’s `sites-enabled` directory - 
```
sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/
```

This will tell Nginx to use the configuration next time it is reloaded. 
WE can test our configuration for syntax errors by typing - 
```
sudo nginx -t
```
![image](https://user-images.githubusercontent.com/22638955/132588534-03104962-13f1-4ba4-90a6-d4d47de75ecd.png)

If any errors are reported, go back to the configuration file to review its contents before going forward.

We also need to disable default Nginx host that is currently configured to listen on port 80. To do this, run - 
```
sudo unlink /etc/nginx/sites-enabled/default
```
Reload Nginx to apply the changes - 
```
sudo systemctl reload nginx
```
The new website is now active, but the web root `var/www/projectLEMP`is still empty.
We have to ceate an index.html file in that location so that we can test that your new server block works as expected -
```
sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html
```
Running the above command should copy the output of the curl command (the IP address for our EC2 instance) into the `ndex.html` file.
Now go to the browser and try to open the website URL using our IP address found in the index.html file - 

![image](https://user-images.githubusercontent.com/22638955/132594849-42b45b36-a6f6-41c1-8d2d-298fe6fcf2ae.png)

## STEP 6– TESTING PHP WITH NGINX
We can test to validate that Nginx can correctly hand `php`files to our PHP processor. 
We can do this by creating a test PHP file in our document root.
Open a new file called info.php within your document root in your text editor:

