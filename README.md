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

## To-dos

- [x] Set up database

Basic data population:

- [x] Deities
  - [x] Domains
- [ ] Religions
- [ ] link deities to religion(s)

---

## SPARQL

### Predicates

- P31 instance of
- P279 subclass of
- P1049 worshipped by
- P2925 domain of saint or deity
- P22 father
- P24 mother

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
