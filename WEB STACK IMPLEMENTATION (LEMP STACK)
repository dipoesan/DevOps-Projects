# WEB STACK IMPLEMENTATION (LEMP STACK)

In this project we will implement a web stack that is similar to the LAMP stack, but instead of using Apache, we will be using an alternative Web Server – NGINX
This web sever is also very popular and widely used by many websites in the Internet.

Launch a new EC2 instance of the `t2.micro` family with the Ubuntu Server 20.04 LTS (HVM) AMI under the `free tier`, and then `SSH` into the instance.

![image](https://user-images.githubusercontent.com/22638955/132418515-71877057-02ef-4f45-a96d-d668eb0e1527.png)

![image](https://user-images.githubusercontent.com/22638955/132418712-dddcc93d-53cc-4f01-9f1a-3f939c8aebad.png)

## ![image](https://user-images.githubusercontent.com/22638955/132418712-dddcc93d-53cc-4f01-9f1a-3f939c8aebad.png)

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


