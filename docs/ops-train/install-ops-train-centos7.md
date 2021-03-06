# Hưỡng dẫn cài Openstack Train với CentOS 7
---
## Phần 1: Chuẩn bị

THIẾU CẤU HÌNH OSLO MESSAGING

### Mô hình

controller01
- vlan mgnt: eth0: 10.10.11.87
- vlan provider: eth1: 10.10.12.87
- vlan datavm: eth2: 10.10.14.87
- vlan cephcom: eth3: 10.10.15.87

compute01
- vlan mgnt: eth0: 10.10.11.88
- vlan provider: eth1: 10.10.12.88
- vlan datavm: eth2: 10.10.14.88
- vlan cephcom: eth3: 10.10.15.88

compute02
- vlan mgnt: eth0: 10.10.11.89
- vlan provider: eth1: 10.10.12.89
- vlan datavm: eth2: 10.10.14.89
- vlan cephcom: eth3: 10.10.15.89

### CTL 1

```
yum install -y epel-release
yum install -y wget byobu git vim

hostnamectl set-hostname ctl01

echo "Setup IP eth0"
nmcli c modify eth0 ipv4.addresses 10.10.11.87/24
nmcli c modify eth0 ipv4.gateway 10.10.11.1
nmcli c modify eth0 ipv4.dns 8.8.8.8
nmcli c modify eth0 ipv4.method manual
nmcli con mod eth0 connection.autoconnect yes

echo "Setup IP eth1"
nmcli c modify eth1 ipv4.addresses 10.10.12.87/24
nmcli c modify eth1 ipv4.method manual
nmcli con mod eth1 connection.autoconnect yes

echo "Setup IP eth2"
nmcli c modify eth2 ipv4.addresses 10.10.14.87/24
nmcli c modify eth2 ipv4.method manual
nmcli con mod eth2 connection.autoconnect yes

systemctl disable NetworkManager
systemctl stop NetworkManager
systemctl start network
systemctl enable network

sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
systemctl stop firewalld
systemctl disable firewalld

curl -Lso- https://raw.githubusercontent.com/nhanhoadocs/scripts/master/Utilities/cmdlog.sh | bash

timedatectl set-timezone Asia/Ho_Chi_Minh
yum -y install chrony
systemctl enable chronyd.service
systemctl restart chronyd.service
chronyc sources

init 6
```

echo "10.10.11.87 ctl01" >> /etc/hosts
echo "10.10.11.88 com01" >> /etc/hosts
echo "10.10.11.89 com02" >> /etc/hosts

echo 'net.ipv4.conf.all.arp_ignore = 1'  >> /etc/sysctl.conf
echo 'net.ipv4.conf.all.arp_announce = 2'  >> /etc/sysctl.conf
echo 'net.ipv4.conf.all.rp_filter = 2'  >> /etc/sysctl.conf
echo 'net.netfilter.nf_conntrack_tcp_be_liberal = 1'  >> /etc/sysctl.conf

cat << EOF >> /etc/sysctl.conf
net.ipv4.ip_nonlocal_bind = 1
net.ipv4.tcp_keepalive_time = 6
net.ipv4.tcp_keepalive_intvl = 3
net.ipv4.tcp_keepalive_probes = 6
net.ipv4.ip_forward = 1
net.ipv4.conf.all.rp_filter = 0
net.ipv4.conf.default.rp_filter = 0
EOF
sysctl -p

### COM1

```
yum install -y epel-release
yum install -y wget byobu git vim

hostnamectl set-hostname com01

echo "Setup IP eth0"
nmcli c modify eth0 ipv4.addresses 10.10.11.88/24
nmcli c modify eth0 ipv4.gateway 10.10.11.1
nmcli c modify eth0 ipv4.dns 8.8.8.8
nmcli c modify eth0 ipv4.method manual
nmcli con mod eth0 connection.autoconnect yes

echo "Setup IP eth1"
nmcli c modify eth1 ipv4.addresses 10.10.12.88/24
nmcli c modify eth1 ipv4.method manual
nmcli con mod eth1 connection.autoconnect yes

echo "Setup IP eth2"
nmcli c modify eth2 ipv4.addresses 10.10.14.88/24
nmcli c modify eth2 ipv4.method manual
nmcli con mod eth2 connection.autoconnect yes

systemctl disable NetworkManager
systemctl stop NetworkManager
systemctl start network
systemctl enable network

sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
systemctl stop firewalld
systemctl disable firewalld

curl -Lso- https://raw.githubusercontent.com/nhanhoadocs/scripts/master/Utilities/cmdlog.sh | bash

timedatectl set-timezone Asia/Ho_Chi_Minh
yum -y install chrony
systemctl enable chronyd.service
systemctl restart chronyd.service
chronyc sources

init 6
```

echo "10.10.11.87 ctl01" >> /etc/hosts
echo "10.10.11.88 com01" >> /etc/hosts
echo "10.10.11.89 com02" >> /etc/hosts


### COM2

```
yum install -y epel-release
yum install -y wget byobu git vim

hostnamectl set-hostname com02

echo "Setup IP eth0"
nmcli c modify eth0 ipv4.addresses 10.10.11.89/24
nmcli c modify eth0 ipv4.gateway 10.10.11.1
nmcli c modify eth0 ipv4.dns 8.8.8.8
nmcli c modify eth0 ipv4.method manual
nmcli con mod eth0 connection.autoconnect yes

echo "Setup IP eth1"
nmcli c modify eth1 ipv4.addresses 10.10.12.89/24
nmcli c modify eth1 ipv4.method manual
nmcli con mod eth1 connection.autoconnect yes

echo "Setup IP eth2"
nmcli c modify eth2 ipv4.addresses 10.10.14.89/24
nmcli c modify eth2 ipv4.method manual
nmcli con mod eth2 connection.autoconnect yes

sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
systemctl stop firewalld
systemctl disable firewalld

systemctl disable NetworkManager
systemctl stop NetworkManager
systemctl start network
systemctl enable network

curl -Lso- https://raw.githubusercontent.com/nhanhoadocs/scripts/master/Utilities/cmdlog.sh | bash

timedatectl set-timezone Asia/Ho_Chi_Minh
yum -y install chrony
systemctl enable chronyd.service
systemctl restart chronyd.service
chronyc sources

init 6
```

