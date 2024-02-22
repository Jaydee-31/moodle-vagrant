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

# Vagrant updates

Edit the php.ini file and change some settings:

    > sudo nano /etc/php/8.0/fpm/php.ini

Change the following lines:

    memory_limit = 256M
    cgi.fix_pathinfo = 0
    upload_max_filesize = 100M
    max_execution_time = 360
    date.timezone = Asia/Singapore
    max_input_vars = 5000

Save and close the file then restart the PHP-FPM service to apply the changes:

    > sudo systemctl restart php8.0-fpm

Once you are finished, you can proceed to the next step.

# Create the database

Moodle uses a MySQL or MariaDB as a database backend so you will need to create a database and user for Moodle.

First, connect to the MySQL shell with the following command:

    > sudo mysql

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

# Moodle configuration

Edit the Moodle config.php and define the database type:

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
