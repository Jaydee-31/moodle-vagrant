# Introduction

This project serves as an example of [How to Install Moodle on Ubuntu 20.04 with Nginx](https://linux.how2shout.com/how-to-install-moodle-on-ubuntu-20-04-with-nginx/?fbclid=IwAR190M_gwv7u0ZqxFEnN5hDJdmvA7skN8rROBFaIwzCKxSddm7QzKhWeIX8).

# Overview of the box

The Open LMS development box has the following technologies installed:

- Ubuntu is the operating system
- Nginx with PHP-FPM
- PHP 8.0
- MariaDB
- And other various tools required by Moodle.

# Vagrant install

To install and use this project:

1. Clone this project: `git clone https://github.com/Jaydee-31/moodle-vagrant.git`
2. Install recommended versions of [Vagrant and VirtualBox](https://developer.hashicorp.com/vagrant/install#windows).
3. Install Vagrant Host Manager Plugin: `vagrant plugin install vagrant-hostmanager`
4. Open your terminal and from within this project, run: `vagrant up`

After the `vagrant up` command completes, your virtual machine will be ready. You can use `vagrant ssh` to access it.

# Update Ubuntu 20.04

Start with updating the system to install the latest available updates and refresh the APT package index cache.

    sudo apt update

# Install Nginx and PHP

Let’s install the Nginx web server which we require to serve the files and PHP programming language to run Moodle because it has been developed in it.

    sudo apt install nginx-full

# Setup PHP 8.x on Ubuntu 20.04

The version of PHP available through the default repository of Ubuntu is 7.4, however, here we are installing PHP 8.0 so that future versions of Moodle won’t create problems.

However, for the latest version of the PHP, we need to add an extra repository.

    sudo apt install software-properties-common
    sudo add-apt-repository ppa:ondrej/php

Install PHP 8.0 and extensions:

    sudo apt install php8.0 php8.0-{fpm,common,mbstring,xmlrpc,soap,gd,xml,intl,mysql,cli,mcrypt,ldap,zip,curl}

To check the version:

    php -v

Edit the php.ini file and change some settings:

    > sudo nano /etc/php/8.0/fpm/php.ini

Note: If you are using the default version of PHP or any other then change 8.0 with that in the above command.

Change the following lines:

    //also don’t forget to remove the semicolon (;) given in front of max_input_vars.

    memory_limit = 256M
    cgi.fix_pathinfo = 0
    upload_max_filesize = 100M
    max_execution_time = 360
    date.timezone = Asia/Singapore
    max_input_vars = 5000

After making the changes save the file by pressing Ctrl+O, hit the Enter key, and then exit Ctrl+X.

Restart the PHP-FPM service to apply the changes:

    > sudo systemctl restart php8.0-fpm

Once you are finished, you can proceed to the next step.

# Create Moodle Database (MariaDB)

We can install MySQL, however, here we are using MariaDB (fork) which is one best open-source database servers. It is available to install using the default system repository of Ubuntu 20.04. Hence, just run the given command:

Moodle uses a MySQL or MariaDB as a database backend so you will need to create a database and user for Moodle.

    > sudo apt install mariadb-server

Secure your Database server by setting up the root DB user password and removing anonymous rights.

    > sudo mysql_secure_installation

Here are the questions the setup will ask.

    #press the Enter key
    Enter current password for root (enter for none):
    #Press Y and type the password you want to set two times.
    Set root password? [Y/n] Y
    Remove anonymous users? [Y/n] Y
    Disallow root login remotely? [Y/n] Y
    Remove test database and access to it? [Y/n] Y
    Reload privilege tables now? [Y/n] Y

# Create a Database and user for Moodle

Once the installation is completed, let’s create a dedicated database for Moodle to use and save the data.

First, connect to the MySQL shell with the following command:

    > sudo mysql -u root -p

[//]: <> (> sudo mysql)

Use the password you have set for it in the previous step.

Once login, create a database and user with the following command:

    CREATE DATABASE moodledb;
    CREATE USER 'moodle'@'localhost' IDENTIFIED BY 'password';

Next, grant all the privileges to the Moodle database with the following command:

    GRANT ALL ON moodledb.* TO 'moodle'@'localhost' WITH GRANT OPTION;

Next, flush the privileges and exit from the MySQL with the following command:

    FLUSH PRIVILEGES;
    EXIT;

Next, edit the MariaDB default configuration file and define the innodb_file_format:

    > sudo nano /etc/mysql/mariadb.conf.d/50-server.cnf

Add the following lines inside the [mysqld] section:

    [mysqld]
    innodb_file_format = Barracuda
    innodb_file_per_table = 1
    innodb_large_prefix = ON

Save the file then restart the MariaDB service to apply the changes:

    > sudo systemctl restart mariadb

# Download Moodle 4.3

    cd /var/www/html

---

    wget https://download.moodle.org/download.php/direct/stable403/moodle-latest-403.tgz

Extract the download file

    tar xzf moodle-latest-403.tgz

# Moodle configuration

Once the download is completed, edit the Moodle config.php and define the database type:

    > sudo nano /var/www/html/moodle/config-dist.php

Find the following line:

    $CFG->dbtype    = 'mysqli';

And, replaced it with the following line:

    $CFG->dbtype    = 'mariadb';

Save and close the file then create a Moodle data directory set proper ownership and permission with the following command:

    > sudo mkdir -p /var/www/html/moodledata
    > sudo chown -R www-data:www-data /var/www/html/moodle
    > sudo chmod -R 755 /var/www/html/*
    > sudo chown www-data:www-data /var/www/html/moodledata

Once you are finished, you can proceed to the next step.

# Configure Nginx for Moodle

Next, you will need to create an Nginx virtual host configuration file to host Moodle:

    > sudo nano /etc/nginx/conf.d/moodle.conf

Add the following lines:

    server {
        listen 80;
        root /var/www/html/moodle;
        index  index.php index.html index.htm;
        server_name 192.168.0.17;

        client_max_body_size 100M;
        autoindex off;
        location / {
            try_files $uri $uri/ =404;
        }

        location /dataroot/ {
        internal;
        alias /var/www/html/moodledata/;
        }

        location ~ [^/].php(/|$) {
            include snippets/fastcgi-php.conf;
            fastcgi_pass unix:/run/php/php8.0-fpm.sock;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            include fastcgi_params;
        }
    }

Save and close the file then verify the Nginx for any syntax error with the following command:

    > sudo nginx -t

You should get the following output:

    nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
    nginx: configuration file /etc/nginx/nginx.conf test is successful

Finally, restart the Nginx service to apply the changes:

    > sudo systemctl restart nginx

# Access Moodle Web Interface

Now, open your web browser and access the Moodle web interface using the URL <192.168.0.17>. You should see the Moodle installation page:
