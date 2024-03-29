# RABBIT-MQ
```
RabbitMQ is an open-source message-broker software which uses AMPQ Protocol.
Support Streaming Text Oriented Messaging Protocol.
```

### INSTALLATION

* Step1 ` Install Erlang` Rabbit MQ code implementation is done using Erlang dependencies.

```
In the file (sudo nano /etc/apt/sources.list)
=> Add this line at EOF.
=>deb https://packages.erlang-solutions.com/ubuntu bionic contrib
=>(Packages for erlang)
```

* STEP-2	
```
=>Create a file in the directory  (/etc/apt/preferences.d)
=>sudo touch /etc/apt/preferences.d/erlang (erlang name of the file)
=>sudo nano /etc/apt/preferences.d/erlang
```


* STEP-3      
```
=> In the file created 
=> Mention these contents
=> (Package: erlang* esl-erlang
            Pin: version 1:21.3*
            Pin-Priority: 501)
```
* STEP-4 

```
  =>Run these commands in the terminal.
          1. wget https://packages.erlang-solutions.com/ubuntu/erlang_solutions.asc.
          2.sudo apt-key add erlang_solutions.asc
          3.sudo apt-get update -y
          4.sudo apt-get install esl-erlang -y                           // Installs Erlang 
          5.sudo ln -s /usr/lib/erlang/bin/erl /usr/local/bin/erl.      //Symbolic link
```
* STEP-5 
```
             => Install RMQ 
                 =>In the file (sudo nano /etc/apt/sources.list)
                 => Add this line at EOF.
                 =>deb https://packagecloud.io/rabbitmq/rabbitmq-server/ubuntu/ bionic main
                 =>deb-src https://packagecloud.io/rabbitmq/rabbitmq-server/ubuntu/ bionic main
   
```

* STEP-6.
```     
 => Add key-Repo =>sudo apt-key adv --keyserver "keyserver.ubuntu.com" --recv-keys"F6609E60DC62814E"  
```

* STEP-7.
```  
=> Install additional erlang packages.
=>sudo apt-get install -y erlang-base \
                        erlang-asn1 erlang-crypto erlang-eldap erlang-ftp erlang-inets \
                        erlang-mnesia erlang-os-mon erlang-parsetools erlang-public-key \
                        erlang-runtime-tools erlang-snmp erlang-ssl \
                        erlang-syntax-tools erlang-tftp erlang-tools erlang-xmerl
```

STEP-8.  
```
=> Install Rabbitmq-server
=>sudo apt install rabbitmq-server=3.7.9-1
```
STEP-9.
```
=> To enabale RMQ's Dashboard.
-> rabbitmq-plugins enable rabbitmq_management
-> The RabbitMQ management plugin provides an HTTP-based API that can be used to monitor and manage RabbitMQ nodes and clusters through a web browser. By default, it is running on the TCP port 15672.
```

### DATA-PERSISTENCE

* Data should be persisted on disk
```
=> For this a durable queue was created and published the messages so that the messages persisted
    even when the App is stopped and restarted.

=>channel.queue_declare(queue=raju, durable=True)    // Was added in the python script.
=> durable=True says the queue is durable.


=> Publishing the message was also done using the basic_publish method.
=> channel.basic_publish(exchange='', routing_key='raju', body='Hello World!')   

```


### CLUSTERS

* Add 2 more nodes to the cluster without restarting RMQ service on first one.

* STEP-1.  
```
=>  Setup Hosts File.
	     =>Edit the '/etc/hosts' file using nano editor.
               => sudo nano/etc/hosts.
               => At the EOF Add the following configuration.
               =>	192.168.1.14 bu1            // IP and user of VM-1
		            192.168.1.15 bu2            //  IP and user of VM-2
		            192.168.1.16 bu3            // IP and user of VM-3
        NOTE => Do this configuration on the other two Vm’s as well.
```	
* STEP-2 

```
            Enable RabbitMQ Management Plugins
            =>sudo rabbitmq-plugins enable rabbitmq_management
            =>sudo systemctl restart rabbitmq-server
```	    

