The purpose of this section is to understand the basics of a logstash pipeline, to do this we will read data from a file, filter for a json-formatted message and output to the standard output.


## Running logstash

To aid debugging, we will be invoking logstash directly from the command line. To facilitate this, we will be using the following bash script
``` bash
#!/bin/bash
EXEC=/usr/share/logstash/bin/logstash
SETTINGS_DIR=/etc/logstash

PROJECT_ROOT=/home/dan/Dropbox/Documents/Projects/Elasticsearch/Logstash_testing
CONFIG_PATH=$PROJECT_ROOT/$1
LOG_PATH=$PROJECT_ROOT/logs
DATA_PATH=$PROJECT_ROOT/data
LOG_LEVEL="info"

$EXEC -r -f $CONFIG_PATH --path.settings $SETTINGS_DIR -l $LOG_PATH --path.data $DATA_PATH --log.level $LOG_LEVEL
```
Most of the script variables are self-explanitory but we highlight the following arguments and flags:


- `$SETTINGS_DIR`
- `$CONFIG_PATH` and `$DATA_PATH`
- `$CONFIG_PATH`

The script can be called using the following signature (it assumes the .conf file is in the project directory
``` shell
$ ./run JSON_From_File.conf
```

For more information regarding the command line arguments to logstash see [here](https://www.elastic.co/guide/en/logstash/current/running-logstash-command-line.html#command-line-flags)


## Reading events from file input

First we will generate some test data and save it to a file, the following is saved in testdata.json
``` json
{"my_data": {"Param1": 1, "Param2": 2, "Pan1DetG1": {"SerialNumber": "something", "Health1": 56}}, "@timestamp": "2020-07-01T06:00:00.000Z"}
{"my_data": {"Param1": 3, "Param2": 4, "Pan1DetG1": {"SerialNumber": "something", "Health1": 56}}, "@timestamp": "2020-07-02T07:00:00.000Z"}
{"my_data": {"Param1": 5, "Param2": 4, "Pan1DetG1": {"SerialNumber": "something", "Health1": 56}}, "@timestamp": "2020-07-02T08:00:00.000Z"}

```
In testdata.json we have defined three events. By default, when reading files with Logstash, each event is assumed to be one line and a line is taken to be the text before a newline character.

To begin, we will configure logstash to parse testdata.json and output all the generated events to the standard output. To configure Logstash, you create a config file that specifies which plugins you want to use and settings for each plugin. To parse testdata.json we have used the input file plugin and to write to stdout we have used the stdout output plugin, see JSON_From_File.conf below
``` yaml
input {
 file {
   path => ["/home/dan/Dropbox/Documents/Projects/Elasticsearch/Logstash_testing/json_from_file/testdata.json"]
   sincedb_path => "/dev/null"
   start_position => "beginning"
  }
}
output {
  stdout {
    codec => rubydebug
  }
}

```
The following options have been used with the [file input plugin](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-file.html#plugins-inputs-file-path):

- path: self-explanitory
- sincedb_path: when logstash is used to monitor logfiles which are continuously updated (syslog etc.), a database of the last position in the file is maintained incase logstash is restarted. sincedb_path specifies the path to this "database" file.
- start_position: where in the file to start processing (tail etc.)

rubydebug: outputs event data using the ruby "awesome_print" library This is the default codec for stdout. See [here](https://www.elastic.co/guide/en/logstash/current/plugins-outputs-stdout.html) for a description of the output plugin.

After running the bash script defined above, Logstash outputs the following:

``` shell
{                                                                                                                                                                                            
    "@timestamp" => 2020-07-26T08:55:37.133Z,                                                                                                                                                
       "message" => "{\"my_data\": {\"Param1\": 1, \"Param2\": 2, \"Pan1DetG1\": {\"SerialNumber\": \"something\", \"Health1\": 56}}, \"@timestamp\": \"2020-07-01T06:00:00.000Z\"}",        
      "@version" => "1",                                                                                                                                                                     
          "path" => "/home/dan/Dropbox/Documents/Projects/Elasticsearch/Logstash_testing/json_from_file/testdata.json",
          "host" => "lu-sym1"
}
{
    "@timestamp" => 2020-07-26T08:55:37.165Z,
       "message" => "{\"my_data\": {\"Param1\": 5, \"Param2\": 4, \"Pan1DetG1\": {\"SerialNumber\": \"something\", \"Health1\": 56}}, \"@timestamp\": \"2020-07-02T08:00:00.000Z\"}",
      "@version" => "1",
          "path" => "/home/dan/Dropbox/Documents/Projects/Elasticsearch/Logstash_testing/json_from_file/testdata.json",
          "host" => "lu-sym1"
}
{
    "@timestamp" => 2020-07-26T08:55:37.164Z,
       "message" => "{\"my_data\": {\"Param1\": 3, \"Param2\": 4, \"Pan1DetG1\": {\"SerialNumber\": \"something\", \"Health1\": 56}}, \"@timestamp\": \"2020-07-02T07:00:00.000Z\"}",
      "@version" => "1",
          "path" => "/home/dan/Dropbox/Documents/Projects/Elasticsearch/Logstash_testing/json_from_file/testdata.json",
          "host" => "lu-sym1"
}
```
We can see that Logstash has produced three events as expected. Each event has 5 field names:

- "@timestamp": The time at which the event was processed
- "message": The data read from the file
- "@version": Integer value, not sure where it comes from
- "path": Path to the file parsed (added by file input plugin I think)
- "host": hostname of the machine running logstash

### Parsing JSON using a filter

```
filter {
json { 
source => "message"
   }
}
```

Running Logstash with the new configuration, results in three events - each having the following format:
``` shell
{
    "@timestamp" => 2020-07-01T06:00:00.000Z,
       "message" => "{\"my_data\": {\"Param1\": 1, \"Param2\": 2, \"Pan1DetG1\": {\"SerialNumber\": \"something\", \"Health1\": 56}}, \"@timestamp\": \"2020-07-01T06:00:00.000Z\"}",
      "@version" => "1",
          "path" => "/home/dan/Dropbox/Documents/Projects/Elasticsearch/Logstash_testing/json_from_file/testdata.json",
          "host" => "lu-sym1",
       "my_data" => {
           "Param1" => 1,
           "Param2" => 2,
        "Pan1DetG1" => {
            "SerialNumber" => "something",
                 "Health1" => 56
        }
    }
}
```
We can see that the "@timestamp" field is no longer the time at which the event was processed by Logstash but instead it has been replaced by the "@timestamp" field from the "message" with all other fields from "message" appearing in the event.

### Formatting event data using a filter
Once the message field has been parsed, it is now a redundant piece of information that we no longer needed. The Logstash mutate filter can be used to perform a variety of opertions on event fields including removal. As an example, we will use the mutate remove_field filter to remove the "message", "host" and "path" fields and then the mutate rename filter to rename the "my_data" field. The filter block of the configuration file now looks like
``` yaml
filter {
	json { source => "message" }
    mutate { remove_field => [ "message", "path", "host"] }
    mutate { rename => ["my_data", "lhr" ] }
}
```


``` shell
{
      "@version" => "1",
           "lhr" => {
           "Param1" => 5,
           "Param2" => 4,
        "Pan1DetG1" => {
            "SerialNumber" => "something",
                 "Health1" => 56
        }
    },
    "@timestamp" => 2020-07-02T08:00:00.000Z
}
{
      "@version" => "1",
           "lhr" => {
           "Param1" => 1,
           "Param2" => 2,
        "Pan1DetG1" => {
            "SerialNumber" => "something",
                 "Health1" => 56
        }
    },
    "@timestamp" => 2020-07-01T06:00:00.000Z
}
{
      "@version" => "1",
           "lhr" => {
           "Param1" => 3,
           "Param2" => 4,
        "Pan1DetG1" => {
            "SerialNumber" => "something",
                 "Health1" => 56
        }
    },
    "@timestamp" => 2020-07-02T07:00:00.000Z
}

```


### Output to Elasticsearch

```
output {
  stdout {
    codec => rubydebug
  }
  elasticsearch {
	index => "testing_from_file"
  }
}
```

``` shell
curl -X GET "localhost:9200/testing_from_file/_search?pretty" -H 'Content-Type: application/json' -d'
{
    "query": {
        "match_all": {}
    }
}
'
```

``` shell
{
  "took" : 5,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 3,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "testing_from_file",
        "_type" : "_doc",
        "_id" : "2kW4inMBmZY_EcVlPLBN",
        "_score" : 1.0,
        "_source" : {
          "@version" : "1",
          "lhr" : {
            "Param1" : 1,
            "Param2" : 2,
            "Pan1DetG1" : {
              "SerialNumber" : "something",
              "Health1" : 56
            }
          },
          "@timestamp" : "2020-07-01T06:00:00.000Z"
        }
      },
      {
        "_index" : "testing_from_file",
        "_type" : "_doc",
        "_id" : "2UW4inMBmZY_EcVlPLBL",
        "_score" : 1.0,
        "_source" : {
          "@version" : "1",
          "lhr" : {
            "Param1" : 3,
            "Param2" : 4,
            "Pan1DetG1" : {
              "SerialNumber" : "something",
              "Health1" : 56
            }
          },
          "@timestamp" : "2020-07-02T07:00:00.000Z"
        }
      },
      {
        "_index" : "testing_from_file",
        "_type" : "_doc",
        "_id" : "20W4inMBmZY_EcVlPLDx",
        "_score" : 1.0,
        "_source" : {
          "@version" : "1",
          "lhr" : {
            "Param1" : 5,
            "Param2" : 4,
            "Pan1DetG1" : {
              "SerialNumber" : "something",
              "Health1" : 56
            }
          },
          "@timestamp" : "2020-07-02T08:00:00.000Z"
        }
      }
    ]
  }
}

```

## Reading events from a TCP socket


``` yaml
input {
	tcp {
		port => 5000
		codec => json
	}
}


filter {
	mutate {
		lowercase => [ "host" ]
	}
	mutate { remove_field => [ "port"] }
}


output {

	stdout {
		codec => rubydebug
	}
	
	elasticsearch {
		index => "health_record-%{host}"
	}


}

```

To test the TCP configuration we will pass the data to Logstash using netcat as follows:
```
nc -N localhost 5000 <  testdata.json
```
