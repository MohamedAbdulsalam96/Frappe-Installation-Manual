# Frappe-Installation-Manual
Frappe Installation Manual
تم تنسيق دليل التثبيت الخاص بك إلى ملف Markdown منظّم وجاهز للاستخدام على أوبنتو، وستجد أدناه النسخة النهائية مع تقسيم واضح وخطوات عملية بالأوامر كما طلبت.[1]

***
title: "Frappe/ERPNext Installation Manual (Ubuntu 22.04, v14)"
***

### Overview
Operational steps for installing Frappe/ERPNext are grouped into four main phases: Before Installation, Installation, Frappe Installation, and Production Setup.[1]

- Before Installation: Update/upgrade packages and ensure a non‑root user.[1]
- Installation: Install and configure required system packages and services.[1]
- Frappe Installation: Install bench, initialize the environment, and create a site.[1]
- Production Setup: Enable scheduler, configure production, and set up services.[1]

### Before Installation
Ensure the system is up to date and work with a non‑root user for security and proper environment setup.[1]

- Update and upgrade system packages, then reboot.[1]

```bash
sudo apt-get update -y
sudo apt-get upgrade -y
sudo reboot
```

- Create a non-root user (bench user), add to sudoers, switch to it, and move to its home directory.[1]

```bash
sudo adduser frappe
sudo usermod -aG sudo frappe
su frappe
cd /home/frappe
```

### Install required packages
Install Git, Python 3.10 and tooling, virtualenv, repository tools, MariaDB, Redis, and other runtime dependencies needed by ERPNext.[1]

- Install Git.[1]

```bash
sudo apt-get install git -y
```

- Install Python toolchain (ERPNext v14 needs Python 3.10+).[1]

```bash
sudo apt-get install -y python3-dev python3.10-dev python3-setuptools python3-pip python3-distutils
```

- Install Python venv.[1]

```bash
sudo apt-get install -y python3.10-venv
```

- Install software-properties-common for repo management.[1]

```bash
sudo apt-get install -y software-properties-common
```

- Install MariaDB server and client.[1]

```bash
sudo apt install -y mariadb-server mariadb-client
```

- Install Redis Server.[1]

```bash
sudo apt-get install -y redis-server
```

- Install other dependencies (xvfb, fonts, PDF, MySQL client libs).[1]

```bash
sudo apt-get install -y xvfb libfontconfig wkhtmltopdf libmysqlclient-dev
```

### Configure MySQL
Secure the MariaDB installation and set character encodings for Frappe/ERPNext compatibility.[1]

- Run MySQL secure installation and follow prompts as indicated below.[1]

```bash
sudo mysql_secure_installation
```

- Prompts guide: enter current root password (SSH root), enable unix_socket auth (Y), change root password (Y), remove anonymous users (Y), disallow root remote login (N), remove test DB (Y), reload privileges (Y).[1]

- Edit MySQL defaults to utf8mb4 and restart the service.[1]

```bash
sudo nano /etc/mysql/my.cnf
```

Add the following, then save:[1]

```
[mysqld]
character-set-client-handshake = FALSE
character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci

[mysql]
default-character-set = utf8mb4
```

Restart MySQL:[1]

```bash
sudo service mysql restart
```

### Install curl, Node, npm, Yarn
Install curl, Node via nvm (Node 16.15.0 for v14), npm, and Yarn globally.[1]

- Install curl.[1]

```bash
sudo apt install -y curl
```

- Install Node using nvm and load the profile, then install Node 16.15.0.[1]

```bash
curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash
source ~/.profile
nvm install 16.15.0
```

- Install npm.[1]

```bash
sudo apt-get install -y npm
```

- Install Yarn globally.[1]

```bash
sudo npm install -g yarn
```

### Install Frappe (bench)
Install bench, initialize a bench folder, enter it, and set directory permissions for the bench user.[1]

- Install Frappe Bench.[1]

```bash
sudo pip3 install frappe-bench
```

- Initialize bench for Frappe v14.[1]

```bash
bench init --frappe-branch version-14 frappe-bench
```

- Change directory to the bench folder.[1]

```bash
cd frappe-bench
```

- Grant execute permission to the bench user on its home directory.[1]

```bash
chmod -R o+rx /home/frappe
```

### Create site and install apps
Create a site, fetch ERPNext and optional apps (e.g., HRMS), then install them on the site.[1]

- Create a new site (replace [site-name]).[1]

```bash
bench new-site [site-name]
```

- Get ERPNext app for v14.[1]

```bash
bench get-app --branch version-14 erpnext
```

- Optionally get HRMS or other apps similarly.[1]

```bash
bench get-app hrms
```

- Install ERPNext on the site.[1]

```bash
bench --site [site-name] install-app erpnext
```

- Install other apps on the site (example: HRMS).[1]

```bash
bench --site [site-name] install-app hrms
```

### Run development server
Start the development server and access ERPNext on port 8000 if no other instance is running.[1]

- Start the server.[1]

```bash
bench start
```

- Access via http://YOUR_SERVER_IP:8000 to see ERPNext v14 running if the port is free.[1]

- Note: in develop mode, the server will not auto-start on reboot; rerun bench start after restarts.[1]

### Production setup
Switch to production mode so services start automatically and the site is served without a port in the URL.[1]

- Enable scheduler on the site.[1]

```bash
bench --site [site-name] enable-scheduler
```

- Disable maintenance mode.[1]

```bash
bench --site [site-name] set-maintenance-mode off
```

- Set up production configuration for the bench user (replace with your bench user if different).[1]

```bash
sudo bench setup production frappe
```

- Generate and apply NGINX configuration.[1]

```bash
bench setup nginx
```

- Restart Supervisor and finalize production setup, accepting to save updated configs when prompted.[1]

```bash
sudo supervisorctl restart all
sudo bench setup production frappe
```

- After completion, the instance runs in production and is accessible via the server IP without specifying the port, and will auto-start on server reboot.[1]

### Finish
Your ERPNext v14 on Ubuntu 22.04 is now ready; good luck.[1]

[1](https://ppl-ai-file-upload.s3.amazonaws.com/web/direct-files/attachments/71173464/8b4e168f-c3ba-4c96-b559-568c9c823191/FrappeERPNEXT-Installation-Manual.docx)