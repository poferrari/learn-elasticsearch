# Query DSL
Query DSL in Elasticsearch offers a powerful way to craft precise search queries using JSON syntax.

To learn about query DSL, we will create the `customers` index with the following settings and mappings.

```bash
PUT /customers
{
  "settings": {
    "number_of_shards": 1,
    "number_of_replicas": 1,
    "analysis": {
      "filter": {
        "my_stopwords": {
          "type": "stop",
          "stopwords": [ "de", "do", "da", "dos", "das", "e" ]
        }
      },
      "analyzer": {
        "my_standard": {
          "tokenizer": "standard",
          "filter": [ "lowercase", "asciifolding", "my_stopwords" ]
        }, 
        "pt_stp": {
          "type": "portuguese",
          "stopwords": "_portuguese_"
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "name": {
        "type": "text",
        "analyzer": "standard",
        "fields": {
          "keyword": {
            "type": "keyword",
            "ignore_above": 256            
          },
          "std": {
            "type": "text",
            "analyzer": "my_standard"
          },
          "br": {
            "type": "text",
            "analyzer": "brazilian"
          },
          "pt_std": {
            "type": "text",
            "analyzer": "pt_stp"
          }
        }
      },
      "date_of_birth": {
        "type": "date"
      },
      "profile_aliases": {
        "type": "text",
        "analyzer": "my_standard"
      },
      "tax_id": {
        "type": "keyword",
        "ignore_above": 50
      },
      "salary": {
        "type": "float"
      }
    }
  }
}
```

These commands showcase the settings and mappings created for the `customers` index.
```bash
GET /customers/_settings
GET /customers/_mapping
```

### Testing Analyzers
(`name`, `name.keyword`, `name.std`, `name.br` and `name.pt_std`)
```bash
GET /customers/_analyze
{
  "field": "name",
  "text" : "José Victor Ferreira da Silva Silvestre"
}
```

