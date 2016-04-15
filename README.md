# Provisioning for demo app
<!-- If you'd like to use a logo instead uncomment this code and remove the text above this line

  ![Logo](URL to logo img file goes here)

-->

By [Gerardo Garcia Mendez](https://twitter.com/Geraz69).

## Prerequirements

To run this in your machine you need to have Virtualbox and Vagrant installed. For Mac OSX you can do it with the next commands:

```bash
$ brew cask install virtualbox
$ brew cask install vagrant
$ brew cask install vagrant-manager
```

## Installation

Download this repo and setup Vagrant. The targeted base image is precise64, so you need to add it to your Vagrant setup like this:


```bash
$ cd demo-app-provisioning
$ vagrant box add precise64 http://files.vagrantup.com/precise64.box
```

## Usage

After that is just a matter of telling Vagrant to create the instance and provision it:

```bash
$ vagrant up
```

As soon as the command is finished you can go and check that everything is working, you can go and hit http://127.0.0.1:1234 on a web browser. "Hello World".

## Port Forwarding

The demo app runing in the VM is listening on port 8080, but only port 80 is open to the outside, an NGINX server is listening in port 80 and internaly redirecting all the requests to the demo app.

On top of that, Vagrant does a port forwarding on its own, it is configured to be port 1234, in the host machine, which is in turn forwarded to port 80 in the guest. You can change it in the Vagrant file in the root of the repo.

## Management

If you want to run the provisioning again just run:

```bash
$ vagrant provision
```

Vagrant creates instances with a completely new set of SSH keys. which means that the instance is only available to anyone that has access to the working directory (the key is stored under the hidden folder .vagrant). Using this key you can do passwordless login:

```bash
$ vagrant ssh
```

Once inside the instance you can:

```bash
# check the app logs
$ sudo tail /var/log/demo-app/demo-app.log

# check access logs
$ sudo tail /var/log/nginx/access.log

# start/stop/restart the app
$ sudo supervisorctl start demo-app
$ sudo supervisorctl stop demo-app
$ sudo supervisorctl restart demo-app

```

App logs are rotated in place every 500MB. Compresion and backup can be added in the supervisord config file for the demo-app. Nginx access logs are not rotated (future enhancement).

Firewall management is provided by UFW tool for ubuntu.

## License

GNU GPL lincence applies.