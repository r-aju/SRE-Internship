## TLS

###  TLS-CERTS
```
The RMQ cluster should be on TLS and have a username/password
The TLS Concept is used for securing Node.
Steps to be followed were 
Take any node in the cluster (Master Node was taken into consideration)
Generated  certificates using tls-Gen
```
```
>git clone https://github.com/michaelklishin/tls-gen tls-gen
>cd tls-gen/basic                                                       // directory for tls certificate


>make PASSWORD=phonepe                                                  // private key password  
> make verify
>make info
>ls -l ./result
```

```
=> Post installing those 3 files were Generated
>ca_certificate.pem: a certificate authority bundle
>server_certificate.pem: certificate (public key) that will be used by the configured node (and/or CLI tools)
>server_key.pem: private key that will be used by the configured node (and/or CLI tools)


=> The new files were created.
=> In the Directory => /etc/rabbitmq/
rabbitmq.conf, advanced.config and  rabbitmq-env.conf
```

```=> Post Creating the files 


=> in the file => /etc/rabbitmq/rabbitmq-env.conf
=> The following contents were added.
=>

# overrides primary config file location
RABBITMQ_CONFIG_FILE=/etc/rabbitmq/rabbitmq.conf

# overrides advanced config file location
RABBITMQ_ADVANCED_CONFIG_FILE=/etc/rabbitmq/advanced.config

# overrides environment variable file location
RABBITMQ_CONF_ENV_FILE=/etc/rabbitmq/rabbitmq-env.conf



// Post configuring these files there was an issue in working with RMQ Service.
// The RMQ -service is not getting restarted. (So curently debugging on it)
```

