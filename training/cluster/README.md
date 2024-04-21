# Cluster
Study on Elasticsearch cluster.

## Installation
This repository contains a `docker-compose-cluster.yml` file to set up Elasticsearch Cluster and Kibana using Docker Compose.

## Prerequisites
- Docker installed on your machine
- Docker Compose installed on your machine

## Usage
Run the following command to start an Elasticsearch cluster with 3 nodes (`es01`, `es02` and `es03`) and the Kibana service.
```bash
docker-compose -f docker-compose-cluster.yml up -d es01 es02 es03 kibana
```

 The services will be accessible as follows:

- Elasticsearch: `http://localhost:9200`
- Kibana: `http://localhost:5601`

Access the Kibana Dev Tools tool and check the cluster's health using this command:
```bash
GET /_cluster/health
```

> [!TIP]
> We will see that our cluster status is **green**.

> [!NOTE]
> Elasticsearch cluster status and its meanings:
> * **green**: all primary and replica shards are active;
> * **yellow**: all primary shards are active, but not all replica shards are active;
> * **red**: not all primary shards are active.

We can check who the master node is, how the primary and replica shards are organized for each index created in our Elasticsearch Cluster using this command:
```bash
GET /_cluster/state
```

We can see the statistical information for each node as well, run this command for that.
```bash
GET /_nodes/es01/stats
```

To list all started services, run this command.
```bash
docker-compose -f docker-compose-cluster.yml ps -a
```

Now let's create an index, set the number of primary shards, replicas, and add some documents. We can see how our cluster organizes the `favorite_candy` index among the 3 nodes (`es01`, `es02` and `es03`).
```bash
PUT /favorite_candy
{
  "settings": {
    "number_of_shards": 3,
    "number_of_replicas": 2
  }
}

POST favorite_candy/_doc
{
  "first_name": "Lisa",
  "candy": "Sour Skittles"
}

PUT favorite_candy/_doc/1
{
  "first_name": "John",
  "candy": "Starburst"
}

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

GET favorite_candy/_search
{
  "query": {
    "match_all": {}
  }
}
```

We can stop some nodes in the Elasticsearch cluster and check how the cluster's health is affected, as well as the organization of each shard and replica of the index `favorite_candy`.
```bash
docker-compose -f docker-compose-cluster.yml stop es03
```

> [!WARNING]
> Checking our cluster's health will be **yellow** after shutting down a node. 
> All primary shards are active, but not all replica shards are active (state unassigned).

We can get details of the unassigned state by running the command, look for `favorite_candy`:
```bash
GET /_cluster/allocation/explain
```

We can view index `favorite_candy` statistics by running the command:
```bash
GET /favorite_candy/_stats
```

When stopping another node in the cluster and trying to run our query, we notice a delay in the response and even timeouts in the request.
```bash
docker-compose -f docker-compose-cluster.yml stop es02
docker-compose -f docker-compose-cluster.yml start es02
```

> [!CAUTION]
> Starting the node again and checking the cluster's health, we notice that the status turns **red** (not all primary shards are active) and then quickly returns to **yellow**.

Starting a new node `es04` in the Elasticsearch cluster, we see that the health status returns to **green**. Analyzing the state of our cluster, we notice that the primary shards and replicas have been rearranged among the available nodes.
```bash
docker-compose -f docker-compose-cluster.yml up -d es04
```

Starting the node `es03` again and checking the cluster's state, we see a new organization among the primary shards and replicas of the index `favorite_candy`.
```bash
docker-compose -f docker-compose-cluster.yml start es03
docker-compose -f docker-compose-cluster.yml ps -a
```

## Stopping the services
To stop the services and remove the containers, use the following command:

```bash
docker-compose -f docker-compose-cluster.yml down -v
```

## References
* https://www.elastic.co/blog/every-shard-deserves-a-home
* https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-replication.html
* https://www.elastic.co/guide/en/elasticsearch/reference/7.17/modules-cross-cluster-search.html