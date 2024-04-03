## The purpose of this tutorial is to explain three things below :

* How to install necessary softwares, such as, Vim and Nginx
* How to configure Nginx, including setting up a separate server block
* Systemd components and `systemctl` commands.
---

** Before you begin, if you meet an error related to "Permission denied", you will need to put `sudo` at the beginning of your command so that it allows you to have privileged access temporarily. **

### Step 1 : Connecting to digital Ocean droplet using SSH
1. Open Powershell or Terminal to connect to your droplet
2. Run `ssh -i key_path root@your_droplet_ip` 
3. Make sure you are successfully connected to your droplet!

###### [NOTE] `-i` option in ssh allows you to select a file from which the identity for public key authentication is read.


---
### Step 2 : Installing/Updating necessary softwares
1. To synchronize your pacman package to the newest one, run `sudo pacman -Syu`
* ###### [NOTE] `sudo` allows you to have privileged access temporarily to run the command that you cannot run under regular access.
* ###### `pacman` refers to 'package manager', which allows you to manage your packages. 

2. For this tutorial, the main text editor we'll be using is `vim`. and for web server side, we will be using `nginx` as it is well known for its stability, simple configuration, etc.
   
4. You can install both `vim` and `nginx` by running `pacman -S vim nginx`
   
6. You might want to check if your packages are installed correctly. You can check the status of `nginx` service by running `systemctl status nginx.service`
   
8. Moreover, you can enable/disable your `nginx` by running `sudo systemctl enable/disable nginx`

###### [NOTE] `systemctl` is used to introspect and control the status of the "systemd" and service manager

---
### Step 3 : Configuring Nginx
1. You should create new server block for this tutorial. This server block files will be stored in `/etc/nginx/sites-available`, which you will need to create. To create this directory path, you should run `mkdir -p /etc/nginx/sites-available`

###### [NOTE] `-p` option makes you create a directory hierarchy.

3. Inside of this directory, you should create your configuration file for your website to work properly. Run `vim /etc/nginx/sites-available/nginx-2420.conf` Now, Press `-i` to make yourself enter `INSERT MODE` so that you are able to edit the file in vim.

###### [NOTE] Press `esc` and then type `:wq` to save your vim file properly!

4. Now, you are allowed to make a new server block inside of this configuration file. To make a new server block, include a new server block below :
```
server { 
    listen 80; 
    server_name your_droplet_ip_address;
    
    root /web/html/nginx-2420; 

    index index.html; 

    location / { 
        try_files $uri $uri/ =404; 
    }
}
```
** Don't forget to replace `your_droplet_ip_address` with your actual droplet IP address.

4. Now you created a new server block inside of your nginx configuration file. But, There's one more step left : Creating `sites-enabled` directory. This directory is to link with your configuration file.

5. You might guess we have to run `mkdir -p /etc/nginx/sites-enabled/` to make a new directory, named `sites-enabled` in `/etc/nginx/` directory.

6. Now, as mentioned above, you should link your configuration file with your newly-created directory, by running `ln -s /etc/nginx/sites-available/nginx-2420.conf /etc/nginx/sites-enabled`
---
### Step 4 : Configuring nginx.conf
1. To ensure any conflicts, You will need to edit the existing `nginx.conf`. To edit it, you will have to give the proper permission to the file. Run `sudo chmod 777 /etc/nginx/nginx.conf`. It will allow you to write/read/execute the file.
2. In the file, you should comment the server block out below :
```
server {
...
}
```
3. By doing this, you will be able to avoid any conflicts with your own configuration file.
4. To run the website properly, you will need to include `sites-enabled` directory, which is linked to your configuration. To do this, include `include /etc/nginx/sites-enabled/*;` at the end of your `http` block.

###### [NOTE] Make sure you are not including the command inside of the server block, as it is commented out!

---
### Step 5 : Storing your website documents
1. You should create your own directory in `/web/html/`, named `nginx-2420` for this project, which will store website documents you make. To create the new directory to the given path, You will need to run `sudo mkdir -p /web/html/nginx-2420`
   
3. In this `/web/html/nginx-2420/` directory, you should create your website document, such as html files, javascript files, or style files. For our tutorial, we'll be using a basic html document. To create `index.html` in `nginx-2420`, run `sudo touch /web/html/nginx-2420/index.html`
   
5. Now you will see that you created a new html file in `/web/html/nginx-2420` directory.
   
7. To configure this file, you might need proper permission to write/read/execute. To give permission to be written/read/executed properly, run `sudo chmod 777 /web/html/nginx-2420/index.html`
   
9. Edit index.html file, by copying and pasting the html code below :
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>2420</title>
    <style>
        * {
            color: #db4b4b;
            background: #16161e;
        }
        body {
            display: flex;
            align-items: center;
            justify-content: center;
            height: 100vh;
            margin: 0;
        }
        h1 {
            text-align: center;
            font-family: sans-serif;
        }
    </style>
</head>
<body>
    <h1>All your base are belong to us</h1>
</body>
</html>
```
---
### Step 6 : Configuring Systemd
1. `systemd` is a system/service manage the allows you to use some cool tools which perform general tasks, such as, logging, boot manager, and an init system.
2. We will be using systemd to control over nginx, thereby handling our website. It basically is stored in `/etc/systemd/system` directory.
3. You might not be able to find `nginx.service` as we didn't configure this service file. To create `nginx.service` file in side of `etc/systemd/system` directory, run `vim /etc/systemd/system/nginx.service`
4. Now, you can edit your service file. You should copy and paste the codes below and save it : 
```
[Unit]
Description=NGINX-2420
After=network.target

[Service]
Type=forking
ExecStartPre=/usr/bin/nginx -t
ExecStop=/usr/bin/nginx -s
ExecStart=/usr/bin/nginx


[Install]
WantedBy=multi-user.target
```

---
### Step 7: Running nginx.service
1. This is your last stipe for this project. As you finished all the steps above, you should be able to see the website document that you created, by running two commands :

1) `sudo systemctl start nginx.service`

2) `sudo systemctl enable nginx.service`

Congrats! Now you will be able to access your website document, by typing the `your_droplet_ip_address` that you used for your configuration file

Now, you will see the website as same as the screenshot I provided in the attachment section!









