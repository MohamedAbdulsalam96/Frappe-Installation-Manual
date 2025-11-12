# Frappe Installation Manual
**Frappe Installation Manual**

**Operational steps for installation:**
Operational Steps for Installation Can Be Summarized In 4 Main Steps/Processes:

- **Before Installation**
1. Insure **Update & Upgrade Packages.**
2. Insure **a non-root User.**

- **Installation**
3. Start **Install Required Packages**
4. Install Frappe
---------------------------------------------
---------------------------------------------
## **Before Installation**

### > **Update & Upgrade Packages**

- Make sure you install the latest package versions by **Updating System Packages**
``` 
sudo apt-get update -y && sudo apt-get upgrade -y
sudo reboot
```
### > **Important! | Create a non-root User**

**Create a new user - (bench user)**

- In linux, the root user processes escalated privileges to perform any tasks within the system. This is why it is not advisable to use this user on a daily basis. We will create a user that we can use, and this will be the user we will also use as the **Frappe Bench User.**
- If your default user is root: >> Create a non-root User by following these steps:
- Create a new user (you can change frappe to your preferred user name):
```
sudo adduser frappe
```

**Add the user to sudoers:**
```
usermod -aG sudo frappe
```

**Switch to created user:**
```
sudo su frappe
```
**Ensure you're on created user's home directory:**
```
cd /home/frappe
```
**Continue with the next steps below.** >>

--------------------------

## **Installation**

### > **Install Required Packages: >>>>**

- ERPNext, which is built on Frappe Framework, requires a number of packages in order to run smoothly. These are the packages we will be installing in this step.

**Install GIT**
```
sudo apt-get install git
```
**Install Python**
- ERPNext version 14 requires Python version 3.10+. This is what we will install in this step.
```
sudo apt-get install python3-dev python3.10-dev python3-setuptools python3-pip python3-distutils
```
**Install Python Virtual Environment**
- A virtual environment helps in managing the dependencies for one software at one place, without having to interfere with other sections in the computer or server in which the software is running.
```
sudo apt-get install python3.10-venv
```
**Install Software Properties Common**
- Software Properties Common will help in repository management.
```
sudo apt-get install software-properties-common
```
**Install MariaDB**
```
sudo apt install mariadb-server mariadb-client
```
**Install Redis Server**
```
sudo apt-**get** install redis-server
```
**Install other packages**
- ERPNext functionality also relies on other packages we will install in this step. These will load fonts, PDFs, and other resources to our instance.
```
sudo apt-get install xvfb libfontconfig wkhtmltopdf sudo apt-get install libmysqlclient-dev
```
#### **Configure MYSQL Server**

**Setup the server**
```
sudo mysql_secure_installation
```
- When you run this command, the server will show the following prompts. Please follow the steps as shown below to complete the setup correctly.
- Enter current password for root: (Enter your SSH root user password)
- Switch to unix_socket authentication \[Y/n\]: Y
- Change the root password? \[Y/n\]: Y  
    It will ask you to set new MySQL root password at this step. This can be different from the SSH root user password.
- Remove anonymous users? \[Y/n\] Y
- Disallow root login remotely? \[Y/n\]: N  
    This is set as N because we might want to access the database from a remote server for using business analytics software like Metabase / PowerBI / Tableau, etc.
- Remove test database and access to it? \[Y/n\]: Y
- Reload privilege tables now? \[Y/n\]: Y

**Edit MYSQL default config file**
```
sudo nano /etc/mysql/my.cnf
```
- Add the following block of code exactly as is:
```
\[mysqld\] character-set-client-handshake = FALSE character-set-server = utf8mb4 collation-server = utf8mb4_unicode_ci \[mysql\] default-character-set = utf8mb4
```
**Restart the MYSQL Server**
```
sudo service mysql restart
```
#### **Instal CURL, Node, NPM and Yarn**

**Install CURL**
```
sudo apt install curl
```
**Install Node**
```
curl <https://raw.githubusercontent.com/creationix/nvm/master/install.sh> | bash source ~/.profile nvm install 16.15.0
```
**Install NPM**
```
sudo apt-get install npm
```
**Install Yarn**
```
sudo npm install -g yarn
```
---------------------------
**_All the packages required to run Frappe have been installed._**
----------------------------

### > **Install Frappe** >>>

**Install Frappe Bench**
```
sudo pip3 install frappe-bench
```
**Initialize Frappe Bench**
```
bench init --frappe-branch version-14 frappe-bench
```
**Switch directories into the Frappe Bench directory**
```
cd frappe-bench
```
**Change user directory permissions**
- This will give the bench user execution permission to the home directory.
```
chmod -R o+rx /home/frappe
```
------------------------
**_Now, Ready To Deployment Your Site And Install Apps On It._**
------------------------


#### **Create a New Site**
- A site is a requirement in ERPNext, Frappe and all the other apps we will be needing to install. We will create the site in this step.
```
bench new-site [site-name]
```
**Install ERPNext and other Apps**

- The first app we will download is the **ERPNext** **app**. This app is required when setting up ERPNext.
```
bench get-app --branch version-14 erpnext
```
- Download any other app you may be interested in in a similar manner. For instance, if you need the Human Resource app to be installed, use the following command.
```
bench get-app hrms
```
#### **Install Apps on Site**
```
bench --site \[site-name\] install-app erpnext
```
- Install all the other apps in the same way. For example, if you downloaded the human resource app, use the below command to install it.
```
bench --site \[site-name\] install-app hrms
```

----------------------
We have successfully setup ERPNext version 14 on ubuntu 22.04. 
---------------------------
**You can start the server by running the below command:**
```
bench start
```

- If you didn't have any other ERPNext instance running on the same server, 
- ERPNext will get started on port 8000. If you visit \[YOUR SERVER IP:8000\], you should be able to see Site running.
- **Please note that instances which are running on develop mode, >> like the one we just setup, will not get started when you restart your server. You will need to run the ```**bench start**``` command every time the server restarts.
- In the below steps, we will learn 
------------
### **How to deploy the production mode?**
#### **Setting ERPNext for Production**

**Enable Scheduler**
```
bench --site \[site-name\] enable-scheduler
```
**Disable maintenance mode**
```
bench --site \[site-name\] set-maintenance-mode off
```
**Setup production config**
```
sudo bench setup production frappe
```
**Setup NGINX to apply the changes**
```
bench setup nginx
```
**Restart Supervisor and Launch Production Mode**
```
sudo supervisorctl restart all sudo bench setup production frappe
```
- If you are prompted to save the new/existing config file, respond with a Y.
- When this completes doing the settings, your instance is now on production mode and can be accessed using your IP, without needing to use the port.
- This also will mean that your instance will start automatically even in the event you restart the server.

Finsh! GOOD LUCK!
-----------------------------
-----------------------------

### **>> What the Next?** 
**You can begin your journey by learning:**
* How to Set Up Multitenancy?
* How to Create a Custom App and Install it on the Site?
