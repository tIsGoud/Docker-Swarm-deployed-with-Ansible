# Ansible playbooks to deploy Docker Swarm

Depending on the inventory file Docker Swarm is deployed on a two- or six-node Raspberry Pi stack or on Virtual Machines deployed with Vagrant.

This project is based on the playbooks written for the Docker Grunn MeetUp. After putting the first version online I read about the Ansible "best practices" and felt the need to make some improvements. You could call this a version 2.0, new and improved ...

Vagrant machines:

| Machine name | IP address |
|:--- | :--- |
| machine1 | 192.168.99.100 |
| machine2 | 192.168.99.101 |
| machine3 | 192.168.99.102 |

The Docker images still use DHCP.

This readme file is still work-in-progress, more information will be added soon.

Install the Vagrant plugin:
vagrant plugin install vagrant-vbguest