### Index Some Data with Bulk
Let's register some documents in the `customers` index. At this point, we'll use fictitious data solely for the purpose of learning about Elasticsearch.
```bash
POST /customers/_doc/_bulk
{ "index": { "_id": 1 } }
{ "name": "Tarcísio Meira", "date_of_birth": "1935-10-05", "profile_aliases": ["Tarcísio", "Meira", "Tarcisão", "Galã das antigas"], "tax_id": 321654987, "salary": 8700.00 }
{ "index": { "_id": 2 } }
{ "name": "Larissa de Macedo Machado", "date_of_birth": "1993-03-30", "profile_aliases": ["Anitta", "Anira", "Anira do Brasil", "Poderosa"], "tax_id": 852963741, "salary": 17500.00 }
{ "index": { "_id": 3 } }
{ "name": "Ivete Maria Dias de Sangalo Cady", "date_of_birth": "1972-05-27", "profile_aliases": ["Ivete Sangalo", "Veveta", "Rainha do Axé", "Ivete"], "tax_id": 741258963,"salary": 8500.00 }
{ "index": { "_id": 4 } }
{ "name": "Thiago André Barbosa", "date_of_birth": "1983-03-11", "profile_aliases": ["Thiaguinho", "TG", "Thiagão", "Pagodeiro"], "tax_id": 963258741, "salary": 6200.00}
{ "index": { "_id": 5 } }
{ "name": "Cláudia Cristina Leite Inácio Pedreira", "date_of_birth": "1980-07-10", "profile_aliases": ["Claudia Leitte", "Claudinha", "Leitte", "Cacau", "Rainha do Axé"], "tax_id": 789456123, "salary": 4800.00 }
{ "index": { "_id": 6 } }
{ "name": "Larissa de Macedo Magalhães", "date_of_birth": "1955-11-02", "profile_aliases": ["Larissa Magalhães"], "tax_id": 123456789, "salary": 8000.00 }
{ "index": { "_id": 7 } }
{ "name": "Machado Larissa de Macedo", "date_of_birth": "1991-05-30", "profile_aliases": ["Machado Larissa"], "tax_id": 987654321, "salary": 8500.00 }
{ "index": { "_id": 8 } }
{ "name": "Larissa Macedo Marques", "date_of_birth": "1983-06-12", "profile_aliases": ["Larissa Marques", "Marquesa"], "tax_id": 456123789, "salary": 7000.00 }
{ "index": { "_id": 9 } }
{ "name": "Larissa Machado de Melo", "date_of_birth": "1993-05-22", "profile_aliases": ["Larissa Melo", "DJ Melo"], "tax_id": 321654987, "salary": 7000.00 }
{ "index": { "_id": 10 } }
{ "name": "Luiz de Macedo Machado", "date_of_birth": "1973-03-30", "profile_aliases": ["Luizito", "Macedão"], "tax_id": 789456123, "salary": 6500.00 }
{ "index": { "_id": 11 } }
{ "name": "Luan Rafael Domingos Santana", "date_of_birth": "1991-03-13", "profile_aliases": ["Luan Santana", "Luanzito", "Sertanejo", "Santana", "Gurizinho"], "tax_id": 654321987, "salary": 7100.00 }
{ "index": { "_id": 12 } }
{ "name": "Lima Duarte", "date_of_birth": "1930-03-29", "profile_aliases": ["Lima", "Duarte", "José de Duarte", "Grande veterano"], "tax_id": 123456789, "salary": 8500.00 }
{ "index": { "_id": 13 } }
{ "name": "Susana Vieira", "date_of_birth": "1942-08-23", "profile_aliases": ["Susu", "Rainha das novelas", "Vieira", "Susana"], "tax_id": 987654321, "salary": 9200.00 }
{ "index": { "_id": 14 } }
{ "name": "Tony Ramos", "date_of_birth": "1948-08-25", "profile_aliases": ["Tonico", "Ramos", "Tony", "Ator consagrado"], "tax_id": 456789123, "salary": 8900.00 }
{ "index": { "_id": 15 } }
{ "name": "Fernanda Montenegro", "date_of_birth": "1929-10-16", "profile_aliases": ["Montenegro", "Fernanda", "Grande dama da TV", "Fernandinha"], "tax_id": 789123456, "salary": 9300.00 }
{ "index": { "_id": 16 } }
{ "name": "Ana Carolina", "date_of_birth": "1992-03-18", "profile_aliases": ["Aninha", "Carol", "Ana Carol", "Carolininha"], "tax_id": 246810975, "salary": 4800.00 }
{ "index": { "_id": 17 }}
{ "name": "Paulo Henrique", "date_of_birth": "1985-07-10", "profile_aliases": ["PH", "Paulinho", "Paulão", "Henri"], "tax_id": 135792468, "salary": 6200.00}
{ "index": { "_id": 18 }}
{ "name": "Camila Oliveira", "date_of_birth": "1998-12-05", "profile_aliases": ["Camilinha", "Mila", "Cami", "Lia"],"tax_id": 987654321, "salary": 5400.00}
{ "index": { "_id": 19 } }
{ "name": "Fernanda Cristina", "date_of_birth": "1982-09-30", "profile_aliases": ["Fê", "Fernandinha", "Cris", "Fernandona"], "tax_id": 654321987, "salary": 5800.00 }
{ "index": { "_id": 20 } }
{ "name": "Aline Oliveira", "date_of_birth": "1990-06-12", "profile_aliases": ["Ali", "Linha", "Line", "Alininha"], "tax_id": 852963741, "salary": 5200.00 }
{ "index": { "_id": 21 } }
{ "name": "Rodrigo Souza", "date_of_birth": "1987-03-25", "profile_aliases": ["Rô", "Rodriguinho", "Souzinha", "Rodrigão"], "tax_id": 963258741, "salary": 6000.00 }
{ "index": { "_id": 22 } }
{ "name": "Juliana Santos", "date_of_birth": "1995-11-08", "profile_aliases": ["Ju", "Julia", "Jujuba", "Santinha"], "tax_id": 741258963, "salary": 5500.00 }
{ "index": { "_id": 23 } }
{ "name": "Ricardo Augusto", "date_of_birth": "1978-04-20", "profile_aliases": ["Ricardinho", "Rick", "Dudu", "Augustinho"], "tax_id": 789456123, "salary": 6700.00 }
{ "index": { "_id": 24 }}
{ "name": "Paulo Henrique", "date_of_birth": "1985-07-10", "profile_aliases": ["PH", "Paulinho", "Paulão", "Henri"], "tax_id": 135792468, "salary": 6200.00}
{ "index": { "_id": 25 }}
{ "name": "Camila Oliveira", "date_of_birth": "1998-12-05", "profile_aliases": ["Camilinha", "Mila", "Cami", "Lia"],"tax_id": 987654321, "salary": 5400.00}
```

> [!NOTE]
> Bulk API allows us to make multiple create, index, update, or delete requests in a single step.

## Most Important Queries and Filters
```bash
GET /customers/_search
{
  "query": {
    "match_all": {}
  }
}
```

### Finding Exact Values
```bash
GET /customers/_search
{
  "query": {
    "term": {
      "_id" : 1
    }
  }
}
```

