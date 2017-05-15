# Ansible playbooks to deploy Docker Swarm

Depending on the inventory file Docker Swarm is deployed on a two- or six-node Raspberry Pi stack or on Virtual Machines deployed with Vagrant.

This project is based on the playbooks written for the Docker Grunn MeetUp. After putting the first version online I read about the Ansible "best practices" and felt the need to make some improvements. You could call this a version 2.0, new and improved ...


## Multi deployment
The Docker Swarm has been tested on three different configurations:
- 6-node Raspberry Pi v3 Cluster
- 2-node Raspberry Pi v2 Cluster
- 3-node Vagrant VM Cluster

The setup of the Ansible workbooks is very flexible and can be used for all three scenarios.
Based on the processor architecture the right images are loaded. This is done in the first task in the file.

Architecture specific services are loaded on the swarm cluster, what services are loaded is configured in the processor specific section in the file group_vars/all.


## RGB-led
In my Raspberry Pi setup I use an RGB-led to show the different states of the swarm nodes.   


## Requirements for the physical stack

The physical stack was tested with two(v2) and six(v3) Pi's. Inventory files for the two and six node setup are included in this release.
Other numbers should work just fine but don't forget to change the inventory file.

Requirements for the physical stack:
- Requires six (inventory_6stack) or two (inventory_2stack) Raspberry Pi devices
- Requires six or two micro-SD cards (8GB)
- Requires Ansible (used with 2.2.0.0)
- Requires [Hypriot flash tool](https://github.com/hypriot/flash)
- Requires [HypriotOS](https://blog.hypriot.com/downloads/) (used with 1.4.0)
- Requires HypriotOS installed on the micro-SD cards
- Requires hostnames configured on the micro-SD cards matching the names in the inventory file

- Expects the six or two Raspberry Pi devices powered on and connected on the same network as the machine running Ansible
- Expects an RGB Common Cathode led connected to the GPIO pins. Red connected to pin 15 (GPIO22), green connected to pin 13 (GPIO13), blue connected to pin 11 (GPIO17) and the ground connected to pin 9. If you have a Common Anode RGB led or a different pin setup change the values for ledOn and ledOff or GPIO pins in the file: roles/gpio/vars/gpio.yml.

Note: If you do not have the leds installed you will miss out on the visuals but the playbooks should work fine.


### Preparing the micro-SD cards

Download [HypriotOS](https://blog.hypriot.com/downloads/) and instal the [Hypriot flash tool](https://github.com/hypriot/flash).
Install HypriotOS with the Hypriot flash tool on the micro-SD cards:

 		flash -hostname [name] hypriotos-rpi-v1.1.3.img.zip

Use the name(s) of the swarmManager and swarmNodes from the inventory file


### Set up SSH

If you use homebrew then follow the steps below otherwise follow the instructions on [How to set up SSH Keys](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys--2)

Install ssh-copy-id with brew:

		brew install ssh-copy-id

Repeat for each node:

		ssh-copy-id -i ~/.ssh/id_rsa.pub pirate@[name]

Use the names from the inventory file.

In my test setup I frequently re-created the whole setup. The ssh-copy-id command adds the name of the nodes to the file ~/.ssh/known_hosts. This can result in very serious warnings. Don't panic, just inspect your known_hosts file and remove the conflicting lines.


## Vagrant preparations

Vagrant machines:

| Machine name | IP address |
|:--- | :--- |
| machine1 | 192.168.99.100 |
| machine2 | 192.168.99.101 |
| machine3 | 192.168.99.102 |

The Docker images still use DHCP.

Install the Vagrant plugin:
vagrant plugin install vagrant-vbguest


### Running the cluster

Starting up the cluster:

		ansible-playbook -i inventory site.yml

The first time this playbook runs on the cluster you have to be patient. The Docker images are downloaded in the background. The visualization image takes quit some time. The next time the startup time of the cluster will be much faster.

The RGB-leds indicate the function of a cluster node:

- Red: GPIO initialized
- Blue: Swarm manager
- Green: Swarm node

The ansible playbook starts two services:

- whoami
- busybox

By default each service is started twice per node.

To visualize the running instances per node and the running services a default browser will be started on the host from where Ansible is run. This result in a browser with three tabs, the first tab displays the platform specific [Docker Swarm Visualizer](https://github.com/dockersamples/docker-swarm-visualizer) (configured in group_vars/all). The next two tabs will display the running services.


### Playing around with the number of Docker Swarm replicas

Initially two instances of a service are running per node.

Adding more instances of the services (replicas):

		docker service update whoami --replicas 18
		docker service update busybox --replicas 18

Check the visualization page to see the additional instances running.

Removing replicas:

		docker service update whoami --replicas 6
		docker service update busybox --replicas 6

Again check the visualization page to see the changed number of running service instances.


### Teardown

To shutdown the cluster gracefully:

		ansible-playbook -i inventory siteTeardown.yml

The only remaining manual step is to close the three browser tabs.


### Note

This readme file is still work-in-progress, more information will be added.
