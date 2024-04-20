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

> [!NOTE]
> Elasticsearch cluster status and its meanings:
> * **green**: all primary and replica shards are active;
> * **yellow**: all primary shards are active, but not all replica shards are active;
> * **red**: not all primary shards are active;