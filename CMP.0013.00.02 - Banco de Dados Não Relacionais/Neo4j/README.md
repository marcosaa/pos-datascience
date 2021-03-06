# Neo4j
FURB - Pós Data Science - Banco de Dados não Relacional - Neo4j

## Configurações

Subindo container do Neo4j: `docker run --name neo4j-furb --publish=7474:7474 --publish=7687:7687 --volume=$HOME/neo4j/data:/data --env=NEO4J_AUTH=none neo4j:4.0`.

Acessar o Neo4j via browser: `http://localhost:7474`.

Na tela inicial do Neo4j, selecionar a opção "No authentication" e clicar em Connect.

No editor, executar o comando `:play movie-graph`, navegar para a seguinda página e executar o comando CREATE para criar o modelo de dados.

## Exercício 1 - Retrieving Nodes

### 1. Retrieve all nodes from the database.
`MATCH (n) RETURN n`

### 2. Examine the data model for the graph.
`CALL db.schema.visualization()`

### 3. Retrieve all Person nodes.
`MATCH (p:Person) RETURN p`

### 4. Retrieve all Movie nodes.
`MATCH (m:Movie) RETURN m`

### Evidência
![Comandos Exercício 1](print_comandos_exercicio_1.png)

## Exercício 2 - Filtering queries using property values

### 1. Retrieve all movies that were released in a specific year.
`MATCH (m:Movie {released: 2006}) RETURN m`

### 2. View the retrieved results as a table.
No painel de resultados, clicar no icone de tabela.

### 3. Query the database for all property keys.
`CALL db.propertyKeys`

### 4. Retrieve all Movies released in a specific year, returning their titles.
`MATCH (m:Movie {released: 2003}) RETURN m.title`

### 5. Display title, released, and tagline values for every Movie node in the graph.
`MATCH (m:Movie) RETURN m.title, m.released, m.tagline`

### 6. Display more user-friendly headers in the table.
```
MATCH (m:Movie) RETURN m.title AS `Title`, m.released AS `Released`, m.tagline AS `TagLine`
```

### Evidência
![Comandos Exercício 2](print_comandos_exercicio_2.png)

## Exercício 3 - Filtering queries using relationships

### 1. Display the schema of the database.
`CALL db.schema.visualization()`

### 2. Retrieve all people who wrote the movie Speed Racer.
`MATCH (p:Person)-[:WROTE]->(:Movie {title: 'Speed Racer'}) RETURN p`

### 3. Retrieve all movies that are connected to the person, Tom Hanks.
`MATCH (:Person {name: 'Tom Hanks'})--(m:Movie) RETURN m.title`

### 4. Retrieve information about the relationships Tom Hanks had with the set of movies retrieved earlier.
`MATCH (:Person {name: 'Tom Hanks'})-[rel]-(m:Movie) RETURN m.title, type(rel)`

### 5. Retrieve information about the roles that Tom Hanks acted in.
`MATCH (:Person {name: 'Tom Hanks'})-[rel:ACTED_IN]-(m:Movie) RETURN m.title, rel.roles`

### Evidência
![Comandos Exercício 3](print_comandos_exercicio_3.png)

## Exercício 4 - Filtering queries using WHERE clause

### 1. Retrieve all movies that Tom Cruise acted in.
```
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE p.name = 'Tom Cruise'
RETURN m.title
```

### 2. Retrieve all people that were born in the 70’s.
```
MATCH (p:Person)
WHERE p.born >= 1970 AND p.born <= 1979
RETURN p.name
```

### 3. Retrieve the actors who acted in the movie The Matrix who were born after 1960.
```
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE m.title = 'The Matrix' AND p.born > 1960
RETURN p.name
```

### 4. Retrieve all movies by testing the node label and a property.
```
MATCH (m)
WHERE m:Movie AND m.released = 2003
RETURN m.title
```

### 5. Retrieve all people that wrote movies by testing the relationship between two nodes.
```
MATCH (p)-[r]->(m)
WHERE p:Person AND type(r) = 'WROTE' AND m:Movie
RETURN p.name
```

### 6. Retrieve all people in the graph that do not have a property.
```
MATCH (p:Person)
WHERE NOT exists(p.born)
RETURN p.name
```

### 7. Retrieve all people related to movies where the relationship has a property.
```
MATCH (p:Person)-[r]->(m:Movie)
WHERE exists(r.rating)
RETURN p.name, m.title, r.rating
```

### 8. Retrieve all actors whose name begins with James.
```
MATCH (p:Person)-[r]->(:Movie)
WHERE type(r) = 'ACTED_IN' AND p.name STARTS WITH 'James'
RETURN p.name
```

