#

## Task 1

### SELECT queries – please implement one of these queries:

#### Q1: Return all films

```sql
PREFIX : <http://semantics.id/ns/example/film#>
SELECT ?film WHERE {
    ?film a :Film .
}
```

![Q1](/images/Q1.png)

#### Q2: Return all films with their title

### ASK queries – please implement one of these queries:

#### Q3: Is there a film named “Dune”?

```sql
PREFIX : <http://semantics.id/ns/example/film#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
ASK WHERE {
    ?film a :Film ;
          rdfs:label "Dune" .
}
```

![Q3](/images/Q3.png)

#### Q4: Is there a film named “Dune” released before 1984?

### DESCRIBE queries – please implement one of these queries:

#### Q5: Give me all information about “Dune” movie released in 1984

```sql
PREFIX : <http://semantics.id/ns/example/film#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
DESCRIBE ?film WHERE {
    ?film a :Film ;
          rdfs:label "Dune" ;
          :releaseYear "1984"^^xsd:integer .
}
```

![Q5](/images/Q5.png)

#### Q6: Give me all information about actors which are playing in “Dune” released in 1984

### SPARQL CONSTRUCT queries – please implement one of these queries and additionally build a query of your own with code Q9

#### Q7: Return all writers and the film studios for which they have worked

```sql
PREFIX ex: <http://semantics.id/ns/example/film#>

CONSTRUCT {
    ?writer ex:hasWorkedFor ?studio .
} WHERE {
    ?film ex:hasScriptWriter ?writer .
    ?film ex:hasFilmStudio ?studio .
}
```

![Q7](/images/Q7.png)

#### Q8: Return all actors and directors who know each other from movies

#### Q9

```sql
PREFIX ex: <http://semantics.id/ns/example/film#>

CONSTRUCT {
    ?actor ex:playedInGenre ?genre .
} WHERE {
    ?film ex:hasActor ?actor ;
          ex:hasGenre ?genre .
}
```

![Q9](/images/Q9.png)

### FILTER queries – please implement one of these queries and additionally build a query of your own with code Q13

#### Q10: Select film studios that were established after 1960

```sql
PREFIX ex: <http://semantics.id/ns/example/film#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>

SELECT ?studio ?date WHERE {
    ?studio a ex:FilmStudio ;
            ex:establishedDate ?date .
    FILTER (?date >= "1960-12-31"^^xsd:date)
}
```

#### Q11: Select unique name of actors who play in movies between 2014 and 2020

#### Q12: Select name of actors who play in movies released after 2016 and with title containing string “Beauty”

### Q13

```sql
PREFIX ex: <http://semantics.id/ns/example/film#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>

SELECT ?actorName ?birthdate WHERE {
    ?film ex:hasActor ?actor ;
          ex:hasGenre ex:genre_family .
    ?actor ex:fullName ?actorName ;
           ex:dateOfBirth ?birthdate .
    FILTER (?birthdate > "1980-01-01"^^xsd:date)
}
```

![Q13](/images/Q13.png)

### ORDER and GROUP queries – please implement one of these queries and additionally build a query of your own with code Q16

#### Q14: Select name of actors who plays in movies ordered by birthdate (ascending)

```sql
PREFIX ex: <http://semantics.id/ns/example/film#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

SELECT DISTINCT ?actorName ?actor ?birthdate WHERE {
    ?film ex:hasActor ?actor .
    ?actor a ex:Actor ;
           ex:dateOfBirth ?birthdate ;
           ex:fullName ?actorName .
}
ORDER BY ?birthdate
```

![Q14](/images/Q14.png)

#### Q15: Count the average number of ScriptWriters involved in each movie!

#### Q16

```sql
PREFIX ex: <http://semantics.id/ns/example/film#>

SELECT ?actor (COUNT(?film) AS ?numberOfFilms) WHERE {
    ?film ex:hasActor ?actor .
} GROUP BY ?actor
ORDER BY DESC(?numberOfFilms)
```

![Q16](/images/Q16.png)

### UNION queries – please implement this query without utilizing inference mechanisms:

#### Q17: List all actors and crews together with the title of movies that they are involved in, ordered by their name.

```sql
PREFIX ex: <http://semantics.id/ns/example/film#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

SELECT ?personName ?movieTitle WHERE {
    {
        ?film ex:hasActor ?person .
        ?person ex:fullName ?personName .
    }
    UNION
    {
        ?film ex:hasCrew ?person .
        ?person ex:fullName ?personName .
    }
    ?film rdfs:label ?movieTitle .
}
ORDER BY ?personName
```