echo "10.10.11.87 ctl01" >> /etc/hosts
echo "10.10.11.88 com01" >> /etc/hosts
echo "10.10.11.89 com02" >> /etc/hosts

## Phần 2: Cài đặt MariaDB
> Thực hiện trên CTL

echo '[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/10.2/centos7-amd64
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1' >> /etc/yum.repos.d/MariaDB.repo

yum -y install mariadb mariadb-server python2-PyMySQL

cat << EOF >> /etc/my.cnf.d/openstack.cnf
[mysqld]
bind-address = 10.10.11.87

default-storage-engine = innodb
innodb_file_per_table = on
max_connections = 4096
collation-server = utf8_general_ci
character-set-server = utf8
EOF

systemctl enable mariadb.service
systemctl start mariadb.service


[root@ctl01 ~]# mysql_secure_installation

NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, we'll need the current
password for the root user.  If you've just installed MariaDB, and
you haven't set the root password yet, the password will be blank,
so you should just press enter here.

Enter current password for root (enter for none): 
OK, successfully used password, moving on...

Setting the root password ensures that nobody can log into the MariaDB
root user without the proper authorisation.

Set root password? [Y/n] y
New password: Welcome123
Re-enter new password: Welcome123
Password updated successfully!
Reloading privilege tables..
 ... Success!


By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n] y
 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] y
 ... Success!

By default, MariaDB comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n] y
 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] y
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!


## Phần 3: Cài đặt Memcache

yum -y install memcached python-memcached
cp /etc/sysconfig/memcached /etc/sysconfig/memcached.bak

sed -i "s/-l 127.0.0.1,::1/-l 127.0.0.1,::1,10.10.11.87/g" /etc/sysconfig/memcached

systemctl enable memcached.service
systemctl restart memcached.service

ss -atun | grep 11211

## Phần 4: Cài đặt MariaDB

yum -y install rabbitmq-server

systemctl enable rabbitmq-server.service
systemctl start rabbitmq-server.service

rabbitmq-plugins enable rabbitmq_management

systemctl restart rabbitmq-server.service

curl -O http://localhost:15672/cli/rabbitmqadmin

chmod a+x rabbitmqadmin

mv rabbitmqadmin /usr/sbin/

rabbitmqctl add_user openstack Welcome123
rabbitmqctl set_permissions openstack ".*" ".*" ".*"
rabbitmqctl set_user_tags openstack administrator

rabbitmqadmin list users

## Phần 5: Chuẩn bị môi trường
> Thực hiện trên CTL và COM

yum install -y centos-release-openstack-train \
   open-vm-tools python2-PyMySQL vim telnet wget curl
yum install -y python-openstackclient openstack-selinux
yum upgrade -y

Lưu ý:
- snapshot preops

## Phần 6: Cài đặt Etcd

> Thực hiện trên CTL

yum -y install etcd
cp /etc/etcd/etcd.conf /etc/etcd/etcd.conf.bak
rm -rf /etc/etcd/etcd.conf

cat << EOF >> /etc/etcd/etcd.conf
ETCD_DATA_DIR="/var/lib/etcd/default.etcd"
ETCD_LISTEN_PEER_URLS="http://10.10.11.87:2380"
ETCD_LISTEN_CLIENT_URLS="http://10.10.11.87:2379"
ETCD_NAME="ctl01"

ETCD_INITIAL_ADVERTISE_PEER_URLS="http://10.10.11.87:2380"
ETCD_ADVERTISE_CLIENT_URLS="http://10.10.11.87:2379"
ETCD_INITIAL_CLUSTER="ctl01=http://10.10.11.87:2380"
ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster-01"
ETCD_INITIAL_CLUSTER_STATE="new"
EOF

systemctl enable etcd
systemctl start etcd
systemctl status etcd

## Phần 7: Cài đặt Keystone
> Thực hiện trên CTL

mysql -uroot -pWelcome123
CREATE DATABASE keystone;
GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'localhost'  IDENTIFIED BY 'Welcome123';
GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'%' IDENTIFIED BY 'Welcome123';
FLUSH PRIVILEGES;
exit;

yum install openstack-keystone httpd mod_wsgi -y

mv /etc/keystone/keystone.{conf,conf.bk}
rm -rf /etc/etcd/etcd.conf


cat << EOF >> /etc/keystone/keystone.conf
[DEFAULT]
[application_credential]
[assignment]
[auth]
[cache]
[catalog]
[cors]
[credential]
[database]
connection = mysql+pymysql://keystone:Welcome123@10.10.11.87/keystone
[domain_config]
[endpoint_filter]
[endpoint_policy]
[eventlet_server]
[federation]
[fernet_receipts]
[fernet_tokens]
[healthcheck]
[identity]
[identity_mapping]
[jwt_tokens]
[ldap]
[memcache]
[oauth1]
[oslo_messaging_amqp]
[oslo_messaging_kafka]
[oslo_messaging_notifications]
[oslo_messaging_rabbit]
[oslo_middleware]
[oslo_policy]
[policy]
[profiler]
[receipt]
[resource]
[revoke]
[role]
[saml]
[security_compliance]
[shadow_users]
[token]
provider = fernet
[tokenless_auth]
[totp]
[trust]
[unified_limit]
[wsgi]
EOF

chown root:keystone /etc/keystone/keystone.conf

su -s /bin/sh -c "keystone-manage db_sync" keystone

keystone-manage fernet_setup --keystone-user keystone --keystone-group keystone
keystone-manage credential_setup --keystone-user keystone --keystone-group keystone

keystone-manage bootstrap --bootstrap-password Welcome123 \
  --bootstrap-admin-url http://10.10.11.87:5000/v3/ \
  --bootstrap-internal-url http://10.10.11.87:5000/v3/ \
  --bootstrap-public-url http://10.10.11.87:5000/v3/ \
  --bootstrap-region-id RegionOne

