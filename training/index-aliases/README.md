# Index Aliases and Zero Downtime

```bash
PUT /favorite_candy_v1
{
  "settings": {
    "number_of_shards": 2,
    "number_of_replicas": 2
  }
}
```

```bash
POST favorite_candy_v1/_doc
{
  "first_name": "Lisa",
  "candy": "Sour Skittles"
}

PUT favorite_candy_v1/_doc/1
{
  "first_name": "John",
  "candy": "Starburst"
}
```

```bash
GET /favorite_candy_v1/_search
{
  "query": {
    "match_all": {}
  }
}
```

```bash
PUT /favorite_candy_v1/_settings
{
  "index": {
    "number_of_replicas": 1
  }
}
```

```bash
PUT /favorite_candy_v1/_alias/favorite_candy
```

```bash
GET /favorite_candy_v1/_settings
```

> [!NOTE]
> You can check which index `favorite_candy` the alias points to:
> ```bash
> GET /*/_alias/favorite_candy
> ```
> Or which aliases point to the index `favorite_candy_v1`:
> ```bash
> GET /favorite_candy_v1/_alias/*
> ```

```bash
PUT favorite_candy/_create/2
{
  "first_name": "Finn",
  "candy": "Jolly Ranchers"
}

PUT favorite_candy/_create/3
{
  "first_name": "Mary",
  "candy": "Apple"
}
```

```bash
GET /favorite_candy/_search
{
  "query": {
    "match_all": {}
  }
}
```

```bash
PUT /favorite_candy_v2
{
  "settings": {
    "number_of_shards": 5,
    "number_of_replicas": 1
  },
  "mappings": {
    "candy": {
      "properties": {
      "tags": {
        "type": "string",
        "index": "not_analyzed"
      }
    }
  }
}

GET /favorite_candy_v2/_search
{
  "query": {
    "match_all": {}
  }
}
```

```bash
POST /_reindex
{
  "source": {
    "index": "favorite_candy_v1"
  },
  "dest": {
    "index": "favorite_candy_v2"
  }
}
```

```bash
POST /_aliases
{
  "actions": {
    "remove": {
      "index": "favorite_candy_v1", 
      "alias": "favorite_candy"  
    },
    "add": {
      "index": "favorite_candy_v2", 
      "alias": "favorite_candy"  
    }
  } 
}
```

```bash
GET /favorite_candy/_search
{
  "query": {
    "match_all": {}
  }
}
```

```bash
DELETE /favorite_candy_v1
```

