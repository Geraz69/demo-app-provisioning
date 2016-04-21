# -*- mode: ruby -*-
# vi: set ft=ruby :

# Ansible is generally installed by Vagrant itself, but there is a bug in the latest version of Vagrant (1.8.1)
# which prevents the installation and validation of Ansible. Until next version is available this script and the
# shell provisioning down below will successfully install Ansible on Vagrant

$install_ansible = <<SCRIPT
sudo apt-get update -y -qq
sudo apt-get install -y -qq software-properties-common python-software-properties
sudo add-apt-repository ppa:ansible/ansible -y
sudo apt-get update -y -qq
sudo apt-get install -y -qq ansible
SCRIPT

Vagrant.configure(2) do |config|

  config.vm.box = "precise64"
  config.vm.box_url = "http://files.vagrantup.com/precise64.box"

  config.vm.network "forwarded_port", guest: 80, host: 7080
  config.vm.network "forwarded_port", guest: 443, host: 7443

  # Patch for https://github.com/mitchellh/vagrant/issues/6793
  config.ssh.shell = "bash -c 'BASH_ENV=/etc/profile exec bash'"
  config.vm.provision 'shell', inline: $install_ansible
  config.vm.provision "shell" do |s|
    s.inline = '[[ ! -f $1 ]] || grep -F -q "$2" $1 || sed -i "/__main__/a \\    $2" $1'
    s.args = ['/usr/bin/ansible-galaxy', "if sys.argv == ['/usr/bin/ansible-galaxy', '--help']: sys.argv.insert(1, 'info')"]
  end

  config.vm.provision "ansible_local" do |ansible|
    ansible.playbook = "playbook.yml"
  end

end
