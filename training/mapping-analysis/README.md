# Configuring Analyzers
Text analysis allows Elasticsearch to perform a full-text search, where the search returns all relevant results instead of just exact matches.

## Built-in Analyzers
To check how the Analyzers work, we can execute the following commands:

* **Simple** analyzer (splits the text on anything that isn’t a letter, and lowercases the terms)
```bash
GET /_analyze
{
  "analyzer": "simple",
  "text": "Set the shape to semi-transparent by calling set_trans(5)."
}
```

* **Standard** analyzer (lowercase, removes most punctuation and Unicode Consortium)
```bash
GET /_analyze
{
  "analyzer": "standard",
  "text": "Set the shape to semi-transparent by calling set_trans(5)."
}
```

* **Whitespace** analyzer (splits the text on whitespace. It doesn’t lowercase)
```bash
GET /_analyze
{
  "analyzer": "whitespace",
  "text": "Set the shape to semi-transparent by calling set_trans(5)."
}
```

There are also specific analyzers for each language (**english**, **portuguese**, **spanish** and others). For example:
```bash
GET /_analyze
{
  "analyzer": "english",
  "text": "Set the shape to semi-transparent by calling set_trans(5)."
}

GET /_analyze
{
  "analyzer": "portuguese",
  "text": "Defina a forma como semitransparente chamando set_trans(5)."
}

GET /_analyze
{
  "analyzer": "spanish",
  "text": "Establece la forma en semitransparente llamando a set_trans(5)."
}
```

> [!NOTE]
> With this command, we can understand how the analyzer works without needing to create any Index, Mapping, or insert documents.

## Mapping with Analyzers
When creating an index, we can configure in the mapping which analyzer will be used in the desired property.
```bash
PUT /my_index
{
  "mappings": {
      "properties": {
        "title": {
          "type": "text",
          "analyzer": "standard"
        }
      }
  }
}
```

We can test the result of the analyzer set for a property of the index. Run the following command to view:
```bash
GET /my_index/_analyze
{
  "field": "title",
  "text": "I'm not happy about the foxes."
}
```

We can create **custom analyzers** for specific cases. In the following example, we use a **stopwords** filter to ignore the defined words.
```bash
PUT /my_index
{
  "settings": {
    "analysis": {
      "filter": {
        "my_stopwords": {
          "type": "stop",
          "stopwords": [ "the", "a", "an", "and", "are", "as", "at", "be", "but", "by" ]
        }
      },
      "analyzer": {
        "my_analyzer": {
          "type": "custom",
          "tokenizer": "standard",
          "filter": [ "lowercase", "asciifolding", "my_stopwords"]
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "title": {
        "type": "text",
        "analyzer": "my_analyzer"
      }
    }
  }
}
```

> [!TIP]
> To create a new index with the same name as an existing one, you can delete the index since we don't have any indexed documents in this index, and we are in a learning environment.
> ```bash
> DELETE /my_index
> ```

```bash
PUT /my_index
{
  "settings": {
    "analysis": {
      "filter": {
        "english_stop": {
          "type": "stop",
          "stopwords": "_english_"
        },
        "english_keywords": {
          "type": "keyword_marker",
          "keywords": []
        },
        "english_stemmer": {
          "type": "stemmer",
          "language": "english"
        },
        "english_possessive_stemmer": {
          "type": "stemmer",
          "language": "possessive_english"
        }
      },
      "analyzer": {
        "my_english": {
          "tokenizer": "standard",
          "filter": [
            "english_possessive_stemmer",
            "lowercase",
            "english_stop",
            "english_keywords",
            "english_stemmer"
          ]
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "title": {
        "type": "text",
        "analyzer": "my_english"
      }
    }
  }
}
```

```bash
GET /my_index/_analyze
{
  "field": "title",
  "text": "Many people believe that education is the key to success. By acquiring knowledge and skills, individuals are able to pursue their passions, advance their careers, and make a difference in the world. From early childhood education to higher levels of learning, the opportunities for growth and development are endless. Education not only opens doors to new possibilities but also helps to create a more informed and enlightened society. Investing in education is investing in the future."
}

GET /my_index/_analyze
{
  "field": "title",
  "text": "key keyless keyword primary-key"
}
```

```bash
PUT /my_index
{
  "settings": {
    "analysis": {
      "analyzer": {
        "pt_std": {
          "type": "portuguese",
          "stopwords": "_portuguese_"
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "title": {
        "type": "text",
        "fields": {
          "pt": {
            "type": "text",
            "analyzer": "portuguese"
          },
          "pt_std": {
            "type": "text",
            "analyzer": "pt_std"
          }
        }
      }
    }
  }
}
```

```bash
GET /my_index/_analyze
{
  "field": "title",
  "text": "A certificação de metodologias que nos auxiliam a lidar com a valorização de fatores subjetivos oferece uma interessante oportunidade para verificação do retorno esperado a longo prazo."
}

GET /my_index/_analyze
{
  "field": "title.pt",
  "text": "A certificação de metodologias que nos auxiliam a lidar com a valorização de fatores subjetivos oferece uma interessante oportunidade para verificação do retorno esperado a longo prazo."
}

GET /my_index/_analyze
{
  "field": "title.pt_std",
  "text": "A certificação de metodologias que nos auxiliam a lidar com a valorização de fatores subjetivos oferece uma interessante oportunidade para verificação do retorno esperado a longo prazo."
}
```

```bash
PUT /my_index
{
  "settings": {
    "analysis": {
      "analyzer": {
        "pt_std": {
          "type": "standard",
          "stopwords": "_portuguese_"
        },
        "en_std": {
          "type": "standard",
          "stopwords": "_english_"
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "title": {
        "type": "text",
        "analyzer": "standard",
        "fields": {
          "pt": {
            "type": "text",
            "analyzer": "portuguese"
          },
          "en": {
            "type": "text",
            "analyzer": "english"
          },
          "pt_std": {
            "type": "text",
            "analyzer": "pt_std"
          },
          "en_std": {
            "type": "text",
            "analyzer": "en_std"
          },
          "keyword": {
            "type": "keyword"
          }
        }
      }
    }
  }
}
```

This command serves to retrieve a list of all indices in Elasticsearch, providing various details such as health status, disk space usage, and other relevant information.
```bash
GET /_cat/indices?v
```

### Mapping
The index attribute controls how the string will be indexed. It can contain one of three values:
* **analyzed**: First analyze the string and then index it. In other words, index this field as full text.
* **not_analyzed**: Index this field, so it is searchable, but index the value exactly as specified. Do not analyze it.
* **no**: Don’t index this field at all. This field will not be searchable.

These four fields would all be considered to be empty, and would not be indexed:
* `"null_value": null`
* `"empty_array": []`
* `"array_with_null_value": [ null ]`

## Refences
* https://www.elastic.co/guide/en/elasticsearch/reference/7.17/analysis-overview.html