sed -i 's|#ServerName www.example.com:80|ServerName 10.10.11.87|g' /etc/httpd/conf/httpd.conf
ln -s /usr/share/keystone/wsgi-keystone.conf /etc/httpd/conf.d/

systemctl restart httpd.service

cat << EOF >> admin-openrc
export export OS_REGION_NAME=RegionOne
export OS_PROJECT_DOMAIN_NAME=Default
export OS_USER_DOMAIN_NAME=Default
export OS_PROJECT_NAME=admin
export OS_USERNAME=admin
export OS_PASSWORD=Welcome123
export OS_AUTH_URL=http://10.10.11.87:5000/v3
export OS_IDENTITY_API_VERSION=3
export OS_IMAGE_API_VERSION=2
EOF

source admin-openrc
openstack token issue

openstack project create service --domain default --description "Service Project" 
openstack project create demo --domain default --description "Demo Project" 
openstack user create demo --domain default --password Welcome123
openstack role create user
openstack role add --project demo --user demo user

## Phần 8: Cài đặt Glance
> Thực hiện trên CTL

mysql -uroot -pWelcome123
CREATE DATABASE glance;
GRANT ALL PRIVILEGES ON glance.* TO 'glance'@'localhost'  IDENTIFIED BY 'Welcome123';
GRANT ALL PRIVILEGES ON glance.* TO 'glance'@'%' IDENTIFIED BY 'Welcome123';
FLUSH PRIVILEGES;
exit;

source admin-openrc

openstack user create --domain default --password Welcome123 glance
openstack role add --project service --user glance admin
openstack service create --name glance --description "OpenStack Image" image

openstack endpoint create --region RegionOne image public http://10.10.11.87:9292
openstack endpoint create --region RegionOne image internal http://10.10.11.87:9292
openstack endpoint create --region RegionOne image admin http://10.10.11.87:9292


yum install -y openstack-glance

mv /etc/glance/glance-api.{conf,conf.bk}
rm -rf /etc/glance/glance-api.conf

cat << EOF >> /etc/glance/glance-api.conf
[DEFAULT]
[cinder]
[cors]
[database]
connection = mysql+pymysql://glance:Welcome123@10.10.11.87/glance
[file]
[glance.store.http.store]
[glance.store.rbd.store]
[glance.store.sheepdog.store]
[glance.store.swift.store]
[glance.store.vmware_datastore.store]
[glance_store]
stores = file,http
default_store = file
filesystem_store_datadir = /var/lib/glance/images/
[image_format]
[keystone_authtoken]
www_authenticate_uri  = http://10.10.11.87:5000
auth_url = http://10.10.11.87:5000
memcached_servers = 10.10.11.87:11211
auth_type = password
project_domain_name = Default
user_domain_name = Default
project_name = service
username = glance
password = Welcome123
[oslo_concurrency]
[oslo_messaging_amqp]
[oslo_messaging_kafka]
[oslo_messaging_notifications]
[oslo_messaging_rabbit]
[oslo_middleware]
[oslo_policy]
[paste_deploy]
flavor = keystone
[profiler]
[store_type_location_strategy]
[task]
[taskflow_executor]
EOF

chown root:glance /etc/glance/glance-api.conf
su -s /bin/sh -c "glance-manage db_sync" glance

systemctl enable openstack-glance-api.service
systemctl restart openstack-glance-api.service


wget http://download.cirros-cloud.net/0.4.0/cirros-0.4.0-x86_64-disk.img
openstack image create "cirros" --file cirros-0.4.0-x86_64-disk.img \
--disk-format qcow2 --container-format bare --public

openstack image list

## Phần 9: Placement service

mysql -u root -pWelcome123
CREATE DATABASE placement;
GRANT ALL PRIVILEGES ON placement.* TO 'placement'@'localhost' \
  IDENTIFIED BY 'Welcome123';
GRANT ALL PRIVILEGES ON placement.* TO 'placement'@'%' \
  IDENTIFIED BY 'Welcome123';
exit

source admin-openrc

openstack user create --domain default --password Welcome123 placement
openstack role add --project service --user placement admin
openstack service create --name placement \
  --description "Placement API" placement

openstack endpoint create --region RegionOne \
  placement public http://10.10.11.87:8778
openstack endpoint create --region RegionOne \
  placement internal http://10.10.11.87:8778
openstack endpoint create --region RegionOne \
  placement admin http://10.10.11.87:8778

yum install openstack-placement-api -y

mv /etc/placement/placement.{conf,conf.bk}

cat << EOF >> /etc/placement/placement.conf
[DEFAULT]
[api]
auth_strategy = keystone
[cors]
[keystone_authtoken]
auth_url = http://10.10.11.87:5000/v3
memcached_servers = 10.10.11.87:11211
auth_type = password
project_domain_name = Default
user_domain_name = Default
project_name = service
username = placement
password = Welcome123
[oslo_policy]
[placement]
[placement_database]
connection = mysql+pymysql://placement:Welcome123@10.10.11.87/placement
[profiler]
EOF

chown root:placement /etc/placement/placement.conf
su -s /bin/sh -c "placement-manage db sync" placement

cp /etc/httpd/conf.d/00-placement-api.conf /etc/httpd/conf.d/00-placement-api.conf.org

cat << 'EOF' >> /etc/httpd/conf.d/00-placement-api.conf

<Directory /usr/bin>
   <IfVersion >= 2.4>
      Require all granted
   </IfVersion>
   <IfVersion < 2.4>
      Order allow,deny
      Allow from all
   </IfVersion>
</Directory>
EOF

systemctl restart httpd

placement-status upgrade check

## Phần 10: Cài đặt nova

mysql -u root -pWelcome123
CREATE DATABASE nova_api;
CREATE DATABASE nova;
CREATE DATABASE nova_cell0;
GRANT ALL PRIVILEGES ON nova_api.* TO 'nova'@'localhost' \
  IDENTIFIED BY 'Welcome123';