* STEP-3             
```
 Setup UFW Firewall.
          
          =>sudo ufw allow ssh
          =>sudo ufw enable
          => sudo ufw allow 5672,15672,4369,25672/tcp.   //New RMQ Ports.
          =>sudo ufw status
```
STEP-4 => Setup RabbitMQ Cluster
```
         => In the directory => /var/lib/rabbitmq/.erlang.cookie
         => Copy the key available in the file and copy it to the other 2 Vm’s as well
 ```           

```
STEP-5=>Run commands below on ‘bu2 (VM-2)’ and ‘bu3 (VM-3)’   

     =>sudo systemctl restart rabbitmq-server
     =>sudo rabbitmqctl stop_app

```
```
STEP-6 => Now let RabbitMQ server on both nodes join the cluster on 'bu1', then start the app.
                   

     => sudo rabbitmqctl join_cluster rabbit@bu1
     => sudo rabbitmqctl start_app
	 => sudo rabbitmqctl cluster_status
     =>list running nodes.(sudo rabbitmqctl cluster_status --formatter=json | q -r .running_nodes[]

	NOTE => Do this configuration on the  Third Vm as well. (Not at the First Vm)
```

* OUTPUT => sudo rabbitmqctl cluster_status
```
Cluster status of node rabbit@bu1 ...
[{nodes,[{disc,[rabbit@bu1,rabbit@bu2,rabbit@bu3]}]},
 {running_nodes,[rabbit@bu3,rabbit@bu2,rabbit@bu1]},
 {cluster_name,<<"rabbit@bu1">>},
 {partitions,[]},
 {alarms,[{rabbit@bu3,[]},{rabbit@bu2,[]},{rabbit@bu1,[]}]}]
 ```
 ### VHOSTS
 ```

 =>Create a vhost and a user with read-write permissions to the vhost.

=>STEP-1. => ADD USERS.

           => sudo rabbitmqctl add_user raju raju
           => sudo rabbitmqctl set_user_tags raju administrator
           =>sudo rabbitmqctl set_permissions -p / admin   ".*" “.*"
           =>sudo rabbitmqctl delete_user guest
           
STEP-2 => ADD VHOSTS.
	   
	     =>rabbitmqctl add_vhost kanakaraju
       =>rabbitmqctl set_permissions -p kanakaraju raju ".*" ".*" ".*" // Setting up the permissions to V-Host under user raju
       => Deleted the default guest user.
       => sudo rabbitmqctl delete_user guest
```

### QUEUES

```

 =>Create 2 queues (DATA, DATA_SIDELINE) on the above created vhost
 => The above queues can be created using RABBIT-MQ app UI, The Commandline commands or By using Python/Java Scripts.
 => The queues were created using Python scripts.
 => Install pika Module.
   => apt install python3-pip
   => pip3 install pika  
 => Install Rabbitmq admin plugin as well.

 => Queue-1 => (DATA QUEUE)

import pika                                                                                              // Importing Pika Packages 
credentials = pika.PlainCredentials("raju", "raju")                                                      // RabbitMQ user   
parameters = pika.ConnectionParameters("192.168.43.102", "5672", "kanakaraju", credentials=credentials)  // Vm's IP and rabbitMQ port Number
connection = pika.BlockingConnection(parameters)
channel = connection.channel()
channel.queue_declare(queue='DATA')                                                                     //  Creating a Queue DATA
channel.basic_publish(exchange='', routing_key='DATA', body='Hello World!')                             //  Publish Hello World message
print(" [x] Sent 'Hello World!'")                                                                      //  Priniting the sent message   
connection.close()                                                                                     // Post Creating the Queue close connection.


=> Queue-2 => (DATA_SIDELINE QUEUE)

import pika
credentials = pika.PlainCredentials("raju", "raju")
parameters = pika.ConnectionParameters("192.168.43.102", "5672", "/", credentials)
connection = pika.BlockingConnection(parameters)
channel = connection.channel()
channel.queue_declare(queue='DATASIDELINE')
channel.basic_publish(exchange='', routing_key='DATASIDELINE', body='Hello World!')
print(" [x] Sent 'Hello World!'")
connection.close()

```

