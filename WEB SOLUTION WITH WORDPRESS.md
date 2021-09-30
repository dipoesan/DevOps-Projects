# WEB SOLUTION WITH WORDPRESS

In this project we will be preparing storage infrastructure on two Linux servers and implement a basic web solution using WordPress.

## Step 1 LAUNCH TWO EC2 INSTANCES THAT WILL SERVE AS “WEB SERVER” AND "DB SERVER" RESPECTIVELY.
For this project we would be launching `RHEL` instances to use for our setup

![image](https://user-images.githubusercontent.com/22638955/135489142-f537e09e-4b9a-46cc-a2ec-cc7b913844ca.png)

We would create 3 volumes in the same AZ as our EC2 Web Server, each of 10 Gb.

Then attach them to the web server instance

![image](https://user-images.githubusercontent.com/22638955/135502905-9674d859-5c4c-4bd7-8238-4736e2633883.png)

Open up the terminal for the web server and use the `lsblk` command to inspect what block devices are attached to the server

![image](https://user-images.githubusercontent.com/22638955/135503674-0028465e-3cb4-4a21-b2b0-8b08eb57c179.png)

From the above image, we can see the newly created devices listed as `xvdf, xvdg, xvdh`

We would be using the gdisk utility to create a single partition on each of the 3 disks as shown in the image below - 

```
sudo gdisk /dev/xvdf
```

![image](https://user-images.githubusercontent.com/22638955/135505250-a1b76a0a-1fbc-4ce0-8661-b38f18dac842.png)

Use the `lsblk` utility to view the newly configured partitions on each of the 3 disks.

![image](https://user-images.githubusercontent.com/22638955/135506269-820be82d-7033-4712-84e8-1369a5833593.png)


