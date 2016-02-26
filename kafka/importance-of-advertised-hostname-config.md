# The Importance of the advertised.host.name Setting for AWS Kafka in a VPC 

For out of network access to a Kafka cluster in AWS you definitely want to stick with using DNS,
and avoid using any IPs in the your configurations.  This will allow of communication with the Kafka
cluster from within the VPC and external to the VPC, through tightly definied security groups or a 
VPC direct connection with your datacenter.

Public DNS to access machines via restricted security groups in AWS.

```
web --> EIP (kafka1.autozane.com) --> EC2 (VPC) kafka1.autozane.com
web --> EIP (kafka2.autozane.com) --> EC2 (VPC) kafka1.autozane.com
web --> EIP (kafka3.autozane.com) --> EC2 (VPC) kafka1.autozane.com
```

```
#/etc/kafka/server.properties on kafka1
advertised.host.name = kafka1.autozane.com
```

```
#/etc/kafka/server.properties on kafka2
advertised.host.name = kafka2.autozane.com
```

```
#/etc/kafka/server.properties on kafka3
advertised.host.name = kafka3.autozane.com
```

```
#/etc/hosts on kafka systems for VPC communication
10.100.1.101 kafka1.autozane.com
10.100.1.102 kafka2.autozane.com
10.100.1.103 kafka3.autozane.com
```