### Python Scripts
```
=> Here basic queue is being implemented.

 =>  Queue-1 => (DATA QUEUE) (Receive1.py)
 
#!/usr/bin/env python
import pika, sys, os

def main():
    credentials = pika.PlainCredentials("raju", "raju")
    parameters = pika.ConnectionParameters("192.168.43.102", "5672", "kanakaraju", credentials=credentials)
    connection = pika.BlockingConnection(parameters)
    channel = connection.channel()
    channel.exchange_declare(exchange='logs', exchange_type='fanout')                          // Exchange type is fanout
    channel.queue_declare(queue='DATA')                                                        // The queue will be created by not created Before
    channel.queue_bind(exchange='logs', queue='DATA')
    def callback(ch, method, properties, body):                                                // Declaring function callback
        print(" [x] Received %r" % body)
    channel.basic_consume(queue='DATA', on_message_callback=callback, auto_ack=True)          // Consumes data sent via sender code
    print(' [*] Waiting for messages. To exit press CTRL+C')                                  // If no messages received press CTRL+C
    channel.start_consuming()

if __name__ == '__main__':
    try:
        main()
    except KeyboardInterrupt:
        print('Interrupted')
        try:
            sys.exit(0)
        except SystemExit:
            os._exit(0)

=> Queue-2 => (DATA_SIDELINE QUEUE) (Receive2.py)

 
#!/usr/bin/env python
import pika, sys, os

def main():
    credentials = pika.PlainCredentials("raju", "raju")
    parameters = pika.ConnectionParameters("192.168.43.102", "5672", "kanakaraju", credentials=credentials)
    connection = pika.BlockingConnection(parameters)
    channel = connection.channel()
    channel.exchange_declare(exchange='logs', exchange_type='fanout')           
    channel.queue_declare(queue='DATA_SIDELINE')                                                  // The queue will be created by not created Before
    channel.queue_bind(exchange='logs', queue='DATA_SIDELINE')
    def callback(ch, method, properties, body):                                                   // Declaring function callback//
        print(" [x] Received %r" % body)
     channel.basic_consume(queue='DATA_SIDELINE', on_message_callback=callback, auto_ack=True)   // Consumes data sent via sender code

    print(' [*] Waiting for messages. To exit press CTRL+C')                                     // If no messages received press CTRL+C
    channel.start_consuming()

if __name__ == '__main__':
    try:
        main()
    except KeyboardInterrupt:
        print('Interrupted')
        try:
            sys.exit(0)
        except SystemExit:
            os._exit(0)
```
### PUBLISHER AND CONSUMER FOR THE DATA QUEUE
```
=> Create a publisher and consumer for the DATA queue.
=>the messages which were not consumed by the consumer should move to the DATA_SIDELINE queue after a specific time.        

=>Create a publisher and consumer for the DATA queue.

=> QUEUE-1 (DATA QUEUE)
=>
import pika
credentials = pika.PlainCredentials("raju", "raju")
parameters = pika.ConnectionParameters("192.168.43.102", "5672", "kanakaraju", credentials=credentials)
connection = pika.BlockingConnection(parameters)
channel = connection.channel()
channel.queue_declare(queue='DATA')
channel.basic_publish(exchange='', routing_key='DATA', body='Hello World!')  // Publishing is done with the help of basic_publish method.
print(" [x] Sent 'Hello World!'")
connection.close()
```

### The messages which were not consumed by the consumer should move to the DATA_SIDELINE queue after a specific time.

```
channel.queue_declare(queue='DATA',arguments = {"x-message-ttl" : 60000,
                                                "x-dead-letter-exchange" : '' ,
                                                "x-dead-letter-routing-key" : 
                                                 "DATA_SIDELINE" } )
 => The TTL was set and DATA_SIDELINE Queue is mentioned because it should've forward to the particular queue.
 
 ```


 