```bash
GET /customers/_search
{
  "query": {
    "term": {
      "name.keyword": "Tarcísio Meira"
    }
  }
}
```

### Range Filter

* **gt**: > greater than;
* **lt**: < less than;
* **gte**: >= greater than or equal to;
* **lte**: <= less than or equal to;

```bash
GET /customers/_search
{
  "query": {
    "range": {
      "salary": {
        "gt": 7000.00,
        "lt": 10000.00
      }
    }
  }
}
```

```bash
GET /customers/_search
{
  "query": {
    "range": {
      "date_of_birth": {
        "gte": "1993-03-30"
      }
    }
  }
}
```

### Ordering Results
```bash
GET /customers/_search
{
  "query": {
    "match_all": {}
  },
  "sort": [
    { "date_of_birth": { "order": "desc" }},
    "_score"
  ]
}
```

### Matching and Analyzer
(`name`, `name.keyword`, `name.std`, `name.br` and `name.pt_std`)
```bash
GET /customers/_search
{
  "query": {
    "match": {
      "name": "tarcisio"
    }
  }
}
```

### Understanding the Score
```bash
GET /customers/_search
{
 "query": {
    "match": {
      "name.std": "tarcisio"
    }
  },
  "explain": true
}
```

### Validating Queries
```bash
GET /customers/_validate/query
{
  "query": {
    "name" : {
      "match" : "tarcisio"
    }
  }
}
```

#### Understanding Errors
```bash
GET /customers/_validate/query?explain
{
  "query": {
    "name" : {
      "match" : "tarcisio"
    }
  }
}
```

### Match Phrase Query
```bash
GET /customers/_search
{
  "query": {
    "match_phrase": {
      "name.pt_std": "larissa de macedo"
    }
  }
}
```

### Prefix Query
```bash
GET /customers/_search
{
  "query": {
    "prefix": {
      "name.pt_std": "lari"
    }
  }
}
```

### Match Query and return fields
```bash
GET /customers/_search
{
  "_source": [ "name", "profile_aliases" ], 
  "query": {
    "match": {
      "name": {
        "query": "macedo magalha", 
        "minimum_should_match": "80%"
      }
    }
  }
}
```

### Pagination
```bash
GET /customers/_search
{
  "from": 2,
  "size": 1,
  "query": {
    "match": {
      "name": {
        "query": "macedo"        
      }
    }
  }
}
```

> [!WARNING]
> index.max_result_window (from+size > 10.000)

```bash
GET /customers/_search
{
  "size": 2,
  "query": {
    "match": {
      "name": {
        "query": "macedo"        
      }
    }
  },
  "sort": [
    {
      "date_of_birth": { "order": "desc" }
    },
    {
      "_id": { "order": "desc" }
    }
  ],
  "search_after": [
    675561600000,
          "7"
  ]
}
```

### Aggregations
```bash
GET /customers/_search
{   
  "query": {
    "match": {
      "name": {
        "query": "larrissa macedo magalhes",
        "operator": "or"         
      }
    }
  },
 "aggs": {
   "salaries": {
      "terms": {
        "field": "salary"
      } 
   },
   "ages": {
     "terms": {
        "field": "date_of_birth"
      } 
   } 
 }
}
```

### Fuzziness (Fuzzy matching)

The fuzziness parameter can be set to AUTO, which results in the following maximum edit distances:
* 0 for strings of one or two characters;
* 1 for strings of three, four, or five characters;
* 2 for strings of more than five characters.

```bash
GET /customers/_search
{
  "query": {
    "fuzzy": {
      "name": {
        "value": "laryssa",
        "fuzziness": 1
      }
    }
  }
}
```

#### Fuzzy match Query
```bash
GET /customers/_search
{
  "query": {
    "multi_match": {
      "fields": [ "name", "profile_aliases" ],
        "query": "larisa macedo machado",
        "fuzziness": "AUTO"
      }
    }
  }
}
```

## Proximity Matching
```bash
POST /my_index_closer/my_type/_bulk
{ "index": { "_id": 1 }}
{ "title": "The quick brown fox jumps over the quick dog" }
{ "index": { "_id": 2 }}
{ "title": "The quick brown fox jumps over the lazy dog" }
{ "index": { "_id": 3 }}
{ "title": "The quick brown fox" }
{ "index": { "_id": 4 }}
{ "title": "Brown fox brown dog" }
```

```bash
POST /my_index_closer/_search
{
  "query": {
    "match_phrase": {
      "title": {
        "query": "quick dog",
        "slop": 50
      }
    }
  }
}
```