GRANT ALL PRIVILEGES ON nova_api.* TO 'nova'@'%' \
  IDENTIFIED BY 'Welcome123';
GRANT ALL PRIVILEGES ON nova.* TO 'nova'@'localhost' \
  IDENTIFIED BY 'Welcome123';
GRANT ALL PRIVILEGES ON nova.* TO 'nova'@'%' \
  IDENTIFIED BY 'Welcome123';
GRANT ALL PRIVILEGES ON nova_cell0.* TO 'nova'@'localhost' \
  IDENTIFIED BY 'Welcome123';
GRANT ALL PRIVILEGES ON nova_cell0.* TO 'nova'@'%' \
  IDENTIFIED BY 'Welcome123';
exit

source admin-openrc

openstack user create --domain default --password Welcome123 nova
openstack role add --project service --user nova admin
openstack service create --name nova \
  --description "OpenStack Compute" compute

openstack endpoint create --region RegionOne \
  compute public http://10.10.11.87:8774/v2.1
openstack endpoint create --region RegionOne \
  compute internal http://10.10.11.87:8774/v2.1
openstack endpoint create --region RegionOne \
  compute admin http://10.10.11.87:8774/v2.1

yum install openstack-nova-api openstack-nova-conductor openstack-nova-novncproxy openstack-nova-scheduler -y

mv /etc/nova/nova.{conf,conf.bk}

cat << EOF >> /etc/nova/nova.conf
[DEFAULT]
my_ip = 10.10.11.87
use_neutron = true
firewall_driver = nova.virt.firewall.NoopFirewallDriver
enabled_apis = osapi_compute,metadata
transport_url = rabbit://openstack:Welcome123@10.10.11.87:5672/
[api]
connection = mysql+pymysql://nova:Welcome123@10.10.11.87/nova
[api_database]
connection = mysql+pymysql://nova:Welcome123@10.10.11.87/nova_api
[barbican]
[cache]
[cinder]
os_region_name = RegionOne
[compute]
[conductor]
[console]
[consoleauth]
[cors]
[database]
connection = mysql+pymysql://nova:Welcome123@10.10.11.87/nova
[devices]
[ephemeral_storage_encryption]
[filter_scheduler]
[glance]
api_servers = http://10.10.11.87:9292
[guestfs]
[healthcheck]
[hyperv]
[ironic]
[key_manager]
[keystone]
[keystone_authtoken]
www_authenticate_uri = http://10.10.11.87:5000/
auth_url = http://10.10.11.87:5000/
memcached_servers = 10.10.11.87:11211
auth_type = password
project_domain_name = Default
user_domain_name = Default
project_name = service
username = nova
password = Welcome123
[libvirt]
[metrics]
[mks]
[neutron]
url = http://10.10.11.87:9696
auth_url = http://10.10.11.87:5000
auth_type = password
project_domain_name = Default
user_domain_name = Default
project_name = service
username = neutron
password = Welcome123
service_metadata_proxy = True
metadata_proxy_shared_secret = Welcome123
[notifications]
[osapi_v21]
[oslo_concurrency]
lock_path = /var/lib/nova/tmp
[oslo_messaging_amqp]
[oslo_messaging_kafka]
[oslo_messaging_notifications]
[oslo_messaging_rabbit]
[oslo_middleware]
[oslo_policy]
[pci]
[placement]
region_name = RegionOne
project_domain_name = Default
project_name = service
auth_type = password
user_domain_name = Default
auth_url = http://10.10.11.87:5000/v3
username = placement
password = Welcome123
[powervm]
[privsep]
[profiler]
[quota]
[rdp]
[remote_debug]
[scheduler]
discover_hosts_in_cells_interval = 300
[serial_console]
[service_user]
[spice]
[upgrade_levels]
[vault]
[vendordata_dynamic_auth]
[vmware]
[vnc]
enabled = true
server_listen = $my_ip
server_proxyclient_address = $my_ip
[workarounds]
[wsgi]
[xenserver]
[xvp]
[zvm]
EOF

chown root:nova /etc/nova/nova.conf

su -s /bin/sh -c "nova-manage api_db sync" nova
su -s /bin/sh -c "nova-manage cell_v2 map_cell0" nova
su -s /bin/sh -c "nova-manage cell_v2 create_cell --name=cell1 --verbose" nova


su -s /bin/sh -c "nova-manage db sync" nova
su -s /bin/sh -c "nova-manage cell_v2 list_cells" nova

systemctl enable \
    openstack-nova-api.service \
    openstack-nova-scheduler.service \
    openstack-nova-conductor.service \
    openstack-nova-novncproxy.service

systemctl start \
    openstack-nova-api.service \
    openstack-nova-scheduler.service \
    openstack-nova-conductor.service \
    openstack-nova-novncproxy.service

su -s /bin/sh -c "nova-manage cell_v2 discover_hosts --verbose" nova

source admin-openrc
openstack compute service list
nova-status upgrade check

### Trên Compute 1

yum install -y openstack-nova-compute

mv /etc/nova/nova.{conf,conf.bk}

