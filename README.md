# kafka-fda-pipeline

Produce and Consume Messages using [openFDA API](https://open.fda.gov/apis/)

# Overview

I will use this cluster to create a Kafka Application that produces and consumes messages. This Application will be written in Python.

To produce messages, I will use the [openFDA API](https://open.fda.gov/apis/) that enables clients to receive data in near real-time. You can [request access](https://open.fda.gov/apis/authentication/) to the API and any developer can build applications using it.

## What is Kafka?
Apache Kafka is a distributed streaming platform that is used to build real time streaming data pipelines and applications that adapt to data streams.

## What is openFDA?
openFDA provides APIs and raw download access to a number of high-value, high priority and scalable structured datasets, including adverse events, drug product labeling, and recall enforcement reports.

# Requirements

**Languages** 
* Python
* Pandas

**Technologies**
* Kafka
* AWS EC2
* Jupyter

**Third-Party Libraries**
* AWS CLI
* s3fs
* kafka-python

# Environment Setup
### 1. Install and configure [AWS CLI](https://aws.amazon.com/cli/)

### 2. Assign AmazonEC2FullAccess to your user on [IAM](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html)

### 3. Follow [this](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html#AccessingInstancesLinuxSSHClient) to use SSH to connect to your instance.

### 4. Connect to instance
```
ssh -i "kafka-fda-project.pem" ec2-user@ec2-44-212-23-201.compute-1.amazonaws.com
```

### 5. Download Apache Kafka compressed version on your EC2 instance
```
wget https://downloads.apache.org/kafka/3.3.1/kafka_2.12-3.3.1.tgz
```

### 6. Uncompress
```
tar -xvf kafka_2.12-3.3.1.tgz
```

### 7. Kafka runs on java, install java jdk 1.8
```
sudo yum install java-1.8.0-openjdk
```

### 8. Verify version of java after installation
```
java -version
```

### 9. Navigate to kafka folder that we uncompressed
```
cd kafka_2.12-3.3.1
```

### 10. Start Zoo-keeper server
```
bin/zookeeper-server-start.sh config/zookeeper.properties
```

### 11. Increase the memory of our kafka server

Open a new terminal window and duplicate the session
```
export KAFKA_HEAP_OPTS="-Xmx256M -Xms128M"
```
### 12. Navigate to kafka folder 
```
cd kafka_2.12-3.3.1
```
### 13. Run kafka server
```
bin/kafka-server-start.sh config/server.properties
```

Note: This is pointing to private server, we need to change server.properties so that it can run on a public IP. 
You cannot access your private DNS from your local computer unless you're on the same network.

### 14. Modify server.properties
```
sudo vi config/server.properties
``` 
Change ```ADVERTISED_LISTENERS``` to public ip of the EC2 instance

### 15. On your AWS EC2 console, edit ```EC2 > Security Groups > Inbound Rules > Allow All Traffic > Source: My IP```
        
(If you're doing this for your organization, consult your Cloud, Devops or Security Engineer for best practices.)

### 16. Create the topic

Open a new terminal window and duplicate the session
```
cd kafka_2.12-3.3.1
```
```
bin/kafka-topics.sh --create --topic demo_test --bootstrap-server {Put the Public IP of your EC2 Instance:9092} --replication-factor 1 --partitions 1
```

### 17. Start Producer
```
bin/kafka-console-producer.sh --topic demo_test --bootstrap-server {Put the Public IP of your EC2 Instance:9092} 
```

### 18. Start Consumer

Open a new terminal window and duplicate the session
```
cd kafka_2.12-3.3.1
```
```
bin/kafka-console-consumer.sh --topic demo_test --bootstrap-server {Put the Public IP of your EC2 Instance:9092}
```

### 19. Run notebook files (KafkaConsumerFDA.ipynb and KafkaProducerFDA.ipynb) on your local machine.