### 9. Retrieve all REVIEW relationships from the graph with filtered results.
```
MATCH (:Person)-[r]->(m:Movie)
WHERE type(r) = 'REVIEWED' AND toLower(r.summary) CONTAINS 'fun'
RETURN m.title, r.summary
```

### 10. Retrieve all people who have produced a movie, but have not directed a movie.
```
MATCH (p:Person)-[:PRODUCED]->(m:Movie)
WHERE NOT ((p)-[:DIRECTED]->(:Movie))
RETURN p.name, m.title
```

### 11. Retrieve the movies and their actors where one of the actors also directed the movie.
```
MATCH (p1:Person)-[:ACTED_IN]->(m:Movie)<-[:ACTED_IN]-(p2:Person)
WHERE exists((p2)-[:DIRECTED]->(m))
RETURN m.title, p1.name AS `Actor`, p2.name AS `Director`
```

### 12. Retrieve all movies that were released in a set of years.
```
MATCH (m:Movie)
WHERE m.released in [2000, 2004, 2008]
RETURN m.title, m.released
```

### 13. Retrieve the movies that have an actor’s role that is the name of the movie.
```
MATCH (p:Person)-[r:ACTED_IN]->(m:Movie)
WHERE m.title in r.roles
RETURN m.title, p.name
```

### Evidência
![Comandos Exercício 4](print_comandos_exercicio_4.png)

## Exercício 5 - Controlling query processing

### 1. Retrieve data using multiple MATCH patterns.
```
MATCH (g:Person)-[:ACTED_IN]->(m:Movie)<-[:DIRECTED]-(d:Person), (a:Person)-[:ACTED_IN]->(m)
WHERE g.name = 'Gene Hackman'
RETURN m.title as movie, d.name AS Director , a.name AS `Actors`
```

### 2. Retrieve particular nodes that have a relationship.
```
MATCH (jt:Person)-[:FOLLOWS]-(p:Person)
WHERE jt.name = 'James Thompson'
RETURN jt, p
```

### 3. Modify the query to retrieve nodes that are exactly three hops away.
```
MATCH (jt:Person)-[:FOLLOWS*3]-(p:Person)
WHERE jt.name = 'James Thompson'
RETURN jt, p
```

### 4. Modify the query to retrieve nodes that are one and two hops away.
```
MATCH (jt:Person)-[:FOLLOWS*1..2]-(p:Person)
WHERE jt.name = 'James Thompson'
RETURN jt, p
```

### 5. Modify the query to retrieve particular nodes that areconnected no matter how many hops are required.
```
MATCH (jt:Person)-[:FOLLOWS*]-(p:Person)
WHERE jt.name = 'James Thompson'
RETURN jt, p
```

### 6. Specify optional data to be retrieved during the query.
```
MATCH (t:Person)
WHERE t.name STARTS WITH 'Tom'
OPTIONAL MATCH (t)-[r:DIRECTED]->(m:Movie)
RETURN t.name, m.title
```

### 7. Retrieve nodes by collecting a list.
```
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
RETURN a.name as actor, collect(m.title) as movies
```

### 8. Retrieve all movies that Tom Cruise has acted in and the co-actors that acted in the same movie by collecting a list
```
MATCH (t:Person)-[:ACTED_IN]->(m:Movie)<-[:ACTED_IN]-(a:Person)
WHERE t.name = 'Tom Cruise'
RETURN m.title as movie, collect(a.name) as co_autors
```

### 9. Retrieve nodes as lists and return data associated with the corresponding lists.
```
MATCH (p:Person)-[:REVIEWED]->(m:Movie)
RETURN m.title AS movie, count(p) AS total_reviewers, collect(p.name) AS reviewers
```

### 10. Retrieve nodes and their relationships as lists.
```
MATCH (d:Person)-[:DIRECTED]->(:Movie)<-[:ACTED_IN]-(a:Person)
RETURN d.name AS director, count(a) as total_actors, collect(a.name) as actors
```

### 11. Retrieve the actors who have acted in exactly five movies.
```
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
WITH a, count(a) as total_movies, collect(m.title) as movies
WHERE total_movies = 5
RETURN a.name as actor, movies
```

### 12. Retrieve the movies that have at least 2 directors with other optional data.
```
MATCH (m:Movie)
WITH m, size((:Person)-[:DIRECTED]->(m)) as total_directors
WHERE total_directors >= 2
OPTIONAL MATCH (r:Person)-[:REVIEWED]->(m)
RETURN m.title as movie, r.name as reviewers
```

### Evidência
![Comandos Exercício 5](print_comandos_exercicio_5.png)

## Exercício 6 - Controlling results returned

### 1. Execute a query that returns duplicate records.
```
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE m.released >= 1990 AND m.released <= 1999
RETURN m.released, m.title, collect(p.name)
```

