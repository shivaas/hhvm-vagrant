HHVM Vagrant box
================

Ubuntu Precise box simply installing HHVM (nightly) via apt.

Requirements
------------

* [VirtualBox](https://www.virtualbox.org)
* [Vagrant](http://vagrantup.com)

Installation
------------

```bash
$ git clone https://github.com/vicb/hhvm-vagrant.git
$ cd hhvm-vagrant
$ vagrant up
```

Once the VM is booted you can log via SSH

```bash
$ vagrant ssh
```

The server root folder is `/vagrant/www` (on the map) which is the `www` folder
on your host machine.

- The HHVM server is available at `http://localhost:8080` on the host OS
- A MySQL server is installed. The root user is "root" with password "pa$$".

HHVM 3.0 Configuration
-------------------------

HHVM config files are in:
`/etc/hhvm/server.ini`

To start hhvm:
sudo service start hhvm. This will load the default config from server.ini

The access and error logs are available in `/var/log/hhvm`

```bash
$ vagrant ssh
$ tail -f /var/log/hhvm/access.log
$ tail -f /var/log/hhvm/error.log
```

WebServer
---------
With HHVM 3.0, the inbuilt webserver support has been deprecated. HHVM should be used under fastcgi mode which is automatically configured in `/etc/hhvm/server.ini`

Installing Nginx
```bash
$ sudo apt-get install nginx
```

Setting up Nginx with HHVM
If you installed Nginx after installing HHVM, it will automatically create a hhvm.conf file for you. You only need to create a site config for Nginx to load the config. This can be done by doing the following:
```bash
$ sudo vim /etc/nginx/sites-available/vagrant
```

```
//  File /etc/nginx/sites-available/vagrant
server {
    root /vagrant;
    index index.html index.htm index.php;

    server_name localhost; # 192.168.33.10.xip.io if you are using Vaprobash

    include hhvm.conf;  # Include HHVM's configuration file for Nginx

    location / {
        try_files $uri $uri/ /index.php?q=$uri&$args;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

```bash
sudo ln -s /etc/nginx/sites-available/vagrant /etc/nginx/sites-enabled/vagrant
```

Edit hhvm.conf and update fastcgi_param to point to your document root path on the vagrant box.
```
  fastcgi_param  SCRIPT_FILENAME /vagrant/www$fastcgi_script_name;
```

Start Nginx
```bash
$ sudo service nginx start
$ curl http://localhost/index.php
    It Works!
```

Virtual Machine Management
--------------------------

When done just log out with `^D` and suspend the virtual machine

```bash
$ vagrant suspend
```

then, resume to testing again

```bash
$ vagrant resume
```

Run

```bash
$ vagrant halt
```

to shutdown the virtual machine, and

```bash
$ vagrant up
```

to boot it again.

You can find out the state of a virtual machine anytime by invoking

```bash
$ vagrant status
```

Finally, to completely wipe the virtual machine from the disk **destroying all
its contents**:

```bash
$ vagrant destroy
```

Credits
-------

- https://github.com/adrienbrault/hhvm-vagrant
- https://github.com/javer/hhvm-vagrant-vm
