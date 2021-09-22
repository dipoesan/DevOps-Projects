# IMPLEMENT A CLIENT SERVER ARCHITECTURE USING MYSQL DATABASE MANAGEMENT SYSTEM (DBMS)

For this project, we are going to demonstrate a basic client-server architecture using MySQL Relational Database Management System (RDBMS).

## STEP 1 Create and configure two Linux-based virtual servers

![image](https://user-images.githubusercontent.com/22638955/134379702-4efb8359-8e1f-446c-af8d-b9f6df9c2ace.png)

## STEP 2 Setup Mysql Server and Client on both instances

On the `mysql-server` Linux Server install `MySQL Server` software.
```
sudo apt update
sudo apt upgrade
sudo apt install mysql-server -y
sudo mysql_secure_installation
```

On the `mysql client` Linux Server install `MySQL Client` software
```
sudo apt update
sudo apt upgrade
sudo apt install mysql-client -y
```

By default, these EC2 virtual servers are located in the same local virtual network, so they can communicate to each other using local IP addresses.
MySQL server uses TCP port `3306` by default, so we will have to open up that port. We would also be allowing access from only the IP address of our mysql client.

Configure MySQL server to allow connections from remote hosts by replacing ‘127.0.0.1’ to ‘0.0.0.0’ in the `Bind Address`:
```
sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
```

![image](https://user-images.githubusercontent.com/22638955/134383762-8665d94c-5f72-43cc-9a69-a3743867ae9e.png)

Create a user in mysql-server -
```
CREATE USER 'user'@'the mysql client ip address' IDENTIFIED BY 'password';
```

Grant all privileges to all databases in your mysql-server to this user - 
```
grant all privileges on *.* to 'user'@'%' with grant option;
flush privileges;
```

From the `mysql client` Linux Server we will connect remotely to the `mysql server` Database Engine without using `SSH`.
In the mysql-client, type in the command below -
```
mysql -u Opid -p -h ip-address of the mysql-server
```
If we are logged in, we would also try to see if we can run queries from our client to the server - 
```
show databases;
```

![image](https://user-images.githubusercontent.com/22638955/134414821-b835625f-07c9-4f68-b29a-7fd83e59278c.png)

From the image above, we can see that we successfully connected to our mysql-server and we could also run queries from our client to the server.
