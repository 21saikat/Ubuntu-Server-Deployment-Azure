#### **Step 1: Set Up Your Ubuntu Server on Azure**

1. **Create a Virtual Machine (VM) on Azure**:  
   * Go to your **Azure portal** and click on **Create a resource**.  
   * Choose **Ubuntu Server** from the list of operating systems.  
   * Select a **size** (Basic B1s is enough for learning).  
   * Set up a **username** and **password** (or use SSH key pairs for secure access).  
   * Select a **region** and create the VM.  
2. **Access Your Server**:  
   * After the VM is deployed, find the **public IP address** of your server in the Azure portal.

**SSH into your server** from your terminal (on Ubuntu):  

`ssh username@your_vm_ip`

* Replace `username` and `your_vm_ip` with your server’s username and IP address.

---

#### **Step 2: Secure Your Server**

1. **Update Your Server**:

Run the following command to update your server’s software:  

`sudo apt update && sudo apt upgrade -y`

*   
2. **Set Up SSH Key Authentication (Optional but Recommended)**:  
   * If you want to secure your server with SSH keys instead of using a password:

On your local machine, create an SSH key pair:  

`ssh-keygen -t rsa`

* 

Copy the public key to your server:  
 
`ssh-copy-id username@your_vm_ip`

*   
3. **Disable Root Login**:

To prevent logging in as root directly, edit the SSH config file:  

`sudo nano /etc/ssh/sshd_config`

*   
  * Find `PermitRootLogin` and change it to `no`.

Restart SSH:  

`sudo systemctl restart ssh`

* 

---

#### **Step 3: Install and Configure a Web Server (Nginx)**

1. **Install Nginx**:

Run the following command to install Nginx:  
 
`sudo apt install nginx -y`

*   
2. **Start and Enable Nginx**:

Start the Nginx service:  

`sudo systemctl start nginx`

* 

Enable it to start automatically on boot:  

`sudo systemctl enable nginx`

*   
3. **Configure Firewall (Optional but Important)**:

Allow traffic on ports 80 (HTTP) and 443 (HTTPS) through the firewall:  

`sudo ufw allow 'Nginx Full'`

*   
4. **Test the Web Server**:  
   * In your browser, visit your server’s **public IP**. You should see the **Nginx welcome page**.

---

#### **Step 4: Install a Database (MySQL)**

1. **Install MySQL**:

Run the following command to install MySQL:  

`sudo apt install mysql-server -y`

*   
2. **Secure the Database**:

Run MySQL’s security script to secure your installation:  

`sudo mysql_secure_installation`

*   
  * Set up your root password and answer the prompts.  
3. **Create a Database and User**:

Log into MySQL:  

`sudo mysql`

* 

Create a database and a user:  
sql  
 
`CREATE DATABASE mydb;`  
`CREATE USER 'myuser'@'localhost' IDENTIFIED BY 'password';`  
`GRANT ALL PRIVILEGES ON mydb.* TO 'myuser'@'localhost';`  
`FLUSH PRIVILEGES;`

* 

Exit MySQL:  
sql  
CopyEdit  
`exit;`

* 

---

#### **Step 5: Install a Web Application (e.g., WordPress)**

1. **Install PHP** (for WordPress):

Run the following commands to install PHP and the required modules:  

`sudo apt install php-fpm php-mysql -y`

*   
2. **Download and Set Up WordPress**:

Install **WordPress** by downloading it from the official site:  
  
`cd /var/www/html`  
`sudo wget https://wordpress.org/latest.tar.gz`  
`sudo tar -xvzf latest.tar.gz`  
`sudo chown -R www-data:www-data /var/www/html/wordpress`  
`sudo chmod -R 755 /var/www/html/wordpress`

*   
3. **Configure Nginx for WordPress**:

Create a new configuration file for your site:  

`sudo nano /etc/nginx/sites-available/wordpress`

* 

Add the following basic configuration:  
nginx  
CopyEdit  
`server {`  
    `listen 80;`  
    `server_name your_vm_ip;`  
    `root /var/www/html/wordpress;`  
    `index index.php index.html index.htm;`  
    `location / {`  
        `try_files $uri $uri/ =404;`  
    `}`  
    `location ~ \.php$ {`  
        `include snippets/fastcgi-php.conf;`  
        `fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;`  
        `fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;`  
        `include fastcgi_params;`  
    `}`  
`}`

* 

Enable the site and restart Nginx:  
  
`sudo ln -s /etc/nginx/sites-available/wordpress /etc/nginx/sites-enabled/`  
`sudo systemctl restart nginx`

* 

---

#### **Step 6: Add SSL Certificates (Let’s Encrypt)**

1. **Install Certbot**:

Run the following command to install Certbot (for SSL):  

`sudo apt install certbot python3-certbot-nginx -y`

*   
2. **Generate SSL Certificate**:

Run the following command to get an SSL certificate from Let’s Encrypt:  

`sudo certbot --nginx`

*   
  * Follow the prompts to set up SSL.  
3. **Auto-Renewal**:

Set up automatic certificate renewal:  
  
`sudo systemctl enable certbot.timer`

* 

---

#### **Step 7: Automate Server Maintenance (Optional)**

1. **Write Bash Scripts** for backups or updates:

Example script for server backup:  
  
`#!/bin/bash`  
`tar -czf /backup/$(date +\%F)-backup.tar.gz /var/www/html`

*   
2. **Schedule with Cron Jobs**:

Open the crontab file:  

`crontab -e`

* 

Add a line to back up every day at midnight:  
  
`0 0 * * * /path/to/backup_script.sh`

* 

---

#### **Step 8: Monitor Your Server**

1. **Install Monitoring Tools** (e.g., **htop**, **netstat**, **ufw logs**).

To monitor server performance in real-time:  

`sudo apt install htop -y`  
`htop`

*   
2. **Set Up Email Alerts** for server issues (e.g., using **Mail** or **Postfix**).

---

#### **Step 9: Backup & Disaster Recovery**

1. **Set Up Backup** for your databases and web files.

Example for MySQL backup:  
 
`mysqldump -u myuser -p mydb > /backup/mydb_backup.sql`

* 

---

#### **Step 10: Document Your Work**

* **Create a GitHub repo** to store your scripts, configuration files, and documentation.  
* **Write a Medium article** to explain your process and showcase it to employers.

---

### 

