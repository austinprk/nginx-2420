## Assignment3p2

### The main purpose of this tutorial is to explain three things below :

* **Firewall Configuration**
  - How to set up a firewall by using UFW
    
* **Backend Deployment** :
  - How to place the backend binary on the system
  - How to create a new service file for the backend as a service
    
* **Reverse Proxy Setup** :
  - How to test the connection to your backend
---




### Step 1 : Connecting to digital Ocean droplet using SSH
1.  As You went through last tutorial, You will need to connect to our Digital Ocean droplet by using SSH. Open Powershell or Terminal to connect to your Digital Ocean droplet.
   
2. Run `ssh -i key_path root@your_droplet_ip` to connect to your droplet.
   
3. Make sure that you're successfully connected to the droplet.
   If you can't connect to the droplet, double-check `key_path root` and `your_droplet_ip`

###### [NOTE] 
- `your_droplet_ip_address` must be replaced with your actual droplet IP address.
- `-i` option in ssh allows you to select a file from which the identity for public key authentication is read.



---
### Step 2 : Setting up a firewall using UFW

1. It is always required to keep your package database up-to-date before installing any packages to avoid technical or security issues. To do this, Run `sudo pacman -Syu`
   
2. Now, your package database is up-to-date. That means, you can install a required package for this tutorial. In this tutorial, we will be using `ufw` package to set up a firewall. To install `ufw` package, Run the following command :
```bash
sudo pacman -S ufw
```
    
* ###### [NOTE] `ufw` refers to 'Uncomplicated Firewall', which allows you to manage a netfilter firewall easily.

3. It is always a good idea to reboot your system after installing a package. It will instruct the system to safely reboot, keeping your data and system integrity safe. To do this, Run the following command :
```bash
sudo systemctl reboot
```
 
4. Take a while to ensure your system gracefully shuts down all your services. Now, re-login to your droplet using ssh. This is the thing that we did in the previous step!

 
5. After logging into your droplet, you are now ready to move onto the main part of this step. You possibly would like to only allow ssh connections from specific ip addresses. To let ufw to modify firewall rules to permit ssh connections, run the following command :

```bash
sudo ufw allow ssh
```


6. Moreover, as we are building our own web server, we might want to allow `http` and `https` connections. To allow `http` connections, Run the following commands :

```bash
sudo ufw allow http
```

To allow `https` connections, Run the following command :

```bash
sudo ufw allow https
```


7. You are all set! you are able to enable and start `ufw.service` by using `systemctl` command. To enable the service, Run the following command :

```bash
sudo systemctl enable --now ufw.service
```


8. You might want to check if the service is working correctly. To check the status of `ufw`, Run the following command :

```bash
sudo ufw status verbose
```

  ###### [NOTE]
- You should be able to find the `active` status and `22`,`80`,`443` ports when you run the above command. Each port stands for `ssh`, `http`, and `https`.
- `verbose` option is used to print the firewall's status in a more detailed way.




---
### Step 3 : Placing the backend binary
1. To begin, you will need to download the `hello-server` from the attachment. This is a compiled-binary file that will be used to deploy backend.

2. Now, You can upload this binary file to your droplet by using `stfp` command. SFTP, which stands for "Secure File Transfer Protocol", allows you to access the functiality of FTP, using SSH security features.
  
To upload the file, Run the following command in your Powershell or Terminal :

```
sftp -i .ssh/do-key user-name@your-droplet-ip
```

* ###### [NOTE] Make sure to type `exit` before running `sftp`. Moreover, Don't forget to replace `user-name` and `your-droplet-ip` with the actual username and ip-address.




3. Now you are connected to `sftp`. That means, you can upload your local file to remote droplet server. To upload your file to the droplet server, Run the following command :

```bash
put file-path
```

* ###### [NOTE] Don't forget to change `file-path` to where `hello-server` exists in your local.




4. You did upload `hello-server` file to remote server successfully! To check if your server received the file, You will need to re-login to your Digital Ocean Droplet. To log-out from the sftp server, Type `!`

5. As long as you logged into your server, you might be able to see the uploaded `hello-server` file to your home directory. To start this as a service, you will need to put this file to somewhere logical, such as `/usr/local/bin/hello-server`. To do this, Run the following command :

```bash
sudo mv hello-server /usr/local/bin/hello-server
```

* ###### [NOTE] if you meet an error related to "Permission denied", you will need to put `sudo` at the beginning of your command so that it allows you to have privileged access temporarily.




