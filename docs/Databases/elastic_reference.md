# Elasticsearch command reference

## Useful links

- The [REST API](https://www.elastic.co/guide/en/elasticsearch/reference/current/rest-apis.html) documentation.

## Indexes

- [Creating](https://www.elastic.co/guide/en/elasticsearch/reference/7.8/indices-create-index.html) an index
    ```shell script
    curl -X PUT "localhost:9200/my_index"
    ```
- Delete an index

    Curl:
    ```shell script
    curl -X DELETE "localhost:9200/<name_of_index>?pretty"
    ```

- List all indexes:

    In the browser:
    ```http request
    http://localhost:9200/_cat/indices
    ```
    Curl:
    ```shell script
    curl -X GET "localhost:9200/_cat/indices/"
    ```

- Count the number of documents in an index
    ```shell script
    curl -X GET "localhost:9200/<name_of_index>/_count"
    ```

- Get the settings for an index
    ```shell script
    curl -X GET "localhost:9200/<name_of_index>/_settings?pretty"
    ```
  
- Rename an index. This clones the existing index into a new index and deletes the old
    ```shell script
    curl -X POST "localhost:9200/<OLD_index>/_open"
    curl -X PUT "localhost:9200/<OLD_index>/_settings" -H 'Content-Type: application/json' -d'
    {
      "settings": {
        "index.blocks.write": "true"
      }
    }
    '
    curl -X POST "localhost:9200/<NEW_index>/_clone/<OLD_index>" -H 'Content-Type: application/json' -d'
    {
      "settings": {
        "index.blocks.write": null 
      }
    }
    '
    curl -X DELETE "localhost:9200/<OLD_index>"
    ```

## Mappings

- To view the mapping for the index "my_index":
    ```shell script
    curl -XGET http://127.0.0.1:9200/my_index/_mapping?pretty > my_mapping.json
    ```

- Update the maximum number of fieldnames allowed in an index
    ```shell script
    curl -X PUT "localhost:9200/flowprobe200-ipfixa636bf-statsv/_settings" -H 'Content-Type: application/json' -d'
    {
      "index.mapping.total_fields.limit": 10000
    }
    '
    ```

## Cluster/node configuration

Change the number of replicas
```shell script
PUT 'localhost:9200/_settings
{
    "index" : {
        "number_of_replicas" : 0
    }
}'
```


## Search and Queries

- Dump all data from an index
    
    In the browser:
    ```http request
    http://localhost:9200/<name_of_index>/_search?pretty=true&q=*:*
    ```
    Curl:
    ```shell script
    curl -X GET "localhost:9200/<name_of_index>/_search?pretty=true&q=*:*"
    ```
  
- Return all documents in time order that have the field name 'error':
    ```shell script
    curl -X GET "localhost:9200/_search?pretty" -H 'Content-Type: application/json' -d'
    {
      "query": {
        "exists": {
          "field": "error"
        }
      },
      "sort": { "@timestamp" : "desc" },
      "size": 2    
    }
    '

- Return all documents in time order that have the field name 'error':
    ```shell script
    curl -X GET "localhost:9200/_search?pretty" -H 'Content-Type: application/json' -d'
    {
      "sort": { "@timestamp" : "desc" },
      "size": 2    
    }
    '