# Neo4j

Neo4j Cypher

CALL db.schema.visualization() - data model of the graph db

CALL db.schema.nodeTypeProperties() - property types for each node in the graph db

CALL db.schema.relTypeProperties() - property types for each relationships in the graph

SHOW CONSTRAINTS - uniqueness constraint indexes in the graph

Testing Equality:
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE p.name = 'Tom Hanks'
AND m.year = 2013
RETURN m.title

Testing Inequality:
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE p.name <> 'Tom Hanks'
AND m.title = 'Captain Phillips'
RETURN p.name

Testing less than or greater than:
MATCH (m:Movie) WHERE m.title = 'Toy Story'
RETURN
    m.year < 1995 AS lessThan, //  Less than (false)
    m.year <= 1995 AS lessThanOrEqual, // Less than or equal(true)
    m.year > 1995 AS moreThan, // More than (false)
    m.year >= 1995 AS moreThanOrEqual // More than or equal (true)

Testing Ranges:
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE p.name = 'Tom Hanks'
AND  2005 <= m.year <= 2010
RETURN m.title, m.released

MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE p.name = 'Tom Hanks'
OR m.title = 'Captain Phillips'
RETURN p.name, m.title

Testing null property values:
MATCH (p:Person)
WHERE p.died IS NOT NULL
AND p.born.year >= 1985
RETURN p.name, p.born, p.died

MATCH (p:Person)
WHERE p.died IS NULL
AND p.born.year <= 1922
RETURN p.name, p.born, p.died

Testing labels or patterns:
MATCH (p:Person)
WHERE  p.born.year > 1960
AND p:Actor
AND p:Director
RETURN p.name, p.born, labels(p)
Fetches Person who is born after 1960 and is a Actor and a Director

MATCH (p:Person)-[:ACTED_IN]->(m:Movie)<-[:DIRECTED]-(p)
WHERE  p.born.year > 1960
RETURN p.name, p.born, labels(p), m.title
Fetches Person who is born after 1960 and is both Acted and Director in same movie

Discovering the relationship type:
MATCH (p:Person)-[r]->(m:Movie)
WHERE  p.name = 'Tom Hanks'
RETURN m.title AS movie, type(r) AS relationshipType

Testing list inclusion:
MATCH (m:Movie)
WHERE "Israel" IN m.countries
RETURN m.title, m.languages, m.countries

Cypher to return director who has directed movie in 2000 and belongs to horror genre
MATCH(d:Person)-[:DIRECTED]->(m:Movie)-[:IN_GENRE]->(g:Genre)
WHERE m.year = 2000 AND g.name="Horror"
RETURN d.name

Find People Born in the Fifties
Using the sandbox on the right, write and execute a query to return people born in the 1950’s (1950 - 1959) that are both Actors and Directors.
How many Person nodes are returned?
MATCH(p:Person)
WHERE p.born.year >= 1950 AND p.born.year < 1960 AND p:Actor AND p:Director
RETURN p.name


MATCH (m:Movie)
WHERE  m.title STARTS WITH 'Toy Story'
RETURN m.title, m.released

MATCH (m:Movie)
WHERE  m.title ENDS WITH ' I'
RETURN m.title, m.released

MATCH (m:Movie)
WHERE  m.title CONTAINS 'River'
RETURN m.title, m.released

MATCH (p:Person)
WHERE toLower(p.name) ENDS WITH 'demille'
RETURN p.name

MATCH (p:Person)
WHERE toUpper(p.name) ENDS WITH 'DEMILLE'
RETURN p.name

MATCH (p:Person)
WHERE toUpper(p.name) CONTAINS ' DE '
RETURN p.name

EXPLAIN MATCH (m:Movie)
WHERE  m.title STARTS WITH 'Toy Story'
RETURN m.title, m.released
This query produces the execution plan where the first step is NodeIndexSeekByRange. In this case an index will be used because it is defined in the graph.

EXPLAIN MATCH (p:Person)
WHERE toLower(p.name) ENDS WITH 'demille'
RETURN p.name
But with this query the index will not be used, even if present in the graph, because toLower() is used

