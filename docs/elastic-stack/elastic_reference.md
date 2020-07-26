# Elasticsearch command reference


## Adding data

Creating an index
```
PUT /my_index
```
https://www.elastic.co/guide/en/elasticsearch/reference/7.8/indices-create-index.html


## Delete index
```
curl -X DELETE "localhost:9200/testing_from_file?pretty"
```


## Exploring the database

Display all of the indices
```
curl -X GET "localhost:9200/_cat/indices"
```

To view the mapping for the index "my_index":
```
curl -XGET http://127.0.0.1:9200/my_index/_mapping?pretty > my_mapping.json
```

## Editing the configuration

Change the number of replicas
```
PUT 'localhost:9200/_settings
{
    "index" : {
        "number_of_replicas" : 0
    }
}'
```



```
curl -X PUT "localhost:9200/test?pretty" -H 'Content-Type: application/json' -d'
{
  "settings": {
    "number_of_shards": 1
  },
  "mappings": {
    "properties": {
      "field1": { "type": "text" }
    }
  }
}
```

## Queries

```
curl -X GET "localhost:9200/health_record-localhost/_search?pretty" -H 'Content-Type: application/json' -d'
{
    "query": {
        "match_all": {}
    }
}
'
```
## Mappings
https://www.elastic.co/guide/en/elasticsearch/reference/7.8/mapping.html


Nested object mappings:
- https://www.elastic.co/guide/en/elasticsearch/guide/master/nested-mapping.html
- https://stackoverflow.com/questions/30308817/elastic-search-mapping-for-nested-json-objects
https://blog.gojekengineering.com/elasticsearch-the-trouble-with-nested-documents-e97b33b46194
https://qbox.io/blog/handling-relationships-using-nested-objects-elasticsearch
http://joelabrahamsson.com/elasticsearch-nested-mapping-and-filter/ 

'
