## MONITORING TOOLS

```
Tools which are designed to collect and evaluate various system metrics are grouped as MONITORING TOOLS.
```

### SETUP COLLECTD

```
Collectd-web is a web front-end monitoring tool based on RRDtool (Round-Robin Database Tool)
which interprets and graphical outputs the data collected by the Collectd service on Linux systems. 
```

### INSTALLATION

```
STEP-1 => apt-get update                        // Updates the system before installing
           =>apt-get install collectd           // Installs Collectd
           =>service collectd start             // starts the collectd service.


STEP-2 =>Install GIT too ( Beacuse cloning of Repo also takes place and Installing Collectd and Perl Dependencies as well.)
          => apt-get install git
          =>apt install collectd python build-essential librrds-perl libjson-perl libhtml-parser-perl apache2 emboss bioperl ncbi-blast+ 
            gzip libjson-perl libtext-csv-perl libfile-slurp-perl liblwp-protocol-https-perl libwww-perl git libconfig-general-perl libregexp-common-perl


STEP-3 => Change the location of the current directory to the desired location and clone the Repository.
          =>cd /usr/local/
          =>git clone https://github.com/httpdss/collectd-web.git    (Import the Git Repo)
          => Once the Git repository is imported into the system go ahead and enter the collectd-web directory


STEP-4 =>cd collectd-web/
          =>ls
          =>chmod +x cgi-bin/graphdefs.cgi      // Change the permission of the file to exectuable (filename =>graphdefs.cgi)


STEP-5 => Open the file runserver.py and change some configurations inside that file.
          => The runserver.py file by default was set as 
          => standalone Python server script is configured by default to run and bind only on loopback address (127.0.0.1)
          => To change the configurations (127.0.0.1) => (0.0.0.0) in the runserver.py file.
          => This configuration is supported to python 2.7 version.
          => Post configuring run the runserver.py file =>(python2.7 runserver.py &)
          => On successful compilation It would be running the python script.


STEP-6 => Meanwhile the runserver.py file is running. 
          => Open the browser and Type the IP as => (IP:8888) 
          => In my case it was (http://192.168.43.27:8888)       
          => By default collectd runs on port 8888

       => To stop the server 
          => killall python (or) killall python2.7
```