> [!TIP]
> The `slop` parameter in Elasticsearch's match_phrase query specifies the maximum number of positions that the words in the query can be moved to match a document. A higher slop allows more flexibility in word order, while a slop of 0 requires exact word order.

### Producing Shingles
```bash
PUT /my_index_bigram
{
  "settings": {
    "analysis": {
      "filter": {
        "bigram_filter": {
          "type": "ngram",
          "min_gram": 2,
          "max_gram": 2,
          "output_unigrams":"false"
        }
      },
      "analyzer": {
        "bigrams": {
          "type": "custom",
          "tokenizer": "standard",
          "filter": [
            "lowercase",
            "porter_stem", 
            "bigram_filter"
          ]
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "title": {
        "type": "text",
        "fields": {
          "shingles": {
            "type": "text",
            "analyzer": "bigrams"
            }
          }
      }
    }
  }
}
```

```bash
GET /my_index_bigram/_analyze
{
  "field": "title.shingles",
  "text" : "Sue ate the alligator"
}
```

```bash
POST /my_index_bigram/_doc/_bulk
{ "index": { "_id": 1 }}
{ "title": "Sue ate the alligator" }
{ "index": { "_id": 2 }}
{ "title": "The alligator ate Sue" }
{ "index": { "_id": 3 }}
{ "title": "Sue never goes anywhere without her alligator skin purse" }
```

```bash
GET /my_index_bigram/_search
{
  "query": {
    "match": {
     "title": "the hungry alligator ate sue"
    }
  }
}

GET /my_index_bigram/_search
{
  "query": {
    "bool": {
      "must": {
        "match": {
          "title": "the hungry alligator ate sue"
        }
      },
      "should": {
        "match": {
          "title.shingles": "the hungry alligator ate sue"
        }
      }
    }
  }
}
```

## Highlighting
```bash
GET /customers/_search
{
  "query": {
    "match": {
      "name.pt_std": {
        "query": "macedo magalhaes", 
        "minimum_should_match": "75%"
      }
    }
  },
  "highlight": {
    "number_of_fragments": 9,
    "fragment_size": 15,
    "require_field_match": "true",
    "pre_tags": [
      "<em>"
    ],
    "post_tags": [
      "</em>"
    ],
    "fields" : {
      "name.pt_std": {
        "number_of_fragments": 1
      }
    }
  }
}
```

> [!NOTE]
> Types of queries for searching across multiple fields of an index in Elasticsearch:
> * **best_fields**: to find documents that match any field but scores them as if only the best field matched. It's useful when one field is more important than the others, and the score should reflect this importance. 
>   * **Example**: if you're searching for a product, where the name of the product is more important than its description;
> * **most_fields**: to find documents that match most of the query terms across all analyzed fields. It's suitable when the query terms should match across multiple fields, and documents that match most of these terms are considered more relevant. 
>   * **Example**: in a search scenario where you want to find documents related to a topic across various fields like title, description, and tags;
> * **cross_fields**: to find documents where all terms must appear in any field to match. It's useful when you want to search across multiple fields as a single field. 
>   * **Example**: when searching for a person's name in a database where the name could be in any field like first name, last name, or nickname;

```bash
GET /customers/_search
{
  "query": {
    "multi_match": {
      "query": "Larissa de Macedo", 
      "type": "best_fields",  
      "fields": [ "name^10", "profile_aliases" ]
    }
  },
  "highlight": {
    "fields": {
      "name": {},
      "profile_aliases": {}
    }
  }
}
```

> [!NOTE]
> **Boost**: `name^10`, it means it will have ten times more weight in the scoring (score) than the `profile_aliases` field.

## Combining Multiple Clauses

### Bool Filter
* **must**: all of these clauses must match. The equivalent of `AND`.
* **must_not**: all of these clauses must not match. The equivalent of `NOT`.
* **should**: at least one of these clauses must match. The equivalent of `OR`.

```bash
GET /customers/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "name.std": "larissa"
          }
        }
      ],
      "must_not": [
        {
          "match": {
            "name.std": "magalhaes"
          }
        }
      ],
      "should": [
        {
          "match": {
            "name.std": "machado"
          }
        }
      ]
    }
  }
}
```

```bash
GET /customers/_search
{
  "query": {
    "bool": {
      "should": [
        {
          "term": {
            "tax_id": {
              "value": "852963741",
              "boost": 2
            }
          }
        },
        {
          "multi_match": {
            "query": "larissa Macedo", 
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
```