cat << EOF >> /etc/nova/nova.conf
[DEFAULT]
enabled_apis = osapi_compute,metadata
transport_url = rabbit://openstack:Welcome123@10.10.11.87
my_ip = 10.10.11.88
use_neutron = true
firewall_driver = nova.virt.firewall.NoopFirewallDriver
[api]
auth_strategy = keystone
[api_database]
connection = mysql+pymysql://nova:Welcome123@10.10.11.87/nova_api
[barbican]
[cache]
[cinder]
os_region_name = RegionOne
[compute]
[conductor]
[console]
[consoleauth]
[cors]
[database]
connection = mysql+pymysql://nova:Welcome123@10.10.11.87/nova
[devices]
[ephemeral_storage_encryption]
[filter_scheduler]
[glance]
api_servers = http://10.10.11.87:9292
[guestfs]
[healthcheck]
[hyperv]
[ironic]
[key_manager]
[keystone]
[keystone_authtoken]
www_authenticate_uri = http://10.10.11.87:5000/
auth_url = http://10.10.11.87:5000/
memcached_servers = 10.10.11.87:11211
auth_type = password
project_domain_name = Default
user_domain_name = Default
project_name = service
username = nova
password = Welcome123
[libvirt]
virt_type = qemu
[metrics]
[mks]
[neutron]
url = http://10.10.11.87:9696
auth_url = http://10.10.11.87:5000
auth_type = password
project_domain_name = Default
user_domain_name = Default
project_name = service
username = neutron
password = Welcome123
[notifications]
[osapi_v21]
[oslo_concurrency]
lock_path = /var/lib/nova/tmp
[oslo_messaging_amqp]
[oslo_messaging_kafka]
[oslo_messaging_notifications]
[oslo_messaging_rabbit]
[oslo_middleware]
[oslo_policy]
[pci]
[placement]
region_name = RegionOne
project_domain_name = Default
project_name = service
auth_type = password
user_domain_name = Default
auth_url = http://10.10.11.87:5000/v3
username = placement
password = Welcome123
[powervm]
[privsep]
[profiler]
[quota]
[rdp]
[remote_debug]
[scheduler]
[serial_console]
[service_user]
[spice]
[upgrade_levels]
[vault]
[vendordata_dynamic_auth]
[vmware]
[vnc]
enabled = true
server_listen = 0.0.0.0
server_proxyclient_address = $my_ip
novncproxy_base_url = http://10.10.11.87:6080/vnc_auto.html
[workarounds]
[wsgi]
[xenserver]
[xvp]
[zvm]
EOF

chown root:nova /etc/nova/nova.conf

systemctl enable libvirtd.service openstack-nova-compute.service
systemctl restart libvirtd.service openstack-nova-compute.service

### Trên compute 02

yum install -y openstack-nova-compute

mv /etc/nova/nova.{conf,conf.bk}

cat << EOF >> /etc/nova/nova.conf
[DEFAULT]
enabled_apis = osapi_compute,metadata
transport_url = rabbit://openstack:Welcome123@10.10.11.87
my_ip = 10.10.11.89
use_neutron = true
firewall_driver = nova.virt.firewall.NoopFirewallDriver
[api]
auth_strategy = keystone
[api_database]
connection = mysql+pymysql://nova:Welcome123@10.10.11.87/nova_api
[barbican]
[cache]
[cinder]
os_region_name = RegionOne
[compute]
[conductor]
[console]
[consoleauth]
[cors]
[database]
connection = mysql+pymysql://nova:Welcome123@10.10.11.87/nova
[devices]
[ephemeral_storage_encryption]
[filter_scheduler]
[glance]
api_servers = http://10.10.11.87:9292
[guestfs]
[healthcheck]
[hyperv]
[ironic]
[key_manager]
[keystone]
[keystone_authtoken]
www_authenticate_uri = http://10.10.11.87:5000/
auth_url = http://10.10.11.87:5000/
memcached_servers = 10.10.11.87:11211
auth_type = password
project_domain_name = Default
user_domain_name = Default
project_name = service
username = nova
password = Welcome123
[libvirt]
virt_type = qemu
[metrics]
[mks]
[neutron]
url = http://10.10.11.87:9696
auth_url = http://10.10.11.87:5000
auth_type = password
project_domain_name = Default
user_domain_name = Default
project_name = service
username = neutron
password = Welcome123
[notifications]
[osapi_v21]
[oslo_concurrency]
lock_path = /var/lib/nova/tmp
[oslo_messaging_amqp]
[oslo_messaging_kafka]
[oslo_messaging_notifications]
[oslo_messaging_rabbit]
[oslo_middleware]
[oslo_policy]
[pci]
[placement]
region_name = RegionOne
project_domain_name = Default
project_name = service
auth_type = password
user_domain_name = Default
auth_url = http://10.10.11.87:5000/v3
username = placement
password = Welcome123
[powervm]
[privsep]
[profiler]
[quota]
[rdp]
[remote_debug]
[scheduler]
[serial_console]
[service_user]
[spice]
[upgrade_levels]
[vault]
[vendordata_dynamic_auth]
[vmware]
[vnc]
enabled = true
server_listen = 0.0.0.0
server_proxyclient_address = $my_ip
novncproxy_base_url = http://10.10.11.87:6080/vnc_auto.html
[workarounds]
[wsgi]
[xenserver]
[xvp]
[zvm]
EOF

chown root:nova /etc/nova/nova.conf

systemctl enable libvirtd.service openstack-nova-compute.service
systemctl restart libvirtd.service openstack-nova-compute.service

## Phần 11: Cài đặt Neutron OVS

### Tại CTL

mysql -u root -pWelcome123
CREATE DATABASE neutron;
GRANT ALL PRIVILEGES ON neutron.* TO 'neutron'@'localhost' \
  IDENTIFIED BY 'Welcome123';
GRANT ALL PRIVILEGES ON neutron.* TO 'neutron'@'%' \
  IDENTIFIED BY 'Welcome123';
exit

source admin-openrc

openstack user create --domain default --password Welcome123 neutron
openstack role add --project service --user neutron admin

openstack service create --name neutron \
  --description "OpenStack Networking" network

openstack endpoint create --region RegionOne \
  network public http://10.10.11.87:9696
openstack endpoint create --region RegionOne \
  network internal http://10.10.11.87:9696
openstack endpoint create --region RegionOne \
  network admin http://10.10.11.87:9696

yum install openstack-neutron openstack-neutron-ml2 \
  openstack-neutron-openvswitch ebtables -y
yum install libibverbs -y

mv /etc/neutron/neutron.{conf,conf.bk}

