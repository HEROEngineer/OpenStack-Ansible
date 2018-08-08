# OpenStack-ansible
Deploy OpenStack Queens By Ansible

Now only support one controller

Feature list:
* HA controller 

This ansible contains OpenStack service:
* keystone
* glance
* nova
* neutron (linuxbridge)
* hroizon
* cinder
* heat
* aodh
* gnocchi
* ceilometer

# Quick Start

Prepare ansible node and openstack node

## Ansible node

Install pip
<pre><code>
apt install python-pip
</code></pre>

Upgrade pip
<pre><code>
pip install -U pip
</code></pre>

Clone openstak-ansible
<pre><code>
git clone https://github.com/dommgifer/openstack-ansible.git
</code></pre>

Change workdir to openstack-ansible
<pre><code>
cd openstack-ansible
</code></pre>

Add the machine info gathered above into a file called inventory. For inventory example:
>[all] section define openstack node hostname and ip, ansible will auto change openstack node hostname

<pre><code>
[all]
controller    ansible_host=10.50.2.6
compute1      ansible_host=10.50.2.7
storage       ansible_host=10.50.2.8

[controller]
controller  ansible_ssh_user=localadmin ansible_become_pass=openstack

[compute]
compute1   ansible_ssh_user=localadmin ansible_become_pass=openstack

[storage]
storage   ansible_ssh_user=localadmin ansible_become_pass=openstack

[env:children]
controller
</code></pre>

Set the variables in **group_vars/all.yml** to reflect you need options. For example:
> openstack_vip_address is controller ip
<pre><code>
####################
# General options
####################
time_zone: "Asia/Taipei"
keystone_port: "5000"
keystone_admin_password: "openstack"
glance_user_password: "openstack"
nova_user_password: "openstack"
plancement_user_password: "openstack"
neutron_user_password: "openstack"
cinder_user_password: "openstack"
heat_user_password: "openstack"
aodh_user_password: "openstack"
gnocchi_user_password: "openstack"
ceilometer_user_password: "openstack"
####################
# RabbitMQ options
####################
rabbitmq_user: "openstack"
rabbitmq_password: "openstack"
####################
# Networking options
####################
network_manage_interface: "ens3"
network_public_interface: "ens3"

openstack_vip_address: "10.50.2.6"
################
# Chrony options
################
external_ntp_servers: "tock.stdtime.gov.tw"

################
# Cinder options
################
lvm_physical_volume: "vdb"

####################
# Database options
####################
mysql_root_password: "openstack"
keystone_db_password: "openstack"
glance_db_password: "openstack"
nova_db_password: "openstack"
neutron_db_password: "openstack"
cinder_db_password: "openstack"
heat_db_password: "openstack"
aodh_db_password: "openstack"
gnocchi_db_password: "openstack"
####################
# Grafana options
####################
grafana_port: "3000"
</code></pre>

Change your neutron_metada_secret in **group_vars/env.yml**
<code><pre>
metadata_secret: your_secret
</pre></code>

Copy ssh key to all openstack node
<pre><code>
ssh-copy-id 10.50.2.6
ssh-copy-id 10.50.2.7
ssh-copy-id 10.50.2.8
</code></pre>

Run bootstrap-ubuntu-16.04.yml install python2.7 to openstack node
<pre><code>
ansible-playbook -i inventory bootstrap-ubuntu-16.04.yml
</code></pre>

Run deploy.yml to install OpenStack
<pre><code>
ansible-playbook -i inventory deploy.yml
</code></pre>
