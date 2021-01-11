## Local Installation


### Useful Resources

An example of installing the stack on Ubuntu Server 18.04 (on AWS) can be found [here](https://logz.io/learn/complete-guide-elk-stack/#installing-elk).
Instructions for configuring the elastic stack on various Linux distributions. The following instructions follow the same process as [this DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-install-elasticsearch-logstash-and-kibana-elk-stack-on-ubuntu-14-04) tutorial with the exception that an explicit installation of Java is no longer required since it is now bundled with elastic.


https://logz.io/learn/complete-guide-elk-stack/

https://towardsdatascience.com/running-securing-and-deploying-elastic-stack-on-docker-f1a8ebf1dc5b



### Elasticsearch

First, you need to add Elastic’s signing key so that the downloaded package can be verified (skip this step if you’ve already installed packages from Elastic):
```
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
```
For Debian, we need to then install the apt-transport-https package:
```
sudo apt-get update
sudo apt-get install apt-transport-https
```
The next step is to add the repository definition to your system:
```
echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list
```
To install a version of Elasticsearch that contains only features licensed under Apache 2.0 (aka OSS Elasticsearch):
```
echo "deb https://artifacts.elastic.co/packages/oss-7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list
```
All that’s left to do is to update your repositories and install Elasticsearch:
```
sudo apt-get update
sudo apt-get install elasticsearch
```
Elasticsearch configurations are done using a configuration file that allows you to configure general settings (e.g. node name), as well as network settings (e.g. host and port), where data is stored, memory, log files, and more.

For our example, it is a good best practice to bind Elasticsearch to either a private IP or localhost:
```
sudo vim /etc/elasticsearch/elasticsearch.yml

network.host: "localhost"
http.port:9200
cluster.initial_master_nodes: ["<PrivateIP"]
```
To run Elasticsearch, use:
```
sudo service elasticsearch start
```

To confirm the installation has been successful, point curl at localhost on port 9200:
```
curl http://localhost:9200
```


### Logstash
Logstash requires Java 8 or Java 11 to run so we will start the process of setting up Logstash with:
```
sudo apt-get install default-jre
```
Verify java is installed:
```
java -version

openjdk 11.0.7 2020-04-14
OpenJDK Runtime Environment (build 11.0.7+10-post-Ubuntu-2ubuntu218.04)
OpenJDK 64-Bit Server VM (build 11.0.7+10-post-Ubuntu-2ubuntu218.04, mixed mode, sharing)
```
Since we already defined the repository in the system, all we have to do to install Logstash is run:
```
sudo apt-get install logstash
```
Before you run Logstash, you will need to configure a data pipeline. We will get back to that once we’ve installed and started Kibana.

### Kibana