```bash
GET /customers/_search
{
  "query": {
    "bool": {
      "must_not": [
        {
          "match": {
            "name.std": "luiz"
          }
        }
      ],  
      "should": [
        {
          "bool": {
            "must": [
              {
                "match": {
                  "name.std": {
                    "query": "macedo",
                    "boost": 5
                  }
                }
              }
            ],
            "should": [
              {
                "match": {
                  "profile_aliases": "larissa"
                }
              }
            ]
          }
        },
        {
          "match": {
            "name.std": "macedo"
          }
        }
      ],
      "minimum_should_match": 1,
      "filter": [
        {
          "range": {
            "salary": {
              "gte": 5000.00,
              "lte": 10000.00
            }
          }
        }
      ]
    }
  },
  "highlight": {
    "fields": {
      "name.std": {},
      "profile_aliases": {}
    }
  }
}
```

## Synonyms 
```bash
PUT /my_products_synonyms
{
  "settings": {
    "index": {
      "analysis": {
        "analyzer": {
          "synonyms_analyzer": {
            "tokenizer": "standard",
            "filter": [ "lowercase", "synonyms_filter" ]
          }
        },
        "filter": {
          "synonyms_filter": {
            "type": "synonym_graph",
            "lenient": true,
            "synonyms": [
              "pc => personal computer", 
              "computer, pc, laptop",
              "sea biscuit, sea biscit => seabiscuit"              
            ]
          }
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "product_name": {
        "type": "text",
        "analyzer": "synonyms_analyzer",
        "search_analyzer": "synonyms_analyzer"
      }
    }
  }
}
```

```bash
POST my_products_synonyms/_analyze
{
  "field": "product_name",
  "text": ["pc", "biscuit"]
}
```

```bash
POST /_bulk
{ "index" : { "_index": "my_products_synonyms",  "_id" : 1 } }
{ "product_name": "Laptop X1 i7 8gb RAM" }
{ "index" : { "_index": "my_products_synonyms",  "_id" : 2 } }
{ "product_name": "PC Gaming XYZ" }
{ "index" : { "_index": "my_products_synonyms",  "_id" : 3 } }
{ "product_name": "Laptop Z1 i7 6gb RAM" }
{ "index" : { "_index": "my_products_synonyms",  "_id" : 4 } }
{ "product_name": "Desktop Computer" }
{ "index" : { "_index": "my_products_synonyms",  "_id" : 5 } }
{ "product_name": "Laptop X2 i5 4gb RAM" }
{ "index" : { "_index": "my_products_synonyms",  "_id" : 6 } }
{ "product_name": "Laptop Z1 i7 6gb RAM" }
{ "index" : { "_index": "my_products_synonyms",  "_id" : 7 } }
{ "product_name": "Sea Biscit Deluxe Edition" }
{ "index" : { "_index": "my_products_synonyms",  "_id" : 8 } }
{ "product_name": "Seabiscuit Racing Series" }
```

```bash
POST my_products_synonyms/_search
{
  "query": { 
    "match": { 
      "product_name": {
        "query": "notebook i7 8gb",
        "minimum_should_match": "60%"
      }
    }
  },
  "highlight": {
    "fields": {
      "product_name": {
        "type": "plain",
        "pre_tags": ["<b>"],
        "post_tags": ["</b>"]
      }
    }
  }
}
```

## Script
```bash
POST /store/products/_bulk
{ "index" : { "_id" : 1 } }
{ "name": "Produto 1", "unit_price": 10.00, "quantity": 5}
{ "index" : { "_id" : 2 } }
{ "name": "Produto 2", "unit_price": 15.00, "quantity": 3}
{ "index" : { "_id" : 3 } }
{ "name": "Produto 3", "unit_price": 20.00, "quantity": 2}
{ "index" : { "_id" : 4 } }
{ "name": "Produto 4", "unit_price": 20.00, "quantity": 10}
{ "index" : { "_id" : 5 } }
{ "name": "Produto 5", "unit_price": 50.00, "quantity": 2}
```

```bash
POST /store/_search
{
 "query": {
    "script_score": {
      "query": { 
        "match_all": {}
      },
      "script": {
        "source": "doc['unit_price'].value * doc['quantity'].value"
      }
    }
  }
}
```

```bash
PUT _scripts/total_value
{
  "script": {
    "source": "doc['unit_price'].value * doc['quantity'].value",
    "lang": "painless"
  }
}
```

```bash
GET /store/_search
{
  "query": {
    "match_all": {}
  },
  "script_fields": {
    "total_value": {
      "script": {
        "id": "total_value"
      }
    }
  },
  "sort": [
    {
      "_script": {
        "script": {
          "id": "total_value"
        },
        "type": "number",
        "order": "desc"
      }
    }
  ],
  "fields": ["name", "unit_price", "quantity", "total_value"]
}
```