### COLLECTION METRICS
```
=> For collection of Rabbit-MQ Plugins a seperate plugin was installed and configured.
    => The plugin called Signal-FX was used.
    => For this RabbitMQ should be installed and Configured.
    => RabbitMQ Management plugin should also be enabled for this step.
    => Install the python plugin.


=> cd /opt
=> apt-get install collectd-python   // Python Plugin
  => or git clone https://github.com/signalfx/collectd-rabbitmq.git /opt/collectd-rabbitmq
  

  =>In the Collectd Config file. (/etc/collectd/collectd.conf file)
   => add these contents at the end.

   =>LoadPlugin python
    <Plugin python>
       ModulePath "/opt/collectd-rabbitmq"

      Import rabbitmq
     <Module rabbitmq>
     Username "raju5"                    // RMQ => Username
     Password "raju5"                    // RMQ => Password   
     Host "192.168.43.27"                // RMQ => IP   
     Port "15672"                        // RMQ => Default Port
     CollectChannels true
     CollectConnections true
     CollectExchanges true               // All these parameters were set to true so that they get collected.
     CollectNodes true
     CollectQueues true
     # FieldLength 1023
     # HTTPTimeout 60
     # VerbosityLevel "info"
   </Module>
 </Plugin>


=> Post configurations of the file.
   =>systemctl restart collectd
   => systemctl status collectd       (If no errors found in configuration file. The status shows as active)
   =>In the webapp UI it shows the colleted metrics and RMQ Values as well of the particular hosts.
   
  ```
 
 ### SETUP INFLUXDB ON A VM
 
 ```
 => InfluxDB is an Open Source Time Series Database Platform for storing  Time Series Data.
   These are metrics & Events collected from different devices


STEP-1 => The installation was done at Ubuntu 18.04 V
          =>echo "deb https://repos.influxdata.com/ubuntu bionic stable" | sudo tee /etc/apt/sources.list.d/influxdb.list
          =>Add the InfluxData repository to the file  /etc/apt/sources.list.d/influxdb.list
          =>sudo curl -sL https://repos.influxdata.com/influxdb.key | sudo apt-key add -
          =>sudo apt-get update
          =>sudo apt-get install influxdb
          =>sudo systemctl enable --now influxdb
          =>systemctl status influxdb
          =>Open influxdb service ports on the firewall
          =>sudo ufw enable
          =>sudo ufw allow 8086/tcp
          =>By default It runs on port 8086.
 ```
 
  ### SENDING COLLECTD METRICS TO INFLUXDB
  
  ```
  VM-1 (IP =>192.168.43.27)         (Where Collectd was installed and Where stats are collected and transferred to InfluxDB by collectd)
  VM-2 (IP => 192.168.45.34)        (Where Influxdb was installed Where stats are stored by InfluxDB and visualised by Grafana) 

 ```
   ### POST INSTALLING INFLUXDB
 
 ```
 
=> Adding the username  for influxdb
   =>User authentication

=>login to InfluxDB Database
  => influx
  =>CREATE USER raju WITH PASSWORD 'raju' WITH ALL PRIVILEGES 
  =>SHOW USERS
  =>user    admin
    ----    -----
    raju    true
>
> EXIT 


=> To make this user to be the default user while logging in 
   =>In the file /etc/influxdb/influxdb.conf
   => Search for auth-enabled and make it as =>auth-enabled = true in order to enable authentication.

=> sudo service influxdb restart

=>Login to Influxdb
  => influx -username raju -password raju
  => create database collectd                    // Create a database collectd to collect data from Collectd
  => show databases (to verify) 



=> Post creating the database 
   => In the /etc/influx/influxdb.conf file
   => [[collectd]]
      enabled = true
      bind-address = "0.0.0.0:25826"
      database = "collectd"
      retention-policy = ""
      typesdb = "/usr/local/share/collectd/types.db"    //Location of types.db schema.
      batch-size = 5000
      batch-pending = 10
      batch-timeout = "10s"
      read-buffer = 0


=> Download types.db
   => The types.db would be in the Collectd Machine.
   => in the location => /usr/local/share/collectd/types.db
   => From the collectd machine copy the file to Vm-2 via SCP.
   => scp /usr/local/share/collectd/types.db a@192.168.45.34:/usr/local/share/collectd/types.db
   =>      (Source file)                                      (destination location.)

   => sudo service influxdb stop
   =>sudo service influxdb start      //Post configuring the configuration file.


=> In the collectd machine.
   =>VM-1 (IP =>192.168.43.27)  
   =>In the collectd Configuration file.
   => /etc/collectd/collectd.conf
   =>Enable LoadPlugin network then add block below to the bottom of the page.
     
     <Plugin "network">
    Server "192.168.45.34" "25826"     //VM-2 IP
    </Plugin> 

   => systemctl restart collectd
   => systemctl status collectd       // If no error it will show active message.


=> In the Influxdb machine.
   =>influx -username raju -password raju
   =>USE collectd
   =>SHOW MEASUREMENTS
   =>name
     ----
     cpu_value
     memory_value
     ....
     ....
     ....

   => The other metrics can also be shown here.
   ```
   
### PLOT THESE METRICS ON A GRAFANA DASHBOARD
```
=> Grafana is like a Web-UI where InfluxDB Stats can be sent.
=> Grafana is used to facilitates rapid responses and minimalize downtime if anything goes down. (used to send alerts)
=> It's like a vizulization tool.
```

### INSTALLATION OF  GRAFANA 
```
  =>wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -                  //Importing Grafana GPG key with wget
   => sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"        //Adding the Grafana repository to the APT sources
   =>sudo apt update                                                                        // updates system. (Refresh APT Cache)
   =>apt-cache policy grafana                                                               //Making sure Grafana will be installed from the Grafana repository
   =>sudo apt install grafana                                                               // Installing Grafana
   =>sudo systemctl start grafana-server                                                    // Post installing starting the Grafana
   =>sudo systemctl status grafana-server
   =>sudo systemctl enable grafana-server                                                   // Starting it on system reboots
```

### GRAFANA WEB UI 
```
=> Post installing and confguring.
=>Browse to http://192.168.45.34:3000                                                        // By default listens to port 3000
=>Login with admin:admin                                                                     // Default username and password is admin.

=>ADD DATASOURCE
  =>Click Grafana icon -> select Data Sources -> click Add data source.
  => Fill the fields
     => Name  -> metrics
     => Type  -> InfluxDB
     => Url    -> http://192.168.45.34:8086
     => Access  -> proxy
     =>Database  -> collectd
     =>User      -> raju
     =>Password   -> raju

     => Click on OK


=>ADD DASHBOARD
   =>Click Grafana icon in the top header -> select Dashboard -> then New -> Name (metrics)
   => We can select a particular metrics and view the graph
```
   
