# learn-elasticsearch
This repository serves as a comprehensive learning resource for individuals seeking to understand Elasticsearch.

## Installation

```bash
docker-compose -f docker-compose.yml up -d
```

```bash
docker-compose -f docker-compose.yml down -v
```

### Useful tools

#### Elasticsearch
[Elasticsearch](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs.html) is an open-source, scalable, and flexible search and data analysis engine, built on Apache Lucene and ideal for full-text search, log analysis, and infrastructure monitoring.

#### Logstash
[Logstash](https://www.elastic.co/guide/en/logstash/current/index.html) is an open-source tool for collecting, transforming, and sending data to various destinations, such as Elasticsearch, facilitating the centralization and analysis of logs and events.

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