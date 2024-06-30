
# Real-Time Stock Market Data Analysis Using Kafka

This project demonstrates an end-to-end data engineering pipeline for real-time stock market data using Apache Kafka. The goal is to stream, process, and analyze stock market data in real time, showcasing various data engineering concepts and tools.

## Project Overview

1. **Set up Kafka on EC2**
2. **Stream data using Kafka producers and consumers**
3. **Store data in an S3 bucket**
4. **Use AWS Glue for data cataloging**
5. **Query data using AWS Athena**

## Step-by-Step Guide

### 1. Set Up Kafka on EC2

**Create an EC2 Instance:**

- Use t2.micro instance type (free tier).
- Select an existing key pair or create a new one.
- Ensure you have a default VPC and subnet, or create them.

**Connect to Your EC2 Instance:**

```bash
ssh -i "kafka-stock-market.pem" ec2-user@ec2-<public-ip>.compute-1.amazonaws.com
```

If you encounter permissions issues on Mac, run:

```bash
chmod 400 kafka-stock-market.pem
```

**Install Java:**

Kafka requires Java to run. Install Java on your EC2 instance:

```bash
sudo yum install java-1.8.0-openjdk
```

If you encounter issues, follow these steps:

```bash
sudo yum clean all
sudo yum update
sudo yum install -y yum-utils
sudo yum-config-manager --enable extras
sudo yum install -y java-1.8.0-openjdk
```

Alternatively, download and install Java manually:

```bash
wget https://github.com/AdoptOpenJDK/openjdk8-binaries/releases/download/jdk8u292-b10/OpenJDK8U-jdk_x64_linux_hotspot_8u292b10.tar.gz
tar -xvf OpenJDK8U-jdk_x64_linux_hotspot_8u292b10.tar.gz
sudo mv jdk8u292-b10 /usr/local/
sudo ln -s /usr/local/jdk8u292-b10 /usr/local/java
sudo alternatives --install /usr/bin/java java /usr/local/java/bin/java 1
sudo alternatives --set java /usr/local/java/bin/java
```

**Set JAVA_HOME (Optional but recommended):**

```bash
sudo nano /etc/profile.d/jdk.sh
```

Add:

```bash
export JAVA_HOME=/usr/local/java
export PATH=$JAVA_HOME/bin:$PATH
```

Make the script executable and reload the profile:

```bash
sudo chmod +x /etc/profile.d/jdk.sh
source /etc/profile.d/jdk.sh
```

Verify JAVA_HOME:

```bash
echo $JAVA_HOME
```

**Install Kafka:**

```bash
wget https://downloads.apache.org/kafka/3.7.0/kafka_2.13-3.7.0.tgz
tar -xvf kafka_2.13-3.7.0.tgz
cd kafka_2.13-3.7.0
```

### 2. Configure and Start Kafka

Edit Kafka's server properties:

```bash
sudo nano config/server.properties
```

Set the `host.name` to your EC2 instance's public IP address. Save and exit.

**Start Zookeeper:**

```bash
bin/zookeeper-server-start.sh config/zookeeper.properties
```

**Start Kafka Broker:**

In a new terminal:

```bash
ssh -i "kafka-stock-market.pem" ec2-user@ec2-<public-ip>.compute-1.amazonaws.com
cd kafka_2.13-3.7.0
export KAFKA_HEAP_OPTS="-Xmx256M -Xms128M"
bin/kafka-server-start.sh config/server.properties
```

### 3. Create Kafka Topics and Test

**Create a Topic:**

```bash
bin/kafka-topics.sh --create --topic demo_test --bootstrap-server <public-ip>:9092 --replication-factor 1 --partitions 1
```

**Start Producer:**

```bash
bin/kafka-console-producer.sh --topic demo_test --bootstrap-server <public-ip>:9092
```

**Start Consumer:**

In a new terminal:

```bash
ssh -i "kafka-stock-market.pem" ec2-user@ec2-<public-ip>.compute-1.amazonaws.com
cd kafka_2.13-3.7.0
bin/kafka-console-consumer.sh --topic demo_test --bootstrap-server <public-ip>:9092
```

### 4. Python Code for Kafka Producer and Consumer

Install necessary Python libraries:

```bash
pip install kafka-python
```

Refer to the `.ipynb` files in this repository for detailed producer and consumer code. 

### 5. Store Data in S3 and Use AWS Glue

**Create an S3 Bucket:**

Ensure the bucket name is globally unique.

**AWS Glue Crawler:**

- Set the S3 bucket as the source.
- Create a database.
- Ensure Glue has permissions to access the S3 bucket.

### 6. Query Data with AWS Athena

- Set up Athena and choose an S3 bucket to store results.
- Query the data cataloged by AWS Glue.

## Conclusion

This project demonstrates a comprehensive data engineering workflow for real-time stock market data using Apache Kafka and AWS services. By following these steps, you will gain hands-on experience with setting up Kafka, streaming data, and performing real-time data analysis.

---

Make sure to check for IAM roles and permissions when encountering errors. Restart Zookeeper and Kafka server if needed. Update public IP addresses when restarting the instance or assign an elastic IP to avoid IP changes.