![Q17](/images/Q17.png)

## Task 2

### dpedia Querries

http://dbpedia.org/sparql

#### Q18: How many South Korean movies are listed in DBPedia?

```sql
PREFIX dbo: <http://dbpedia.org/ontology/>

SELECT (COUNT(?movie) AS ?numberOfMovies) WHERE {
    ?movie a dbo:Film ;
           dbo:country dbr:South_Korea .
}
```

![Q18](/images/Q18.png)

#### Q19: Find all movies released after year 2000.

```sql
PREFIX dbo: <http://dbpedia.org/ontology/>
PREFIX dbr: <http://dbpedia.org/resource/>

SELECT ?movie ?date
WHERE {
  ?movie a dbo:Film ;
         dbo:releaseDate ?date .
  FILTER (YEAR(?date) > 2000)
}
ORDER BY ?date
-- ORDER BY DESC(?date)
LIMIT 10
```

![Q19](/images/Q19_asc.png)
![Q19](/images/Q19_desc.png)

#### Q20: Find all movies directed by Steven Spielberg where Tom Hanks is not playing

```sql
PREFIX dbo: <http://dbpedia.org/ontology/>
PREFIX dbr: <http://dbpedia.org/resource/>

SELECT ?movie WHERE {
    ?movie a dbo:Film ;
           dbo:director dbr:Steven_Spielberg .
    MINUS { ?movie dbo:starring dbr:Tom_Hanks }
}
```

![Q20](/images/Q20.png)

### Additional Querries

Additionally, please propose and implement 2 complex queries of your own including (any combination of) FILTER, ORDER, GROUP, UNION. Name these Q21 and Q22

#### Q21

```sql
PREFIX dbo: <http://dbpedia.org/ontology/>

SELECT ?era (COUNT(?philosopher) AS ?numberOfPhilosophers) WHERE {
    ?philosopher a dbo:Philosopher ;
                 dbo:era ?era .
} GROUP BY ?era
HAVING (COUNT(?philosopher) > 1)
ORDER BY DESC(?numberOfPhilosophers)
```

![Q21](/images/Q21.png)

#### Q22

```sql
PREFIX dbo: <http://dbpedia.org/ontology/>
PREFIX dbp: <http://dbpedia.org/property/>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>

SELECT ?missionType ?missionCategory (YEAR(SAMPLE(?missionDate)) AS ?missionYear) (COUNT(DISTINCT ?astronautNationality) AS ?numberOfCountriesOrAgencies) ?mission WHERE {
    {
        ?astronaut a dbo:Astronaut .
        ?mission a dbo:SpaceMission ;
                 dbp:missionType ?missionType ;
                 dbo:launchDate ?missionDate .
        ?astronaut dbo:mission ?mission .
        OPTIONAL { ?astronaut dbo:nationality ?nation . }
        OPTIONAL { ?astronaut dbo:employer ?agency . }
        BIND(COALESCE(?nation, ?agency) AS ?astronautNationality)
        FILTER (YEAR(?missionDate) > 2000)
        BIND("Crewed" AS ?missionCategory)
    }
    UNION
    {
        ?mission a dbo:SpaceMission ;
                 dbp:missionType ?missionType ;
                 dbo:launchDate ?missionDate .
        FILTER NOT EXISTS { ?mission dbo:crew ?crew }
        FILTER (YEAR(?missionDate) > 2000)
        BIND("Uncrewed" AS ?missionCategory)
    }
}
GROUP BY ?missionType ?mission ?missionCategory
HAVING (COUNT(DISTINCT ?astronautNationality) >= 0)
ORDER BY DESC(?missionYear)
```

![Q22](/images/Q22.png)

## Task 3

### Q23: RDFS Domain Entailment

```sql
PREFIX ex: <http://semantics.id/ns/example/film#>

SELECT ?subject WHERE {
    ?subject ex:hasPerformer ?crew .
}
```

![Q23](/images/Q23_NI.png)
![Q23](/images/Q23_I.png)

### Q24: RDFS Sub-Property Entailment

```sql

```

### Q25: OWL Class Hierarchy Entailment

```sql
PREFIX ex: <http://semantics.id/ns/example/film#>

SELECT ?instance WHERE {
    ?instance a ex:Performer .
}
```

![Q25](/images/Q25_NI.png)
![Q25](/images/Q25_I.png)
