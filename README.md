# OpenStack-ansible
Deploy OpenStack Queens By Ansible

Feature list:
* ELK

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
apt update -y
apt install python-pip python-netaddr -y
</code></pre>

Install and upgrade ansible
<pre><code>
pip install ansible
pip install -U ansible
</pre></code>

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

> [env:children] section don't modify, controller mean [controller], not your controller hostname

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

Login in horizon:
<pre><code>
http://openstack_vip_address/hroizon
</code></pre>

Login in grafana:
<pre><code>
http://openstack_vip_address:grafana_port
</code></pre>

# Deploy controller HA
Add the machine info gathered above into a file called inventory. For inventory example:
<pre><code>
[all]
controller1    ansible_host=10.50.2.4
controller2    ansible_host=10.50.2.5
controller3    ansible_host=10.50.2.6
compute1      ansible_host=10.50.2.7
compute2      ansible_host=10.50.2.13
storage       ansible_host=10.50.2.8

[controller]
controller1  ansible_ssh_user=localadmin ansible_become_pass=openstack
controller2  ansible_ssh_user=localadmin ansible_become_pass=openstack
controller3  ansible_ssh_user=localadmin ansible_become_pass=openstack

[compute]
compute1   ansible_ssh_user=localadmin ansible_become_pass=openstack
compute2   ansible_ssh_user=localadmin ansible_become_pass=openstack

[storage]
storage   ansible_ssh_user=localadmin ansible_become_pass=openstack

[env:children]
controller
</code></pre>

Set the variables in **group_vars/all.yml** to reflect you need options. For example:
> Please prepare NFS server, and settting up NFS server mounted directory.

> NFS server store glance image and gnocchi data.

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
network_manage_interface: "ens9"
network_public_interface: "ens3"

openstack_vip_address: "10.50.2.3"
openstack_vip_hostname: "controller"

################
# Chrony options
################
external_ntp_servers: "tock.stdtime.gov.tw"

########################
# Glance options
########################
enable_glance_nfs: "yes"
nfs_src_path: "/home/localadmin/images"

################
# Cinder options
################
lvm_physical_volume: "vdb"

################
# Gnocchi options
################
nfs_src_gnocchi_path: "/home/localadmin/gnocchi"

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
grafana_admin_password: "openstack"
grafana_db_password: "openstack"
grafana_port: "3000"

####################
# HAproxy options
####################
clustercheck_password: "openstack"
haproxy_state_password: "openstack"

####################
# NFS options
####################
nfs_server_ip: "10.50.2.12"

</code></pre>

Copy ssh key to all openstack node
<pre><code>
ssh-copy-id 10.50.2.4
ssh-copy-id 10.50.2.5
ssh-copy-id 10.50.2.6
........
</code></pre>

Run bootstrap-ubuntu-16.04.yml install python2.7 to openstack node
<pre><code>
ansible-playbook -i inventory bootstrap-ubuntu-16.04.yml
</code></pre>

Run ha.yml to install OpenStack
<pre><code>
ansible-playbook -i inventory ha.yml
</code></pre>

Login in horizon:
<pre><code>
http://openstack_vip_address/hroizon
</code></pre>

Login in grafana:
<pre><code>
http://openstack_vip_address:grafana_port
</code></pre>