6. Now, you have successfully placed `hello-server` to the correct path! 



 
---
### Step 4 : Creating a new service file
1. To make sure your service works fine, You are required to make a new unit files. To make a new unit file for your service, Run the following command :

```bash
sudo touch /etc/systemd/system/hello-server.service
```

2. Now you can find `hello-server.service` file in `/etc/systemd/system` directory. To edit this file, you will need to set the file permission correctly. To do this, Run the following command :

```bash
sudo chmod 777 /etc/systemd/system/hello-server.service
```

* ###### [NOTE] `chmod 777` allows you have a permission to Read/Write/Execute.




4. After adjusting the permission of your service file, You need to Run the following command to edit your file with `vim`

 ```bash
sudo vim /etc/systemd/system/hello-server.service
```

5. Now, it's time to edit your service file. Copy and paste the following command :

```bash
[Unit]
Description=Assignment3p2-Hello Service
After=network.target

[Service]
Type=simple
ExecStart=/usr/local/bin/hello-server

[Install]
WantedBy=multi-user.target
```

* ###### [NOTE] To save your file after editing it, Run `:wq`, which allows you to save the file safely.




6. After modifying/moving a service file, It is always required to reload your `systemd` configuration file, making `system` aware of any changes made to the service files. To do this, Run the following command :


```bash
sudo systemctl daemon-reload
```
  
7. Well done! It is time to enable and start the service you just made! To do this, Run the following command :

```bash
sudo systemctl enable --now hello-server
```

8. You might want to check if the service is working correctly. To check the status of `hello-server`, Run the following command :

```bash
sudo systemctl status hello-server
```

* ###### [NOTE] Make sure your `hello-server` is `active`. If not, you will need to re-do the previous steps correctly in order.




---
### Step 5 : Configuring Nginx file
1.The main purpose of a `reverse proxy` is to accept client requests and route them to the appropriate backend server. As you might assume, you are required to modify `nginx` file that works for your service. To make `nginx` file and modify it, Run the following command :

```bash
sudo vim /etc/nginx/sites-available/hello-server
```

* ###### [NOTE] `Reverse Proxy` is a type of server located ahead of web servers which forwards requests from the clients to those web servers. 




2. Now, you will find yourself in `vim` editor. Copy and Paste the following contents for `reverse proxy` :

```bash
server {
    listen 80;  
    server_name your_droplet_ip

    location /hey {  
        proxy_pass http://127.0.0.1:8080/hey;
        proxy_http_version 1.1;
        proxy_set_header Host $host; 
        proxy_set_header X-Real-IP $remote_addr; 
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /echo {
        proxy_pass http://127.0.0.1:8080/echo;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

* ###### [NOTE] Don't forget to replace `your_droplet_ip` with your actual droplet ip address.


  

> `listen 80` : Allow your service to listen for port 80 HTTP requests.
>
> ` proxy_pass http://127.0.0.1:8080/hey;` : forward requests to another server running on the same machine (127.0.0.1) on port 8080 at the /hey path.
>
> `proxy_set_header Host $host;` : Sets the "host" header of the forwarded request to the "host" header from the original request.
>
> `proxy_set_header X-Real-IP $remote_addr;` : Sets the "X-Real-IP" header to the client's IP address.
>
> `proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;` : Appends the client's IP address to "X-Forwarded-For" header/
>
> `proxy_set_header X-Forwarded-Proto $scheme;` : Sets the "X-Forwarded-Proto" header to the scheme of the original request.


3. As you might assume, next stpe is to link your `hello-server` with symbolic link. This is the same step that we did in the previous tutorial! To do this, Run the following command :

```bash
sudo ln -s /etc/nginx/sites-available/hello-server /etc/nginx/sites-enabled/
```

4. Now, restart your nginx configuration by typing the following command :

```bash
sudo systemctl restart nginx
```




---
### Step 6 : Testing your connection

1. Congratulations! This is your last step for this tutorial. This step is to test your backend if it is connected correctly. To test your connections, There are two commands that you should run :

```bash
curl HTTP://your_droplet_ip/hey
```

```bash
curl -X POST -H "Content-Type: application/json" \
  -d '{"message": "Hello from your server"}' \
  http://your_droplet_ip/echo
```

###### [NOTE]
- `curl` is used to transfer data with URL
- Make sure you have entered your actual droplet ip to `your_droplet_ip`




2. Your test result should be same as the attachment I provided. Again, Congratulations!
