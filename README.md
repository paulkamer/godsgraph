# Gods Graph

An experiment with SPARQL, Neo4J and Wikidata.

Fetches the gods of polytheistic religions with their domains and stores them as a graph.

This project is first and foremost a playground for working with SPARQL and graph database. Ultimately, it will provide an API service to query this data in a user-friendly way.

## Technology

- Database: [Neo4j](https://neo4j.com)
- Data population:

  - [SPARQL](https://www.w3.org/TR/sparql11-query/)
  - [Jupyter notebook](https://jupyter.org/)

- Data source: [Wikidata](https://www.wikidata.org/wiki/Wikidata:Main_Page)

## Running

- Copy .env.sample to .env and set values

```sh
docker-compose up -d
```

Open Neo4J browser: http://localhost:47474/browser/

## To-dos

- [x] Set up database

Basic data population:

- [x] Deities
  - [x] Domains
- [x] Religions
- [x] link deities to religion(s)
- [x] link parents, siblings
- [x] prevent duplicate religions (religions vs mythologies)
- [ ] store deity description & 'also known as'
- [ ] Odin not as 'Norse deity', but 'Old Norse religion' https://www.wikidata.org/wiki/Q43610

---

## SPARQL

## Sources

- https://sparql.dev/
- https://sparql.dev/article/Top_10_SPARQL_query_examples_for_beginners.html
- https://jena.apache.org/documentation/query/index.html
- https://jena.apache.org/tutorials/sparql_filters.html

### Predicates

- P31 instance of
- P279 subclass of
- P1049 worshipped by
- P2925 domain of saint or deity
- P22 father
- P24 mother
- P361 part of (https://www.wikidata.org/wiki/Q22989102)

### objects

- Q178885 deity
- Q9174 religion
- Q9134 mythology
- tutelary spirit (Q11681141)

### Test queries

```sparql
-- list of all instances of religions or mythologies
SELECT DISTINCT ?subject ?subjectLabel
WHERE
{
{ ?subject wdt:P31 wd:Q9174 . } # instance_of religion
UNION
{ ?subject wdt:P31 wd:Q9134 . } # instance_of mythology

SERVICE wikibase:label { bd:serviceParam wikibase:language "en" }

?subject rdfs:label ?subjectLabel .
FILTER(LANG(?subjectLabel) = "en") # Filter for English labels
}
```

```
SELECT DISTINCT
  ?deity ?deityLabel
#   ?parentDeity ?parentDeityLabel
#   ?domainOfSaint ?domainOfSaintLabel
#   ?deityOf ?deityOfLabel
  ?religion
#   ?parentReligion
WHERE
{
  { ?deity wdt:P31* wd:Q178885 . }     # instance_of deity

  UNION
  {
#     ?deity wdt:P31* ?parentDeity .      # deities that are an instance of (P31) a parent deity
#     ?parentDeity wdt:P279 wd:Q178885  .  # parent deity that is a "subclass of" (P279) 'deity' (Q178885)
  }

  OPTIONAL { ?deity wdt:P1049* ?religion . }
#   OPTIONAL { ?parentDeity wdt:P1049 ?parentReligion . }

#   ?deity p:P31 ?deity_instance .                  # instance_of (Pax is an instance of a Romain Deity)
#   OPTIONAL { ?deity_instance pq:P642 ?deityOf . } # "of" attribute of the instance_of (Romain Deity OF peace)
#   OPTIONAL { ?deity wdt:P2925  ?domainOfSaint . } # "domain of saint or deity" (P2925) (Mars is deity of War and Agriculture)


  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }

  ?deity rdfs:label ?deityLabel .
  FILTER(LANG(?deityLabel) = "en")
#   FILTER (STR(?deityLabel) = "Mars")
  FILTER ( ?deity != ?religion)
}

ORDER BY ?deityLabel
```

```
SELECT
  ?deity ?deityLabel
#   ?parentDeity ?parentDeityLabel
#   ?domainOfSaint ?domainOfSaintLabel
#   ?deityOf ?deityOfLabel
  ?religion
#   ?parentReligion
WHERE
{
  { ?deity wdt:P31* wd:Q178885 . }     # instance_of deity

  UNION
  {
# #     ?deity wdt:P31* ?parentDeity .      # deities that are an instance of (P31) a parent deity
# #     ?parentDeity wdt:P279 wd:Q178885  .  # parent deity that is a "subclass of" (P279) 'deity' (Q178885)
  }

  OPTIONAL { ?deity wdt:P1049* ?religion . }
#   OPTIONAL { ?parentDeity wdt:P1049 ?parentReligion . }

#   ?deity p:P31 ?deity_instance .                  # instance_of (Pax is an instance of a Romain Deity)
#   OPTIONAL { ?deity_instance pq:P642 ?deityOf . } # "of" attribute of the instance_of (Romain Deity OF peace)
#   OPTIONAL { ?deity wdt:P2925  ?domainOfSaint . } # "domain of saint or deity" (P2925) (Mars is deity of War and Agriculture)


  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }

  ?deity rdfs:label ?deityLabel .
  FILTER(LANG(?deityLabel) = "en")
  FILTER (STR(?deityLabel) = "Mars")
#   FILTER ( ?deity != ?religion)
}

ORDER BY ?deityLabel


SELECT DISTINCT
  ?deity ?deityLabel
  ?religion
WHERE
{
   { ?deity wdt:P31* wd:Q178885 . }
  UNION { }

  OPTIONAL { ?deity wdt:P1049* ?religion . }

  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }

  ?deity rdfs:label ?deityLabel .
  FILTER(LANG(?deityLabel) = "en")
  FILTER (STR(?deityLabel) = "Mars")
#   FILTER ( ?deity != ?religion)
}

ORDER BY ?deityLabel



SELECT DISTINCT
  ?deity ?deityLabel
  ?religion
WHERE
{
   { ?deity wdt:P31* wd:Q178885 . }
  UNION { }

  OPTIONAL { ?deity wdt:P1049* ?religion . }

  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }

  ?deity rdfs:label ?deityLabel .
  FILTER(LANG(?deityLabel) = "en")
#   FILTER (STR(?deityLabel) = "Mars")
#   FILTER ( ?deity != ?religion)
}

ORDER BY ?deityLabel
```