cat << EOF >> /etc/neutron/neutron.conf
[DEFAULT]
bind_host = 10.10.11.87
core_plugin = ml2
service_plugins = router
transport_url = rabbit://openstack:Welcome123@10.10.11.87:5672
auth_strategy = keystone
notify_nova_on_port_status_changes = true
notify_nova_on_port_data_changes = true
allow_overlapping_ips = True
dhcp_agents_per_network = 2
[agent]
[cors]
[database]
connection = mysql+pymysql://neutron:Welcome123@10.10.11.87/neutron
[keystone_authtoken]
auth_uri = http://10.10.11.87:5000
auth_url = http://10.10.11.87:5000
memcached_servers = 10.10.11.87:11211
auth_type = password
project_domain_name = default
user_domain_name = default
project_name = service
username = neutron
password = Welcome123
[matchmaker_redis]
[nova]
auth_url = http://10.10.11.87:5000
auth_type = password
project_domain_name = default
user_domain_name = default
region_name = RegionOne
project_name = service
username = nova
password = Welcome123
[oslo_concurrency]
lock_path = /var/lib/neutron/tmp
[oslo_messaging_amqp]
[oslo_messaging_kafka]
[oslo_messaging_notifications]
[oslo_messaging_rabbit]
rabbit_retry_interval = 1
rabbit_retry_backoff = 2
amqp_durable_queues = true
rabbit_ha_queues = true
[oslo_messaging_zmq]
[oslo_middleware]
[oslo_policy]
[quotas]
[ssl]
EOF

### Cấu hình file ml2
cp /etc/neutron/plugins/ml2/ml2_conf.ini /etc/neutron/plugins/ml2/ml2_conf.ini.org
rm -rf /etc/neutron/plugins/ml2/ml2_conf.ini

cat << EOF >> /etc/neutron/plugins/ml2/ml2_conf.ini
[DEFAULT]
[l2pop]
[ml2]
type_drivers = flat,vlan,vxlan
tenant_network_types = vxlan
mechanism_drivers = openvswitch,l2population
extension_drivers = port_security
[ml2_type_flat]
flat_networks = provider
[ml2_type_geneve]
[ml2_type_gre]
[ml2_type_vlan]
[ml2_type_vxlan]
vni_ranges = 1:1000
[securitygroup]
enable_ipset = True
EOF

### Cấu hình file ovs agent
cp /etc/neutron/plugins/ml2/openvswitch_agent.ini /etc/neutron/plugins/ml2/openvswitch_agent.ini.org 
rm -rf /etc/neutron/plugins/ml2/openvswitch_agent.ini

cat << EOF >> /etc/neutron/plugins/ml2/openvswitch_agent.ini
[DEFAULT]
interface_driver = openvswitch
[agent]
tunnel_types=vxlan
l2_population = True
[network_log]
[ovs]
bridge_mappings = provider:br-eth1
local_ip=10.10.14.87
[securitygroup]
enable_security_group = True
firewall_driver = iptables_hybrid
[xenapi]
EOF

### Cấu hình trên file l3 agent

cp /etc/neutron/l3_agent.ini /etc/neutron/l3_agent.ini.org
rm -rf /etc/neutron/l3_agent.ini

cat << EOF >> /etc/neutron/l3_agent.ini
[DEFAULT]
interface_driver = openvswitch
external_network_bridge = 
[agent]
[ovs]
EOF

### Thêm vào /etc/nova/nova.conf 
[neutron]
url = http://10.10.11.87:9696
auth_url = http://10.10.11.87:5000
auth_type = password
project_domain_name = default
user_domain_name = default
region_name = RegionOne
project_name = service
username = neutron
password = Welcome123
service_metadata_proxy = true
metadata_proxy_shared_secret = Welcome123

### Restart lại dv nova-api
systemctl restart openstack-nova-api.service

### Phần quyền
chown -R root:neutron /etc/neutron/

### Tạo liên kết
ln -s /etc/neutron/plugins/ml2/ml2_conf.ini /etc/neutron/plugin.ini

### Sync db
su -s /bin/sh -c "neutron-db-manage --config-file /etc/neutron/neutron.conf \
  --config-file /etc/neutron/plugins/ml2/ml2_conf.ini upgrade head" neutron

### Bật ovs
systemctl enable openvswitch
systemctl restart openvswitch

### Thay đổi cấu hình card dùng làm provider

ovs-vsctl add-br br-eth1
ovs-vsctl add-port br-eth1 eth1

### Thay đổi file cấu hình card mạng của eth1

cp /etc/sysconfig/network-scripts/ifcfg-eth1 etc/sysconfig/network-scripts/bak-eth1
rm -rf /etc/sysconfig/network-scripts/ifcfg-eth1

cat << EOF >> /etc/sysconfig/network-scripts/ifcfg-eth1
DEVICE=eth1
NAME=eth1
DEVICETYPE=ovs
TYPE=OVSPort
OVS_BRIDGE=br-eth1
ONBOOT=yes
BOOTPROTO=none
BONDING_MASTER=yes
BONDING_OPTS="mode=1 miimon=100"
NM_CONTROLLED=no
EOF

cat << EOF >> /etc/sysconfig/network-scripts/ifcfg-br-eth1
ONBOOT=yes
DEVICE=br-eth1
NAME=br-eth1
DEVICETYPE=ovs
OVSBOOTPROTO=none
TYPE=OVSBridge
EOF

### Restart network
systemctl restart network

[root@ctl01 ~]# ovs-vsctl show
b45624ac-cf7e-4590-887f-8b750b9c5952
    Bridge "br-eth1"
        Port "eth1"
            Interface "eth1"
        Port "br-eth1"
            Interface "br-eth1"
                type: internal
    ovs_version: "2.12.0"

### Enable và start dịch vụ 

systemctl enable neutron-server.service \
  neutron-openvswitch-agent.service neutron-l3-agent.service
  
systemctl restart neutron-server.service \
  neutron-openvswitch-agent.service neutron-l3-agent.service

### Trên COMPUTE 1

yum install openstack-neutron openstack-neutron-ml2 \
  openstack-neutron-openvswitch ebtables -y

### Bật ovs
systemctl enable openvswitch
systemctl restart openvswitch

### Cấu hình
mv /etc/neutron/neutron.{conf,conf.bk}

