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

Install the `lvm2` package using `sudo yum install lvm2 -y` 

Run `sudo lvmdiskscan` command to check for available partitions.

![image](https://user-images.githubusercontent.com/22638955/135509096-d87ee9aa-25ad-4600-8efe-0c2a8a80bc98.png)

Use `pvcreate` utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM

```
sudo pvcreate /dev/xvdf1
sudo pvcreate /dev/xvdg1
sudo pvcreate /dev/xvdh1
```

![image](https://user-images.githubusercontent.com/22638955/135509769-1e18a7ee-84f8-45cf-b508-a6e07223798b.png)

We would verify that our Physical volume has been created successfully by running `sudo pvs`

![image](https://user-images.githubusercontent.com/22638955/135510322-9df2d7db-7409-4e50-944a-7af312742144.png)

Use the `vgcreate` utility to add all 3 PVs to a volume group (VG). Name the VG `webdata-vg`

```
sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1
```

Verify that the VG has been created successfully by running `sudo vgs`

![image](https://user-images.githubusercontent.com/22638955/135511290-fc844f49-6f1d-40f9-a8da-f6569a469271.png)

Use the `lvcreate` utility to create 2 logical volumes. apps-lv (Use half of the PV size), and logs-lv Use the remaining space of the PV size. Apps-lv will be used to store data for the Website while, logs-lv will be used to store data for logs.

```
sudo lvcreate -n apps-lv -L 14G webdata-vg
sudo lvcreate -n logs-lv -L 14G webdata-vg
```
Verify that the Logical Volumes have been created successfully by running `sudo lvs`

![image](https://user-images.githubusercontent.com/22638955/135518047-eeec5397-dc7c-4bed-bf49-f8f768ded46e.png)

Verify the entire setup
```
sudo vgdisplay -v #view complete setup - VG, PV, and LV
sudo lsblk 
```

![image](https://user-images.githubusercontent.com/22638955/135518499-761e6cdd-8b2c-4cc5-aafc-62a179fdedf8.png)

Use mkfs.ext4 to format the logical volumes with ext4 filesystem
```
sudo mkfs -t ext4 /dev/webdata-vg/apps-lv
sudo mkfs -t ext4 /dev/webdata-vg/logs-lv
```

Create `/var/www/html` directory to store website files
```
sudo mkdir -p /var/www/html
```

Create `/home/recovery/logs` to store backup of log data
```
sudo mkdir -p /home/recovery/logs
```

Mount `/var/www/html` on `apps-lv` logical volume
```
sudo mount /dev/webdata-vg/apps-lv /var/www/html/
```

We would use the `rsync` utility to backup all the files in the log directory /var/log into /home/recovery/logs as this is required before mounting the file system
```
sudo rsync -av /var/log/. /home/recovery/logs/
```

Mount `/var/log` on `logs-lv` logical volume. (Note that all the existing data on `/var/log` will be deleted. That is why we backed up the `log` directory earlier
```
sudo mount /dev/webdata-vg/logs-lv /var/log
```

Restore `log` files back into `/var/log` directory
```
sudo rsync -av /home/recovery/logs/. /var/log
```

Update `/etc/fstab` file so that the mount configuration will persist after restart of the server.

The UUID of the device will be used to update the `/etc/fstab` file;

To get the UUID of the devices, we would run `sudo blkid`

![image](https://user-images.githubusercontent.com/22638955/135521786-1d69290d-bf1b-461c-ade2-172e65720bf0.png)

Open up the `/etc/fstab` file using `sudo vi /etc/fstab` and paste the UUID's as shown in the image below

![image](https://user-images.githubusercontent.com/22638955/135696342-62c15ff6-44f8-4fa3-b079-ecd81ea4f685.png)

Test the configuration and reload the daemon
```
sudo mount -a
sudo systemctl daemon-reload
```

Verify the setup by running df -h, output must look like what is below:

![image](https://user-images.githubusercontent.com/22638955/135536944-dd2c6b37-7502-43a8-8934-a2f06af1762a.png)

