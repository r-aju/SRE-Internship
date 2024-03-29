* TASK -3 =>Convert this setup into two nodes Galera clusters and then add another node to this cluster

```

=> Create another Vm consider it as Third Vm. 
=> The Vm's are (Master) (Slave) (New Vm)

=> Since, MariaDB is installed and configured we can continue with adding Galera nodes
=> The IP address => Master => 192.168.1.4;
                  => Slave  => 192.168.1.6;
                  => the new node => 192.168.1.8;
                    (These were assigned statically)
=>  Configure the First Node => Each node in the cluster needs to have a nearly identical configuration
=>  Open => sudo nano /etc/mysql/conf.d/galera.cnf
             (A empty file) 

=> Add the below details.
=>[mysqld]
binlog_format=ROW
default-storage-engine=innodb
innodb_autoinc_lock_mode=2
bind-address=0.0.0.0                  // can be bind 192.168.1.4

# Galera Provider Configuration
wsrep_on=ON
wsrep_provider=/usr/lib/galera/libgalera_smm.so

# Galera Cluster Configuration
wsrep_cluster_name="test_cluster"
wsrep_cluster_address="gcomm://192.168.1.4,192.168.1.6,192.168.1.8"

# Galera Synchronization Configuration
wsrep_sst_method=rsync

# Galera Node Configuration
wsrep_node_address="192.168.1.4"
wsrep_node_name="Node_1" 
```
* KEY-POINTS
```
 “Galera Provider Configuration” 
 section configures the MariaDB components that provide a WriteSet replication API. 
This means Galera in your case, since Galera is a wsrep (WriteSet Replication) provider. 

=> "Galera Cluster Configuration" 
defines the cluster, 
identifying the cluster members by IP address or resolvable domain name 
and creating a name for the cluster to ensure that members join the correct group.

=>“Galera Synchronization Configuration”
->section defines how the cluster will communicate and synchronize data between members. 
  This is used only for the state transfer that happens when a node comes online.


=>“Galera Node Configuration”
clarifies the IP address and the name of the current server.
This is helpful when trying to diagnose problems in logs and for referencing each server in multiple ways.
```

* Configuring the other two Nodes 
* SLAVE VM
```
=>sudo nano /etc/mysql/conf.d/galera.cnf
=> => Add the below details.
=>
[mysqld]
binlog_format=ROW
default-storage-engine=innodb
innodb_autoinc_lock_mode=2
bind-address=0.0.0.0                          // can be bind 192.168.1.6

# Galera Provider Configuration
wsrep_on=ON
wsrep_provider=/usr/lib/galera/libgalera_smm.so

# Galera Cluster Configuration
wsrep_cluster_name="test_cluster"
wsrep_cluster_address="gcomm://192.168.1.4,192.168.1.6,192.168.1.8"

# Galera Synchronization Configuration
wsrep_sst_method=rsync

# Galera Node Configuration
wsrep_node_address="192.168.1.6"
wsrep_node_name="Node_2"   
```
* THIRD-VM
```
NEW VM -> Third(VM)

=>sudo nano /etc/mysql/conf.d/galera.cnf
=>Add the below details.

=>[mysqld]
binlog_format=ROW
default-storage-engine=innodb
innodb_autoinc_lock_mode=2
bind-address=0.0.0.0                          // can be bind 192.168.1.8

# Galera Provider Configuration
wsrep_on=ON
wsrep_provider=/usr/lib/galera/libgalera_smm.so

# Galera Cluster Configuration
wsrep_cluster_name="test_cluster"
wsrep_cluster_address="gcomm://192.168.1.4,192.168.1.6,192.168.1.8"

# Galera Synchronization Configuration
wsrep_sst_method=rsync

# Galera Node Configuration
wsrep_node_address="192.168.1.8"
wsrep_node_name="Node_3"   
```
* Starting the Cluster
```
=>Stop MariaDB on All Three Servers => sudo systemctl stop mysql
=> Check status if active => sudo systemctl status mysql


=> Bring Up the First Node
=>sudo galera_new_cluster         ( Can be done as the MASTER VM i.e VM-1)

=>mysql -u root -p -e "SHOW STATUS LIKE 'wsrep_cluster_size'"
=>+--------------------+-------+
| Variable_name      | Value |
+--------------------+-------+
| wsrep_cluster_size | 1     |
+--------------------+-------+

(First Node is up)
```
* Starting the Cluster (Second Node)

```
=>sudo systemctl start mysql
=>mysql -u root -p -e "SHOW STATUS LIKE 'wsrep_cluster_size'"
=>+--------------------+-------+
| Variable_name      | Value |
+--------------------+-------+
| wsrep_cluster_size | 2     |
+--------------------+-------+
```

* Starting the Cluster (Third Node)
```
=>sudo systemctl start mysql
=>mysql -u root -p -e "SHOW STATUS LIKE 'wsrep_cluster_size'"
=>+--------------------+-------+
| Variable_name      | Value |
+--------------------+-------+
| wsrep_cluster_size | 3     |
+--------------------+-------+

```

* Login to MYSQL
```
=>mysql -u root -p
=> show databases;
=> use mysql;
=> show tables;
=> select * from wsrep_cluster_members;
=> Would show the cluster members.
```

* The checksum for the table should not change

```
=> The checksum value should not be 0.
=> The checksum value was same in all the 3 Vm's
=> steps
=> mysql -u root -p
=> use nginx;
=> command is => checksum table ngnix_access_log;
```