cat << EOF >> /etc/neutron/neutron.conf
[DEFAULT]
transport_url = rabbit://openstack:Welcome123@10.10.11.87:5672
auth_strategy = keystone
[agent]
[cors]
[database]
[keystone_authtoken]
auth_uri = http://10.10.11.87:5000
auth_url = http://10.10.11.87:5000
memcached_servers = 10.10.11.87:11211
auth_type = password
project_domain_name = default
user_domain_name = default
project_name = service
username = neutron
password = Welcome123
[matchmaker_redis]
[nova]
[oslo_concurrency]
lock_path = /var/lib/neutron/tmp
[oslo_messaging_amqp]
[oslo_messaging_kafka]
[oslo_messaging_notifications]
[oslo_messaging_rabbit]
rabbit_ha_queues = true
rabbit_retry_interval = 1
rabbit_retry_backoff = 2
amqp_durable_queues= true
[oslo_messaging_zmq]
[oslo_middleware]
[oslo_policy]
[quotas]
[ssl]
EOF


### Cấu hình file ovs agent
cp /etc/neutron/plugins/ml2/openvswitch_agent.ini /etc/neutron/plugins/ml2/openvswitch_agent.ini.org 
rm -rf /etc/neutron/plugins/ml2/openvswitch_agent.ini

cat << EOF >> /etc/neutron/plugins/ml2/openvswitch_agent.ini
[DEFAULT]
interface_driver = openvswitch
[agent]
tunnel_types=vxlan
l2_population = True
[network_log]
[ovs]
bridge_mappings = provider:br-eth1
local_ip=10.10.14.88
[securitygroup]
firewall_driver = neutron.agent.linux.iptables_firewall.OVSHybridIptablesFirewallDriver
[xenapi]
EOF

### Cấu hình dhcp agent
cp /etc/neutron/dhcp_agent.ini /etc/neutron/dhcp_agent.ini.org
rm -rf /etc/neutron/dhcp_agent.ini

cat << EOF >> /etc/neutron/dhcp_agent.ini
[DEFAULT]
interface_driver = openvswitch
dhcp_driver = neutron.agent.linux.dhcp.Dnsmasq
enable_isolated_metadata = True
force_metadata = True
[agent]
[ovs]
EOF

### Cấu hình metadata agent
cp /etc/neutron/metadata_agent.ini /etc/neutron/metadata_agent.ini.org 
rm -rf /etc/neutron/metadata_agent.ini

cat << EOF >> /etc/neutron/metadata_agent.ini
[DEFAULT]
nova_metadata_host = 10.10.11.87
metadata_proxy_shared_secret = Welcome123
[agent]
[cache]
EOF

### Thêm vào file /etc/nova/nova.conf
[neutron]
url = http://10.10.11.87:9696
auth_url = http://10.10.11.87:35357
auth_type = password
project_domain_name = default
user_domain_name = default
region_name = RegionOne
project_name = service
username = neutron
password = Welcome123

chown root:neutron /etc/neutron/metadata_agent.ini /etc/neutron/neutron.conf /etc/neutron/dhcp_agent.ini /etc/neutron/plugins/ml2/openvswitch_agent.ini

### Thay đổi cấu hình card dùng làm provider

ovs-vsctl add-br br-eth1
ovs-vsctl add-port br-eth1 eth1

### Thay đổi file cấu hình card mạng của eth1

cp /etc/sysconfig/network-scripts/ifcfg-eth1 /etc/sysconfig/network-scripts/bak-eth1
rm -rf /etc/sysconfig/network-scripts/ifcfg-eth1

cat << EOF >> /etc/sysconfig/network-scripts/ifcfg-eth1
DEVICE=eth1
NAME=eth1
DEVICETYPE=ovs
TYPE=OVSPort
OVS_BRIDGE=br-eth1
ONBOOT=yes
BOOTPROTO=none
BONDING_MASTER=yes
BONDING_OPTS="mode=1 miimon=100"
NM_CONTROLLED=no
EOF

cat << EOF >> /etc/sysconfig/network-scripts/ifcfg-br-eth1
ONBOOT=yes
DEVICE=br-eth1
NAME=br-eth1
DEVICETYPE=ovs
OVSBOOTPROTO=none
TYPE=OVSBridge
EOF

### Restart network
systemctl restart network

systemctl restart openstack-nova-compute



systemctl enable neutron-openvswitch-agent.service neutron-dhcp-agent.service neutron-metadata-agent.service
systemctl start neutron-openvswitch-agent.service neutron-dhcp-agent.service neutron-metadata-agent.service


### Trên COMPUTE 2

yum install openstack-neutron openstack-neutron-ml2 \
  openstack-neutron-openvswitch ebtables -y

### Bật ovs
systemctl enable openvswitch
systemctl restart openvswitch

### Cấu hình
mv /etc/neutron/neutron.{conf,conf.bk}

cat << EOF >> /etc/neutron/neutron.conf
[DEFAULT]
transport_url = rabbit://openstack:Welcome123@10.10.11.87:5672
auth_strategy = keystone
[agent]
[cors]
[database]
[keystone_authtoken]
auth_uri = http://10.10.11.87:5000
auth_url = http://10.10.11.87:5000
memcached_servers = 10.10.11.87:11211
auth_type = password
project_domain_name = default
user_domain_name = default
project_name = service
username = neutron
password = Welcome123
[matchmaker_redis]
[nova]
[oslo_concurrency]
lock_path = /var/lib/neutron/tmp
[oslo_messaging_amqp]
[oslo_messaging_kafka]
[oslo_messaging_notifications]
[oslo_messaging_rabbit]
rabbit_ha_queues = true
rabbit_retry_interval = 1
rabbit_retry_backoff = 2
amqp_durable_queues= true
[oslo_messaging_zmq]
[oslo_middleware]
[oslo_policy]
[quotas]
[ssl]
EOF


### Cấu hình file ovs agent
cp /etc/neutron/plugins/ml2/openvswitch_agent.ini /etc/neutron/plugins/ml2/openvswitch_agent.ini.org 
rm -rf /etc/neutron/plugins/ml2/openvswitch_agent.ini

