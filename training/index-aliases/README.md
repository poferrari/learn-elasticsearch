# Index Aliases and Zero Downtime

First, let's create our index `favorite_candy_v1` with  `number_of_shards=2` and `number_of_replicas=2`.
```bash
PUT /favorite_candy_v1
{
  "settings": {
    "number_of_shards": 2,
    "number_of_replicas": 2
  }
}
```

Next, let's index some documents into the created index.
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

We can search the indexed documents in this index by running the following command:
```bash
GET /favorite_candy_v1/_search
{
  "query": {
    "match_all": {}
  }
}
```

Now, we need to change the number of replicas for our index (`number_of_replicas=1`). This is a setting we can change without needing to recreate the index `favorite_candy_v1`.
```bash
PUT /favorite_candy_v1/_settings
{
  "index": {
    "number_of_replicas": 1
  }
}
```
> However, there are other settings that we cannot change in the existing index.

Creating an alias `favorite_candy` for the index `favorite_candy_v1`.
```bash
PUT /favorite_candy_v1/_alias/favorite_candy
```

> [!NOTE]
> Aliases in Elasticsearch serve as a pointer or alias name to one or multiple indices. They provide a way to refer to an index by a more friendly or logical name, making it easier to manage and search across multiple indices.

This command shows details of the index `favorite_candy_v1` settings.
```bash
GET /favorite_candy_v1/_settings
```

> [!NOTE]
> You can check which index the alias `favorite_candy` points to:
> ```bash
> GET /*/_alias/favorite_candy
> ```
> Or which aliases point to the index `favorite_candy_v1`:
> ```bash
> GET /favorite_candy_v1/_alias/*
> ```

We can index more documents directly through the alias `favorite_candy`.
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

We can also query directly through the alias `favorite_candy`.
```bash
GET /favorite_candy/_search
{
  "query": {
    "match_all": {}
  }
}
```

For some settings, like the number of shards, mappings, analyzers, among others, changing them requires creating a new index.

To avoid having to recreate the same index, using aliases helps us update indices without losing data and without changes to our queries.

For example, we will create a new index `favorite_candy_v2` with different settings from the existing index `favorite_candy_v1`.
```bash
PUT /favorite_candy_v2
{
  "settings": {
    "number_of_shards": 5,
    "number_of_replicas": 1
  }
}

GET /favorite_candy_v2/_search
{
  "query": {
    "match_all": {}
  }
}
```

We can reindex the documents from index `favorite_candy_v1` to index `favorite_candy_v2` using this command:
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

Finally, we remove the alias `favorite_candy` from index `favorite_candy_v1` and add this alias to index `favorite_candy_v2`.
```bash
POST /_aliases
{
  "actions": {
    "remove": {
      "index": "favorite_candy_v1", 
      "alias": "favorite_candy"  
    }   
  } 
}

POST /_aliases
{
  "actions": {   
    "add": {
      "index": "favorite_candy_v2", 
      "alias": "favorite_candy"  
    }
  } 
}
```

Searching by the alias `favorite_candy` returns all available documents from index `favorite_candy_v2`.
```bash
GET /favorite_candy/_search
{
  "query": {
    "match_all": {}
  }
}
```

## Useful commands
Some commands that can help us manage indices:

* This command is used to force the writing of all uncommitted segments to disk and free up memory resources. It's useful to ensure all indexed data is persistently stored on disk, especially before performing backup operations or stopping a node.
```bash
POST /favorite_candy_v1/_flush
```
* This command closes the index `favorite_candy_v1`. When an index is closed, it can't be searched or indexed, but it still takes up space on the disk.
Closing an index can be useful for maintenance or administrative operations without affecting the availability of other indices in the cluster.
```bash
POST /favorite_candy_v1/_close
```
* This command reopens the index `favorite_candy_v1` after it has been closed.
After reopening the index, it will be available again for searches and indexing.
```bash
POST /favorite_candy_v1/_open
```

If the index `favorite_candy_v1` is no longer needed, it can be deleted, provided that the backup is up-to-date and any aliases for this index are removed if they exist.
```bash
DELETE /favorite_candy_v1
```

> [!CAUTION]
> Make sure to be careful when executing the **DELETE** command, as it is an irreversible operation and will result in the permanent loss of the index data.

