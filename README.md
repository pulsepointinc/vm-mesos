This project contains an ansible playbook and vagrant environment in which to run it. It spins up a six node mesos cluster with three masters (mm[1:3]) and three slaves (ms[1:3]).

It was built while following along with the early lessons in Mesosphere's [Advanced Mesos Course](https://open.mesosphere.com/intro-course/), ignorant of the [later lesson](https://open.mesosphere.com/intro-course/ex16.html) which walks through creating a playbook, and also ignorant of the [linked playbook](https://github.com/AnsibleShipyard/ansible-mesos) on Mesosphere's [tools page](https://open.mesosphere.com/getting-started/tools/).

Assuming you have virtualbox, vagrant, and ansible installed, `vagrant up` should just work. However, the following is a disclaimer describing my actual usage pattern.

My usage includes:

Using the [vagrant-hostsupdater plugin](https://github.com/cogitatio/vagrant-hostsupdater). This is a handy plugin that updates your machine's /etc/hosts file to include the name and IP of your VMs.

    vagrant plugin install vagrant-hostsupdater
    
This allows me to SSH to the mm and ms machines by hostname, which is done when provisioning directly. I also configure my ssh client ( through .ssh/config ) to use the vagrant creds.

    Host mm* ms*
        User vagrant
        IdentityFile ~/.vagrant.d/insecure_private_key

Spinning up the VMs, usually without provisioning.

    vagrant up --no-provision

I do this without provisioning largely because it's a bit faster to provision directly through ansible, if only because it runs each task across all four machines rather than running the playbook one machine at a time. It also serves as a proof point that the playbook should work in a non-vagrant environment.

Provision directly with:

    ansible-playbook -i provisioning/hosts -s provisioning/site.yml

And finally, start up the master node services.

    ansible-playbook -i provisioning/hosts -s provisioning/restart-mesos-master.yml 

The slave nodes are started during the original provisioning. We delay the master startup because the []Mesosphere quickstart](http://open.mesosphere.com/getting-started/datacenter/install/) states:

> You need to bring each service up on the set of master nodes at roughly the same time.

However, there is a playbook for slave restarts.

    ansible-playbook -i provisioning/hosts -s provisioning/restart-mesos-slave.yml

