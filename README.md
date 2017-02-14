JrSilva - ThreeSoft Vagrant File
===================

[Vagrant][1] provides a simple, elegant way to manage and provision Virtual Machines and this is a *recommended* Vagrant setup to get loaded with core development tools to build a powerful PHP application.

## Overview

We use the default Ubuntu trusty64 ISO from Vagrant for compatibility.
If you choose to use a 64-bit ISO you may need to update your BIOS to enable [virtualization][12] with `AMD-V`, `Intel VT-x` or `VIA VT`.

When you provision Vagrant for the first time it's always the longest procedure (`$ vagrant up`). Vagrant will download the entire Linux OS if you've never used Vagrant or the ubuntu/trusty64 Box. Afterwards, booting time is fast.

By default this setup uses 2 GB. You can change this in `Vagrantfile` and simply run `$ vagrant reload`. You can also use more than one core if you like, simply uncomment these two lines in the same file:

```yaml
v.customize ["modifyvm", :id, "--cpus", "2"]
v.customize ["modifyvm", :id, "--ioapic", "on"]
```

## Packages Included

- LAMP Stack
  - Ubuntu 14.04.5 LTS
  - Apache 2.4.7
  - PHP 5.6.26
  - MySQL 5.6.33
- Git 1.9.1
- Memcached 1.4.14
- [Zephir][10] 0.9.4a-dev
- [SQLite][8] 3.8.2
- [PostgreSQL][9] 9.4.9
- [Redis][4] 3.0.7
- [MongoDB][5] 2.4.9
- [Composer][6] (latest stable)

## Requirements

- Operating System: Windows, Linux, or OSX.
- [Virtualbox][7] >= 4.3.10
- [Vagrant][1] >= 1.4.1

If you have issues with Windows and `vbguest` additions, use the following versions:
- Virtualbox version 4.2.*
- Vagrant 1.4.1

## Installation

First you need a [Git enabled terminal](#software-suggestions). Then you should **clone this repository** locally.

```sh
$ git clone https://github.com/jsiilva/js-vagrant.git
```

For newer versions of Vagrant and VirtualBox you may need **guest additions**, so install the plugin:

```sh
# For Linux/OSX
$ vagrant plugin install vagrant-vbguest

# For Windows
$ vagrant plugin install vagrant-windows
```

Now you are ready to provision your Virtual Machine, run:

```sh
$ vagrant up
```

The `Setup.sh` script will provision the system with everything needed. Take a look inside if you want to change any default settings. Once provisioned, to access the box, simply type:

```sh
$ vagrant ssh

# To exit type:
$ exit
```

If you want to change your bound address (`192.168.33.10`), edit `Vagrantfile`, change the ip and run:

```sh
$ vagrant reload
```

If you want to point your Guest Machine (The Virtual Machine OS) to a friendly URL, you could modify your `etc/hosts` file and add the following:

```
192.168.33.10  your-server-name
```

## Vagrant Credentials

These are credentials setup by default:

- **Host Address**: 192.168.33.10 _(Change in Vagrantfile if you like)_
- **SSH**: vagrant / vagrant _(If root password fails, run `$ sudo passwd` and set one)_
- **MySQL**: root / (none)
- **PostgreSQL**: postgres / postgres _(We have disabled the password prompt for `psql -U postgres` command)_
- **Redis**: (none)


## Create a VHost Record

You can have multiple Phalcon projects in subfolders. Make sure to keep your base VirtualHost enabled, in our case it's the `vagrant.conf` enabled by default. Then follow the instructions below and take note, you must include the `ServerPath /project/` in your VirtualHost's.

**Do not include a ServerPath for the base vagrant.conf VirtualHost.**

```sh
$ touch superstar.conf
```

Then include the following data. Notice the two directory paths with `superstar`:

```apache
<VirtualHost *:80>
    DocumentRoot /www/superstar/public
    ServerPath /superstar
</VirtualHost>

<Directory "/www/superstar/public">
    Options Indexes Followsymlinks
    AllowOverride All
    Require all granted
</Directory>
```

Next move your VirtualHost configuration file to sites-available in Apache:

```sh
$ sudo mv superstar.conf /etc/apache2/sites-available
```

Lastly, you must enable your configuration file and restart apache

```sh
$ sudo a2ensite superstar
$ sudo service apache2 reload
```

If you wanted to disable a site:

```sh
$ sudo a2dissite superstar
$ sudo service apache2 reload
```

You should be able to access the following URL's:

```
http://192.168.33.10/
http://192.168.33.10/superstar
```

## Local Editing

On your Host computer open any file explorer or IDE and navigate to `/www/`.
This folder is mounted to the Virtual Machine. Any changes to files within here will reflect realtime changes in the Virtual Machine.

If you are using .git you should initialize your repository locally rather than on the server. This way you will not have to import keys into your Virtual Machine.

## Using SSH

Files in the shared directory of `www` are by default given ownership of `vagrant:vagrant` so that you will have no problems with saving cached files.

## Troubleshooting Vagrant Ubuntu

If you are using Linux such as Ubuntu, you may have to set a different IP that doesn't interfere with DHCP in linux,
here is a safe bet:
- `192.168.33.10

If you are using the latest VirtualBox with Ubuntu 14, after installing guest additions (below), to fix the error message you will get due to a bug in the guest additions do the following after you run `$ vagrant up`.

```sh
$ vagrant ssh
$ sudo ln -s /opt/VBoxGuestAdditions-4.3.10/lib/VBoxGuestAdditions /usr/lib/VBoxGuestAdditions
$ vagrant reload
``

## Credits

Copyright (c) 2017, ThreeSoft App Labs Team

[1]: http://vagrantup.com/
[2]: http://redis.io/
[3]: https://www.mongodb.org/
[4]: https://getcomposer.org
[5]: https://www.virtualbox.org
[6]: https://www.sqlite.org/
[7]: http://www.postgresql.org/
[8]: http://zephir-lang.com/
[9]: http://kr.github.io/beanstalkd/
[10]: https://en.wikipedia.org/wiki/X86_virtualization
