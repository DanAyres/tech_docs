## Introduction

The elastic stack is the name given to a collection of tools for parsing, shipping, storing and viewing data. The parsing and shipping functions are performed by Logstash, 
storing is done by Elasticsearch and viewing is done in Kibana. These three services are usually hosted on a server and a fourth service (beats) is used to ship data from a client to the server. See the figure below.


![elk-infrastructure.png](img/elk-infrastructure.png)

In this tutorial we will learn how to install the elastic stack first locally and then hosted on an ubuntu 18.04 server.