cat << EOF >> /etc/neutron/plugins/ml2/openvswitch_agent.ini
[DEFAULT]
interface_driver = openvswitch
[agent]
tunnel_types=vxlan
l2_population = True
[network_log]
[ovs]
bridge_mappings = provider:br-eth1
local_ip=10.10.14.89
[securitygroup]
firewall_driver = neutron.agent.linux.iptables_firewall.OVSHybridIptablesFirewallDriver
[xenapi]
EOF

### Cấu hình dhcp agent
cp /etc/neutron/dhcp_agent.ini /etc/neutron/dhcp_agent.ini.org
rm -rf /etc/neutron/dhcp_agent.ini

cat << EOF >> /etc/neutron/dhcp_agent.ini
[DEFAULT]
interface_driver = openvswitch
dhcp_driver = neutron.agent.linux.dhcp.Dnsmasq
enable_isolated_metadata = True
force_metadata = True
[agent]
[ovs]
EOF

### Cấu hình metadata agent
cp /etc/neutron/metadata_agent.ini /etc/neutron/metadata_agent.ini.org 
rm -rf /etc/neutron/metadata_agent.ini

cat << EOF >> /etc/neutron/metadata_agent.ini
[DEFAULT]
nova_metadata_host = 10.10.11.87
metadata_proxy_shared_secret = Welcome123
[agent]
[cache]
EOF

### Thêm vào file /etc/nova/nova.conf
[neutron]
url = http://10.10.11.87:9696
auth_url = http://10.10.11.87:35357
auth_type = password
project_domain_name = default
user_domain_name = default
region_name = RegionOne
project_name = service
username = neutron
password = Welcome123

chown root:neutron /etc/neutron/metadata_agent.ini /etc/neutron/neutron.conf /etc/neutron/dhcp_agent.ini /etc/neutron/plugins/ml2/openvswitch_agent.ini

### Thay đổi cấu hình card dùng làm provider

ovs-vsctl add-br br-eth1
ovs-vsctl add-port br-eth1 eth1

### Thay đổi file cấu hình card mạng của eth1

cp /etc/sysconfig/network-scripts/ifcfg-eth1 /etc/sysconfig/network-scripts/bak-eth1
rm -rf /etc/sysconfig/network-scripts/ifcfg-eth1

cat << EOF >> /etc/sysconfig/network-scripts/ifcfg-eth1
DEVICE=eth1
NAME=eth1
DEVICETYPE=ovs
TYPE=OVSPort
OVS_BRIDGE=br-eth1
ONBOOT=yes
BOOTPROTO=none
BONDING_MASTER=yes
BONDING_OPTS="mode=1 miimon=100"
NM_CONTROLLED=no
EOF

cat << EOF >> /etc/sysconfig/network-scripts/ifcfg-br-eth1
ONBOOT=yes
DEVICE=br-eth1
NAME=br-eth1
DEVICETYPE=ovs
OVSBOOTPROTO=none
TYPE=OVSBridge
EOF

### Restart network
systemctl restart network

systemctl restart openstack-nova-compute

systemctl enable neutron-openvswitch-agent.service neutron-dhcp-agent.service neutron-metadata-agent.service
systemctl start neutron-openvswitch-agent.service neutron-dhcp-agent.service neutron-metadata-agent.service


## Cấu hình horizon

### Tải packages

yum install openstack-dashboard -y

### Tạo file direct
filehtml=/var/www/html/index.html
touch $filehtml
cat << EOF >> $filehtml
<html>
<head>
<META HTTP-EQUIV="Refresh" Content="0.5; URL=http://10.10.11.87/dashboard">
</head>
<body>
<center> <h1>Redirecting to OpenStack Dashboard</h1> </center>
</body>
</html>
EOF

```
cp /etc/openstack-dashboard/local_settings /etc/openstack-dashboard/local_settings.org

### Thay đổi cấu hình trong file /etc/openstack-dashboard/local_settings
ALLOWED_HOSTS = ['*',]
OPENSTACK_API_VERSIONS = {
    "identity": 3,
    "image": 2,
    "volume": 2,
}


### Lưu ý thêm SESSION_ENGINE vào trên dòng CACHE như bên dưới
SESSION_ENGINE = 'django.contrib.sessions.backends.cache'
CACHES = {
    'default': {
         'BACKEND': 'django.core.cache.backends.memcached.MemcachedCache',
         'LOCATION': ['10.10.11.87:11211']
    }
}
OPENSTACK_HOST = "10.10.11.87"
OPENSTACK_KEYSTONE_URL = "http://10.10.11.87:5000/v3"
OPENSTACK_KEYSTONE_DEFAULT_DOMAIN = "Default"
OPENSTACK_KEYSTONE_DEFAULT_ROLE = "user"

TIME_ZONE = "Asia/Ho_Chi_Minh"

## Thêm mới
WEBROOT = '/dashboard/'
```

systemctl restart httpd.service memcached.service



https://docs.openstack.org/neutron/train/admin/index.html

https://docs.openstack.org/neutron/pike/admin/deploy-ovs-selfservice.html

https://docs.openstack.org/neutron/pike/admin/deploy-ovs-provider.html#deploy-ovs-provider

https://github.com/meditechopen/meditech-ghichep-openstack/blob/master/docs/04.Neutron/neutron-openvswitch.md

https://github.com/meditechopen/meditech-ghichep-openstack/blob/master/docs/04.Neutron/OpenStack-Networking-basic.md

https://superuser.openstack.org/articles/openvswitch-openstack-sdn/

https://www.server-world.info/en/note?os=CentOS_7&p=download

https://www.server-world.info/en/note?os=CentOS_7&p=openstack_train&f=10

https://www.server-world.info/en/note?os=CentOS_7&p=openstack_train2&f=3

https://louky0714.tistory.com/entry/Openstack-Train-Error-The-requested-URL-authlogin-was-not-found-on-this-server
