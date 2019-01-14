# Setting Elasticsearch

free Elasticsearch bonsai.io

create cluster - free 

[ email used - abhi.docstore@gmail.com ]

---------------

to create new index : 

Go to console

- Create index

```json
// PUT Request --> /twitter 
{
  "acknowledged": true,
  "shards_acknowledged": true,
  "index": "twitter"
}
```

- list indexes 

```json
// GET : /_cat/indices?v
list all the indexes in cluster

```

Create manual entry

```json
// PUT: /twitter/tweets/1

{
    "course" : "kafka for Beginners",
    "instructor" : "Avr",
    "module" : "ElasticSearch"
}

// output

{
  "_index": "twitter",
  "_type": "tweets",
  "_id": "1",
  "_version": 1,
  "result": "created",
  "_shards": {
    "total": 2,
    "successful": 2,
    "failed": 0
  },
  "_seq_no": 0,
  "_primary_term": 1
}
```

print index value

```json
// GET: /twitter/tweets/1

{
  "_index": "twitter",
  "_type": "tweets",
  "_id": "1",
  "_version": 1,
  "found": true,
  "_source": {
    "course": "kafka for Beginners",
    "instructor": "Avr",
    "module": "ElasticSearch"
  }
}
```

Delete index

```json
DELETE: /twitter
```

