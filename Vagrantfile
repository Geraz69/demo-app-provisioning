# -*- mode: ruby -*-
# vi: set ft=ruby :

$install_ansible = <<SCRIPT
sudo apt-get update -y -qq
sudo apt-get install -y -qq software-properties-common python-software-properties
sudo add-apt-repository ppa:ansible/ansible -y
sudo apt-get update -y -qq
sudo apt-get install -y -qq ansible
SCRIPT

Vagrant.configure(2) do |config|
  config.vm.box = "precise64"

  config.vm.network "forwarded_port", guest: 80, host: 1234

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
