#
# Based on http://www.giantflyingsaucer.com/blog/?p=5923
# Before running this script make sure you have installed the vbguest via
# vagrant plugin install vagrant-vbguest
# Source: https://github.com/dotless-de/vagrant-vbguest
#
# To access the machine via SSH (Ansible) you first have to copy the public key
# to the machines via
# ssh-copy-id -i ~/.ssh/id_rsa.pub vagrant@192.168.99.100
# default password is "vagrant"
#
ENV["LC_ALL"] = "en_US.UTF-8"

$script = <<SCRIPT
echo "Installing Docker..."
apt-get update
apt-get install curl -y
curl -fsSL https://test.docker.com/ | sh
echo "Completed Installing Docker"
SCRIPT

Vagrant.configure('2') do |config|

  vm_box = 'ubuntu/trusty64'

  N = 3
  (1..N).each do |machine_id|
    config.vm.define "machine#{machine_id}" do |machine|
      machine.vm.box = vm_box
      machine.vm.box_check_update = true
      machine.vm.network "private_network", ip: "192.168.99.#{99+machine_id}"
      machine.vm.hostname = "machine#{machine_id}"
      machine.vm.provision "shell", inline: $script, privileged: true
      config.vbguest.auto_update = true
      config.vbguest.no_remote = true
    end
  end
end
