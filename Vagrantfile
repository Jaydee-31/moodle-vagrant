# Vagrant configuration version
Vagrant.configure("2") do |config|

# Hostname of VM
config.vm.hostname = "server"
# Box to be installed in VM
config.vm.box = "ubuntu/focal64"

# Network configuration
config.vm.network :private_network, ip: "192.168.0.17"
config.vm.network "forwarded_port", guest:80, host: 8080, auto_correct: true

# Provider to be used for VM
config.vm.provider :virtualbox do |vb|
	# VM name
	vb.name = "Moodle_403"
	# VM memory (1 GB)
	vb.memory = "1024"
	# VM cpu
	vb.cpus = 1
end

# Provisioning scripts
# config.vm.provision "shell", inline: <<-SHELL
# 	# Update & install base packages
# 	sudo apt update -y

# 	# Install Nginx and PHP
# 	sudo apt install -y nginx
# 	sudo apt install -y software-properties-common
# 	sudo add-apt-repository ppa:ondrej/php
# 	sudo apt update
# 	sudo apt install -y php8.0-fpm php8.0-mysql php8.0-mbstring php8.0-curl php8.0-tokenizer php8.0-xmlrpc php8.0-soap php8.0-zip php8.0-gd php8.0-xml php8.0-intl


# 	# Install MariaDB
# 	sudo apt-key adv --fetch-keys 'https://mariadb.org/mariadb_release_signing_key.asc'
# 	sudo add-apt-repository 'deb [arch=amd64,arm64,ppc64el] http://nyc2.mirrors.digitalocean.com/mariadb/repo/10.6/ubuntu focal main'
# 	sudo apt update
# 	sudo apt install -y mariadb-server

# 	# Secure MariaDB installation
# 	sudo mysql_secure_installation

# 	# Configure Nginx for PHP
# 	sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/default.bak
# 	sudo sed -i 's/\(index index.html\)/\1 index.php/' /etc/nginx/sites-available/default
# 	sudo sed -i 's/\(server_name _;\)/\1\n\tlocation ~ \\\.php$ {\n\t\tinclude snippets\/fastcgi-php.conf;\n\t\tfastcgi_pass unix:\/var\/run\/php\/php8.0-fpm.sock;\n\t}/' /etc/nginx/sites-available/default

# 	# Enable and restart Nginx
# 	sudo systemctl enable nginx
# 	sudo systemctl restart nginx

# 	# Enable and restart PHP-FPM
# 	sudo systemctl enable php8.0-fpm
# 	sudo systemctl restart php8.0-fpm

# 	# Create MariaDB database and user
# 	# mysql -u root -p -e "CREATE DATABASE moodledb;"
# 	# mysql -u root -p -e "CREATE USER 'moodle'@'localhost' IDENTIFIED BY 'password';"
# 	# mysql -u root -p -e "GRANT ALL ON moodledb.* TO 'moodle'@'localhost' WITH GRANT OPTION;"
# 	# mysql -u root -p -e "FLUSH PRIVILEGES;"
# 	# mysql -u root -p -e "EXIT;"
	

# 	# Download and extract Moodle
# 	# # wget https://download.moodle.org/stable/moodle-4.3.3.tgz
# 	# wget https://download.moodle.org/download.php/direct/stable403/moodle-latest-403.tgz
# 	# tar xzf moodle-latest-403.tgz
# 	# sudo git clone -b MOODLE_403_STABLE git://git.moodle.org/moodle.git moodle
# 	# mv moodle /var/www/html/

# SHELL

# Configuration
#config.vm.provision :shell, :path => "bootstrap.sh"
# Sync folder from host to guest machine
config.vm.synced_folder ".", "/vagrant"

end
