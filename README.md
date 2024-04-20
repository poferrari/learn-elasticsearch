# learn-elasticsearch
Repository for Elasticsearch studies.

## Installation
This repository contains a `docker-compose.yml` file to set up Elasticsearch, Logstash, and Kibana (Elastic Stack) using Docker Compose.

## Prerequisites
- Docker installed on your machine
- Docker Compose installed on your machine

## Usage
Run the following command to start the services:
```bash
docker-compose -f docker-compose.yml up -d
```

This command will start Elasticsearch, Logstash, and Kibana services with the configurations defined in the `docker-compose.yml` file. The services will be accessible as follows:

- Elasticsearch: `http://localhost:9200`
- Kibana: `http://localhost:5601`

## Run details
First, we started our Elasticsearch service, then we initiated the Logstash service. 

In Logstash service, a [configuration](logstash/logstash.conf) was made to input the [PEP dataset](logstash/202401_PEP.csv) into Elasticsearch.

Finally, we started the Kibana service for interaction with Elasticsearch through Dev Tools.

## Stopping the services
To stop the services and remove the containers, use the following command:

```bash
docker-compose -f docker-compose.yml down -v
```

### Useful tools

#### Elasticsearch
[Elasticsearch](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs.html) is an open-source, scalable, and flexible search and data analysis engine, built on Apache Lucene and ideal for full-text search, log analysis, and infrastructure monitoring.

#### Logstash
[Logstash](https://www.elastic.co/guide/en/logstash/current/index.html) is an open-source tool for collecting, transforming, and sending data to various destinations, such as Elasticsearch, facilitating the centralization and analysis of logs and events.

#### Kibana
[Kibana](https://www.elastic.co/guide/en/kibana/7.17/index.html) is an open-source data visualization and exploration tool designed for Elasticsearch. It provides a user-friendly interface to explore, visualize, and analyze data stored in Elasticsearch indices. Kibana tools enable users to interact with their data through various charts, graphs, and maps, facilitating data-driven decision-making and operational intelligence.

##### Dev Tools
[Dev Tools](https://www.elastic.co/guide/en/kibana/7.17/devtools-kibana.html) provides a user-friendly interface for interacting with Elasticsearch directly through HTTP requests. Execute queries, manage indices, and debug configs right from the UI. A must-have for devs and admins.

#### Multi Elasticsearch Head 
Multi Elasticsearch Head is a [Chrome extension](https://chromewebstore.google.com/detail/multi-elasticsearch-head/cpmmilfkofbeimbmgiclohpodggeheim) graphical interface that allows users to interact with Elasticsearch.

## Data sources
Using a dataset of [politically exposed persons (PEP)](https://portaldatransparencia.gov.br/download-de-dados/pep) provided by the Transparency Portal of the Brazilian Government.

## Query examples

### Count the number of documents in an index
```bash
curl -X POST "localhost:9201/your_index/_count" -H 'Content-Type: application/json' -d'
{
  "query": {
    "match_all": {}
  }
}'
```
You can find an example JSON query in the [`query-examples/1-count-documents-index.json`](query-examples/1-count-documents-index.json) file.

> Other examples you can find in the folder [`query-examples`](query-examples).

## Trainings
Specific trainings on certain Elasticsearch topics, you can find in the folder [`training`](training)

## References
* https://www.elastic.co/blog/benchmarking-and-sizing-your-elasticsearch-cluster-for-logs-and-metrics
* https://aws.amazon.com/solutions/case-studies/pinterest-case-study/
* https://www.infoq.com/br/articles/elasticsearch-ideal-para-meu-projeto/
* https://github.com/dzharii/awesome-elasticsearch
* https://github.com/codingexplained/complete-guide-to-elasticsearch