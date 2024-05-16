# Using the search template

```bash
GET /customers/_search
{
  "query": {
    "match": {
      "name" : "duarte"
    }
  }
}
```

A query template consists of these two main elements:
* A template represents a query object that Elasticsearch supports. The template utilizes the [mustache syntax](http://mustache.github.io/), which is widely used to articulate templates;
* Optionally, a parameter dictionary is employed to populate the template.

```bash
POST _scripts/my-search-template
{
  "script": {
    "lang": "mustache",
    "source": {
       "query": {
          "match": {
            "name" : "{{value}}"
          }
        }
    } 
  } 
}
```

```bash
POST /customers/_search/template
{ 
  "id": "my-search-template", 
  "params": { 
    "value": "duarte" 
  } 
}
```

```bash
POST _scripts/my-search-template-complex-query
{
  "script": {
    "lang": "mustache",
    "source": {
      "query": {
        "bool": {
          "should": [
            {
              "term": {
                "tax_id": {
                  "value": "{{cpf}}",
                  "boost": 2
                }
              }
            },
            {
              "multi_match": {
                "query": "{{name}}", 
                "type": "best_fields",  
                "fields": [ "name.pt_std", "profile_aliases" ]
              }
            }
          ],
          "minimum_should_match": 1
        }
      },
      "highlight": {
        "fields": {
          "name.pt_std": {},
          "profile_aliases": {}, 
          "tax_id": {}
        }
      }
    } 
  } 
}
```

```bash
POST /customers/_search/template
{ 
  "id": "my-search-template-complex-query", 
  "params": { 
    "cpf": "852963741",
    "name": "larissa macedo"
  } 
}
```

List existing search templates in Elasticsearch.
```bash
GET _cluster/state/?filter_path=metadata.stored_scripts
```

## References
- [Search templates](https://www.elastic.co/guide/en/elasticsearch/reference/master/search-template.html)