### 2. Modify the query to eliminate duplication.
```
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE m.released >= 1990 AND m.released <= 1999
RETURN m.released, collect(m.title), collect(p.name)
```

### 3. Modify the query to eliminate more duplication.
```
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE m.released >= 1990 AND m.released <= 1999
RETURN m.released, collect(DISTINCT m.title), collect(p.name)
```

### 4. Sort results returned.
```
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE m.released >= 1990 AND m.released <= 1999
RETURN m.released, collect(DISTINCT m.title), collect(p.name)
ORDER BY m.released DESC
```

### 5. Retrieve the top 5 ratings and their associated movies.
```
MATCH (:Person)-[r:REVIEWED]->(m:Movie)
RETURN m.title as movie, r.rating as rating
ORDER BY r.rating DESC
LIMIT 5
```

### 6. Retrieve all actors that have not appeared in more than 3 movies.
```
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WITH p, count(p) as total_movies, collect(m.title) as movies
WHERE total_movies <= 3
RETURN p.name as actor, movies
```

### Evidência
![Comandos Exercício 6](print_comandos_exercicio_6.png)

## Exercício 7 - Working with cypher data

### 1. Collect and use lists.
```
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)<-[:PRODUCED]-(p:Person)
WITH m, collect(DISTINCT a.name) as actors, collect(DISTINCT p.name) as producers
RETURN DISTINCT m.title as movie, actors, producers
ORDER BY size(actors)
```

### 2. Collect a list.
```
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
WITH a, collect(m.title) as movies
WHERE size(movies) > 5
RETURN a.name as actor, movies
```

### 3. Unwind a list.
```
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
WITH a, collect(m) as movies
WHERE size(movies) > 5
WITH a, movies UNWIND movies AS movie
RETURN a.name as actor, movie.title
```

### 4. Perform a calculation with the date type.
```
MATCH (a:Person)-[r:ACTED_IN]->(m:Movie)
WHERE a.name = 'Tom Hanks'
RETURN m.title as movie, m.released as year_released, date().year - m.released as years_ago_released, m.released - a.born as age_of_tom
```

### Evidência
![Comandos Exercício 7](print_comandos_exercicio_7.png)

## Exercício 8 - Creating nodes

### 1. Create a Movie node.
`CREATE (:Movie {title: 'Forrest Gump'})`

### 2. Retrieve the newly-created node.
`MATCH (m:Movie {title: 'Forrest Gump'}) RETURN m`

### 3. Create a Person node.
`CREATE (:Person {name: 'Robin Wright'})`

### 4. Retrieve the newly-created node.
`MATCH (p:Person {name: 'Robin Wright'}) RETURN p`

### 5. Add a label to a node.
```
MATCH (m:Movie)
WHERE m.released < 2010
SET m:OlderMovie
RETURN labels(m)
```

### 6. Retrieve the node using the new label.
```
MATCH (m:OlderMovie)
RETURN m.title as title, m.released as released
```

### 7. Add the Female label to selected nodes.
```
MATCH (p:Person)
WHERE p.name STARTS WITH 'Robin'
SET p:Female
```

### 8. Retrieve all Female nodes.
```
MATCH (p:Female)
RETURN p.name as name
```

### 9. Remove the Female label from the nodes that have this label.
```
MATCH (p:Female)
REMOVE p:Female
```

### 10. View the current schema of the graph.
`CALL db.schema.visualization`

### 11. Add properties to a movie.
```
MATCH (m:Movie)
WHERE m.title = 'Forrest Gump'
SET m:OlderMovie,
m.released = 1994,
m.tagline = 'Life is like a box of chocolates…​you never know what you’re gonna get.',
m.lengthInMinutes = 142
```

### 12. Retrieve an OlderMovie node to confirm the label and properties.
```
MATCH (m:OlderMovie)
WHERE m.title = 'Forrest Gump'
RETURN m
```

### 13. Add properties to the person, Robin Wright.
```
MATCH (p:Person)
WHERE p.name = 'Robin Wright'
SET p.born = 1966,
p.birthPlace = 'Dallas'
```

### 14. Retrieve an updated Person node.
```
MATCH (p:Person)
WHERE p.name = 'Robin Wright'
RETURN p
```

### 15. Remove a property from a Movie node.
```
Remove the lengthInMinutes property from the movie, Forrest Gump.
MATCH (m:Movie)
WHERE m.title = 'Forrest Gump'
SET m.lengthInMinutes = null
```

### 16. Retrieve the node to confirm that the property has been removed.
```
MATCH (m:Movie)
WHERE m.title = 'Forrest Gump'
RETURN m
```

