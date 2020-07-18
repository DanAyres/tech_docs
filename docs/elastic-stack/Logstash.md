
# logstash

> We assume that logstash, elasticsearch and kibana are installed and configured for this tutorial. See [here](/elk_stack) for instructions on installing the elastic stack.
{.is-warning}

## Introduction
[Logstash]([https://www.elastic.co/guide/en/logstash/current/introduction.html](https://www.elastic.co/guide/en/logstash/current/introduction.html)) is an open source data collection engine with real-time pipelining capabilities. From the diagram we can see that a logstash consists of three components or stages:

- [Inputs](https://www.elastic.co/guide/en/logstash/current/input-plugins.html): a number of input plugins are available to parse logs and data from a variety of sources. Some are targted at specific data sources such as Amazon S3 and other will read input from files and TCP/Unix/Web sockets etc. 
- [Filters](https://www.elastic.co/guide/en/logstash/current/filter-plugins.html) (optional) are used to manipulate the input data. For instance, regular expressions can be used to pick certain values, data can be assumed to be deliminted by a certain character, certain date formats can be search for etc.
- [Outputs](https://www.elastic.co/guide/en/logstash/current/output-plugins.html) are used to ship data to the destination service.

![basic_logstash_pipeline.png](img/basic_logstash_pipeline.png)

The aim of this tutorial is to learn how to ingest longterm health records from Symetrica's detector subsystem. Ultimately we will parse the health records with Python and send data (JSON) to logstash via tcp using logstash to output directly to elasticsearch. To learn all of the techniques required, we will invoke logstash directly from the command line (using a bash script) and illustrate these techiques using two very simple data sets. An overview is given below:

- Running logstash: Invoking logstash from the command line and normal operation
- Data set 1 (JSON events in a text file)
- Reading JSON from a file and outputing to standard output

## Useful Resources

Getting started with [logstash](https://www.elastic.co/blog/a-practical-introduction-to-logstash).

Streaming data from python using sockets:
https://docs.python.org/3/howto/sockets.html

Example logstash.conf for socket connectinos:
https://pypi.org/project/python3-logstash/

More Tcp comms in python
https://wiki.python.org/moin/TcpCommunication
https://realpython.com/python-sockets/

Little logstash lessons:
https://www.elastic.co/blog/little-logstash-lessons-part-using-grok-mutate-type-data
https://www.elastic.co/blog/logstash_lesson_elasticsearch_mapping

# Running logstash


Make sure logstash is installed. For the purposes of testing, we will be invoking logstash directly to debug the configuration.  The following bash script is used to invoke logstash:
```
#!/bin/bash
exec=/usr/share/logstash/bin/logstash
SETTINGS_DIR=/etc/logstash

PROJECT_ROOT=/home/dan/Dropbox/Documents/Projects/Elasticsearch/Logstash_testing
CONFIG_PATH=$PROJECT_ROOT/JSON_From_File.conf
LOG_PATH=$PROJECT_ROOT/logs
DATA_PATH=$PROJECT_ROOT/data

$exec -r -f $CONFIG_PATH --path.settings $SETTINGS_DIR -l $LOG_PATH --path.data $DATA_PATH --log.level "info"
```
For more information regarding the command line arguments to logstash see [here]([https://www.elastic.co/guide/en/logstash/current/running-logstash-command-line.html#command-line-flags](https://www.elastic.co/guide/en/logstash/current/running-logstash-command-line.html#command-line-flags)).



## Reading JSON-formatted text from files 


The configuration file for logstash is shown below:
```
input {
 file {
   path => ["/home/dan/Dropbox/Documents/Projects/Elasticsearch/Logstash_testing/testdata.log"]
   sincedb_path => "/dev/null"
   start_position => "beginning"
  }
}
filter {
json { 
source => "message"
   }
}
output {
  stdout {
    codec => rubydebug
  }
}

```
The configuration file has three stages, input, manipulation and outout.

 - There are many input [plugins]([https://www.elastic.co/guide/en/logstash/current/input-plugins.html](https://www.elastic.co/guide/en/logstash/current/input-plugins.html)), for illustration and debugging we will use the file input and for the development of our application we will use the tcp input.
 - 


The JSON data in testdata.log has the following format:
```
{"message": {"Param1": 1, "Param2": 2, "Pan1DetG1": {"SerialNumber": "something", "Health1": 56}}, "@timestamp": "2020-07-01T06:00:00.000Z"}
{"message": {"Param1": 3, "Param2": 4, "Pan1DetG1": {"SerialNumber": "something", "Health1": 56}}, "@timestamp": "2020-07-02T07:00:00.000Z"}
{"message": {"Param1": 5, "Param2": 4, "Pan1DetG1": {"SerialNumber": "something", "Health1": 56}}, "@timestamp": "2020-07-02T08:00:00.000Z"}

```


# A simple example: plotting a sine wave in kibana.

To illustrate the elastic pipeline, we will generate some JSON data for some trig functions (sin, cos and tan). The data will be parsed by Logstash, passed into elastic and plotted by Kibana.

We have 


Make sure elasticsearch and kibana are running then point a web browser at:
http://localhost:5601/



Go to stack management / Index Patterns to create a new index pattern


## Sending JSON over TCP

```
nc -N localhost 5000 <  tmp.json
```
The -N flag will close the connection when the end of file (EOF) is reached.

## Resources
