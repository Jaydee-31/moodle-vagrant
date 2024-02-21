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

1. Clone this project: `git clone https://github.com/Jaydee-31/moodle-vagrant.git ~/vagrant`
2. Install recommended versions of [Vagrant and VirtualBox](https://developer.hashicorp.com/vagrant/install#windows).
3. Install Vagrant Host Manager Plugin: `vagrant plugin install vagrant-hostmanager`
4. Open your terminal and from within this project, run: `vagrant up`

After the `vagrant up` command completes, your virtual machine will be ready. You can use `vagrant ssh` to access it.