### 17. Remove a property from a Person node.
```
MATCH (p:Person)
WHERE p.name = 'Robin Wright'
REMOVE p.birthPlace
```

### 18. Retrieve the node to confirm that the property has been removed.
```
MATCH (p:Person)
WHERE p.name = 'Robin Wright'
RETURN p
```

### Evidência
![Comandos Exercício 8](print_comandos_exercicio_8.png)

## Exercício 9 - Creating relationships

### 1. Create ACTED_IN relationships.
```
MATCH (m:Movie)
WHERE m.title = 'Forrest Gump'
MATCH (p:Person)
WHERE p.name = 'Tom Hanks' OR p.name = 'Robin Wright' OR p.name = 'Gary Sinise'
CREATE (p)-[:ACTED_IN]->(m)
```

### 2. Create DIRECTED relationships.
```
MATCH (m:Movie)
WHERE m.title = 'Forrest Gump'
MATCH (p:Person)
WHERE p.name = 'Robert Zemeckis'
CREATE (p)-[:DIRECTED]->(m)
```

### 3. Create a HELPED relationship.
```
MATCH (p1:Person)
WHERE p1.name = 'Tom Hanks'
MATCH (p2:Person)
WHERE p2.name = 'Gary Sinise'
CREATE (p1)-[:HELPED]->(p2)
```

### 4. Query nodes and new relationships.
```
MATCH (p:Person)-[rel]-(m:Movie)
WHERE m.title = 'Forrest Gump'
RETURN p, rel, m
```

### 5. Add properties to relationships.
```
MATCH (p:Person)-[rel:ACTED_IN]->(m:Movie)
WHERE m.title = 'Forrest Gump'
SET rel.roles =
CASE p.name
  WHEN 'Tom Hanks' THEN ['Forrest Gump']
  WHEN 'Robin Wright' THEN ['Jenny Curran']
  WHEN 'Gary Sinise' THEN ['Lieutenant Dan Taylor']
END
```

### 6. Add a property to the HELPED relationship.
```
MATCH (p1:Person)-[rel:HELPED]->(p2:Person)
WHERE p1.name = 'Tom Hanks' AND p2.name = 'Gary Sinise'
SET rel.research = 'war history'
```

### 7. View the current list of property keys in the graph.
`CALL db.propertyKeys`

### 8. View the current schema of the graph.
`CALL db.schema.visualization`

### 9. Retrieve the names and roles for actors.
```
MATCH (p:Person)-[rel:ACTED_IN]->(m:Movie)
WHERE m.title = 'Forrest Gump'
RETURN p.name, rel.roles
```

### 10. Retrieve information about any specific relationships.
```
MATCH (p1:Person)-[rel:HELPED]-(p2:Person)
RETURN p1.name, rel, p2.name
```

### 11. Modify a property of a relationship.
```
MATCH (p:Person)-[rel:ACTED_IN]->(m:Movie)
WHERE m.title = 'Forrest Gump' AND p.name = 'Gary Sinise'
SET rel.roles =['Lt. Dan Taylor']
```

### 12. Remove a property from a relationship.
```
MATCH (p1:Person)-[rel:HELPED]->(p2:Person)
WHERE p1.name = 'Tom Hanks' AND p2.name = 'Gary Sinise'
REMOVE rel.research
```

### 13. Confirm that your modifications were made to the graph.
```
MATCH (p:Person)-[rel:ACTED_IN]->(m:Movie)
WHERE m.title = 'Forrest Gump'
return p, rel, m
```

### Evidência
![Comandos Exercício 9](print_comandos_exercicio_9.png)

## Exercício 10 - Deleting nodes and relationships

### 1. Delete a relationship.
```
MATCH (:Person)-[rel:HELPED]-(:Person)
DELETE rel
```

### 2. Confirm that the relationship has been deleted.
```
MATCH (:Person)-[rel:HELPED]-(:Person)
RETURN rel
```

### 3. Retrieve a movie and all of its relationships.
```
MATCH (p:Person)-[rel]-(m:Movie)
WHERE m.title = 'Forrest Gump'
RETURN p, rel, m
```

### 4. Try deleting a node without detaching its relationships.
```
MATCH (m:Movie)
WHERE m.title = 'Forrest Gump'
DELETE m
```

### 5. Delete a Movie node, along with its relationships.
```
MATCH (m:Movie)
WHERE m.title = 'Forrest Gump'
DETACH DELETE m
```

### 6. Confirm that the Movie node has been deleted.
```
MATCH (p:Person)-[rel]-(m:Movie)
WHERE m.title = 'Forrest Gump'
RETURN p, rel, m
```

### Evidência
![Comandos Exercício 10](print_comandos_exercicio_10.png)