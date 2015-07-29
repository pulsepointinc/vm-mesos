This project contains an ansible playbook and vagrant environment in which to run it. It spins up a six node mesos cluster with three masters (mm[1:3]) and three slaves (ms[1:3]).

It was built while following along with the early lessons in Mesosphere's [Advanced Mesos Course](https://open.mesosphere.com/intro-course/), ignorant of the [later lesson](https://open.mesosphere.com/intro-course/ex16.html) which walks through creating a playbook, and also ignorant of the [linked playbook](https://github.com/AnsibleShipyard/ansible-mesos) on Mesosphere's [tools page](https://open.mesosphere.com/getting-started/tools/).

## Usage

Install virtualbox, vagrant, and ansible.

Install the [vagrant-hostsupdater plugin](https://github.com/cogitatio/vagrant-hostsupdater). This is a handy plugin that updates your machine's /etc/hosts file to include the name and IP of your VMs.

    vagrant plugin install vagrant-hostsupdater
    
This allows you to SSH to the VMs by hostname. You can also configure your ssh client ( through .ssh/config ) to use the vagrant creds.

    Host mm* ms*
        User vagrant
        IdentityFile ~/.vagrant.d/insecure_private_key

Spin up the VMs:

    vagrant up

This will provision the machines using two playbooks, a vm.yml playbook that hacks the hosts file, and the site.yml playbook that actually installs and configures mesos.

Finally, start up the master node services.

    ansible-playbook -i provisioning/hosts -s provisioning/restart-mesos-master.yml 

Note that the ssh user and private key aren't specified here, as we set those in .ssh/config.

The slave nodes are started during the original provisioning. We delay the master startup because the [Mesosphere quickstart](http://open.mesosphere.com/getting-started/datacenter/install/) states:

> You need to bring each service up on the set of master nodes at roughly the same time.

There is also a playbook for slave restarts.

    ansible-playbook -i provisioning/hosts -s provisioning/restart-mesos-slave.yml

