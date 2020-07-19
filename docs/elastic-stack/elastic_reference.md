# Elasticsearch command reference

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
