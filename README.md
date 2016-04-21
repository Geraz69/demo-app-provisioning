# Provisioning for demo app
<!-- If you'd like to use a logo instead uncomment this code and remove the text above this line

  ![Logo](URL to logo img file goes here)

-->

By [Gerardo Garcia Mendez](https://twitter.com/Geraz69).

## Prerequirements

To run this in your machine you need to have Virtualbox and Vagrant installed. If you happen to use Mac OSX you can use [Homebrew](http://brew.sh/) to install Vagrant and Virtual box with the next commands:

```bash
$ brew cask install virtualbox
$ brew cask install vagrant
$ brew cask install vagrant-manager
```
Otherwise you would needs to install it following this [instructions](https://www.vagrantup.com/docs/installation/).

When that is done, download this repo and go to the repo root.

## Usage

After that is just a matter of telling Vagrant to create the instance and provision it. The necessary image will be downloaded automatically.

```bash
$ vagrant up
```

As soon as the command is finished you can go and check that everything is working, you can go and hit http://localhost:7080 or https://localhost:7443 on a web browser. "Hello World".

## Port Forwarding

The demo app runing in the VM is listening on port 8080, but only port 80 and 443 are open to the outside, an NGINX server is listening in those ports and internaly redirecting all the requests to the demo app, with port 443 enforcing ssl conection with a self signed certificate.

On top of that, Vagrant does a port forwarding on its own, it is configured to forward host port 7080 to guest port 80 and host port 7443 to guest port 443, which is in turn forwarded to port 8080 by NGINX. You can change it in the Vagrant file in the root of the repo.

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

demo-app is not a trusted source, so we run it with the user "demo-app" with no provileges.

App logs are rotated in place every 500MB. Compresion and backup can be added in the supervisord config file for the demo-app. Nginx access logs are not rotated (future enhancement).

Firewall management is provided by UFW tool for ubuntu.

