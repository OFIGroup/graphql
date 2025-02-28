# Cypher Create

Tests create operations.

Schema:

```graphql
type Actor {
    name: String
    movies: [Movie] @relationship(type: "ACTED_IN", direction: OUT)
}

type Movie {
    id: ID
    actors: [Actor]! @relationship(type: "ACTED_IN", direction: IN)
}
```

---

## Simple Create

### GraphQL Input

```graphql
mutation {
    createMovies(input: [{ id: "1" }]) {
        movies {
            id
        }
    }
}
```

### Expected Cypher Output

```cypher
CALL {
  CREATE (this0:Movie)
  SET this0.id = $this0_id
  RETURN this0
}

RETURN this0 { .id } AS this0
```

### Expected Cypher Params

```json
{
    "this0_id": "1"
}
```

---

## Simple Multi Create

### GraphQL Input

```graphql
mutation {
    createMovies(input: [{ id: "1" }, { id: "2" }]) {
        movies {
            id
        }
    }
}
```

### Expected Cypher Output

```cypher
CALL {
  CREATE (this0:Movie)
  SET this0.id = $this0_id
  RETURN this0
}

CALL {
  CREATE (this1:Movie)
  SET this1.id = $this1_id
  RETURN this1
}

RETURN this0 { .id } AS this0,
       this1 { .id } AS this1
```

### Expected Cypher Params

```json
{
    "this0_id": "1",
    "this1_id": "2"
}
```

---

## Two Level Nested create

### GraphQL Input

```graphql
mutation {
    createMovies(
        input: [
            { id: 1, actors: { create: [{ node: { name: "actor 1" } }] } }
            { id: 2, actors: { create: [{ node: { name: "actor 2" } }] } }
        ]
    ) {
        movies {
            id
        }
    }
}
```

### Expected Cypher Output

```cypher
CALL {
  CREATE (this0:Movie)
  SET this0.id = $this0_id

    WITH this0
    CREATE (this0_actors0_node:Actor)
    SET this0_actors0_node.name = $this0_actors0_node_name
    MERGE (this0)<-[:ACTED_IN]-(this0_actors0_node)

  RETURN this0
}

CALL {
  CREATE (this1:Movie)
  SET this1.id = $this1_id

    WITH this1
    CREATE (this1_actors0_node:Actor)
    SET this1_actors0_node.name = $this1_actors0_node_name
    MERGE (this1)<-[:ACTED_IN]-(this1_actors0_node)

  RETURN this1
}

RETURN this0 { .id } AS this0, this1 { .id } AS this1
```

### Expected Cypher Params

```json
{
    "this0_id": "1",
    "this0_actors0_node_name": "actor 1",
    "this1_id": "2",
    "this1_actors0_node_name": "actor 2"
}
```

---

## Three Level Nested create

### GraphQL Input

```graphql
mutation {
    createMovies(
        input: [
            {
                id: "1"
                actors: {
                    create: [
                        {
                            node: {
                                name: "actor 1"
                                movies: { create: [{ node: { id: "10" } }] }
                            }
                        }
                    ]
                }
            }
            {
                id: "2"
                actors: {
                    create: [
                        {
                            node: {
                                name: "actor 2"
                                movies: { create: [{ node: { id: "20" } }] }
                            }
                        }
                    ]
                }
            }
        ]
    ) {
        movies {
            id
        }
    }
}
```

### Expected Cypher Output

```cypher
CALL {
  CREATE (this0:Movie)
  SET this0.id = $this0_id

    WITH this0
    CREATE (this0_actors0_node:Actor)
    SET this0_actors0_node.name = $this0_actors0_node_name
      WITH this0, this0_actors0_node
      CREATE (this0_actors0_node_movies0_node:Movie)
      SET this0_actors0_node_movies0_node.id = $this0_actors0_node_movies0_node_id
      MERGE (this0_actors0_node)-[:ACTED_IN]->(this0_actors0_node_movies0_node)
      MERGE (this0)<-[:ACTED_IN]-(this0_actors0_node)

  RETURN this0
}

CALL {
  CREATE (this1:Movie)
  SET this1.id = $this1_id

    WITH this1
    CREATE (this1_actors0_node:Actor)
    SET this1_actors0_node.name = $this1_actors0_node_name
      WITH this1, this1_actors0_node
      CREATE (this1_actors0_node_movies0_node:Movie)
      SET this1_actors0_node_movies0_node.id = $this1_actors0_node_movies0_node_id
      MERGE (this1_actors0_node)-[:ACTED_IN]->(this1_actors0_node_movies0_node)
      MERGE (this1)<-[:ACTED_IN]-(this1_actors0_node)

  RETURN this1
}

RETURN this0 { .id } AS this0, this1 { .id } AS this1
```

### Expected Cypher Params

```json
{
    "this0_id": "1",
    "this0_actors0_node_name": "actor 1",
    "this0_actors0_node_movies0_node_id": "10",
    "this1_id": "2",
    "this1_actors0_node_name": "actor 2",
    "this1_actors0_node_movies0_node_id": "20"
}
```

---

## Simple create and connect

### GraphQL Input

```graphql
mutation {
    createMovies(
        input: [
            {
                id: 1
                actors: { connect: [{ where: { node: { name: "Dan" } } }] }
            }
        ]
    ) {
        movies {
            id
        }
    }
}
```

### Expected Cypher Output

```cypher
CALL {
    CREATE (this0:Movie)
    SET this0.id = $this0_id

    WITH this0
    CALL {
        WITH this0
        OPTIONAL MATCH (this0_actors_connect0_node:Actor)
        WHERE this0_actors_connect0_node.name = $this0_actors_connect0_node_name
        FOREACH(_ IN CASE this0_actors_connect0_node WHEN NULL THEN [] ELSE [1] END |
        MERGE (this0)<-[:ACTED_IN]-(this0_actors_connect0_node)
        )
        RETURN count(*)
    }

    RETURN this0
}

RETURN this0 { .id } AS this0
```

### Expected Cypher Params

```json
{
    "this0_id": "1",
    "this0_actors_connect0_node_name": "Dan"
}
```

---

## Simple create -> relationship field -> connection(where)

### GraphQL Input

```graphql
mutation {
    createActors(
        input: {
            name: "Dan"
            movies: { connect: { where: { node: { id: 1 } } } }
        }
    ) {
        actors {
            name
            movies {
                actorsConnection(where: { node: { name: "Dan" } }) {
                    totalCount
                    edges {
                        node {
                            name
                        }
                    }
                }
            }
        }
    }
}
```

### Expected Cypher Output

```cypher
CALL {
    CREATE (this0:Actor)
    SET this0.name = $this0_name
    WITH this0
    CALL {
        WITH this0
        OPTIONAL MATCH (this0_movies_connect0_node:Movie)
        WHERE this0_movies_connect0_node.id = $this0_movies_connect0_node_id
        FOREACH(_ IN CASE this0_movies_connect0_node WHEN NULL THEN [] ELSE [1] END |
        MERGE (this0)-[:ACTED_IN]->(this0_movies_connect0_node)
        )
        RETURN count(*)
    }
    RETURN this0
}

RETURN this0 { .name, movies: [ (this0)-[:ACTED_IN]->(this0_movies:Movie) | this0_movies { actorsConnection: apoc.cypher.runFirstColumn("CALL {
    WITH this0_movies
    MATCH (this0_movies)<-[this0_movies_acted_in_relationship:ACTED_IN]-(this0_movies_actor:Actor)
    WHERE this0_movies_actor.name = $projection_movies_actorsConnection.args.where.node.name
    WITH collect({ node: { name: this0_movies_actor.name } }) AS edges
    RETURN { edges: edges, totalCount: size(edges) } AS actorsConnection
} RETURN actorsConnection", { this0_movies: this0_movies, projection_movies_actorsConnection: $projection_movies_actorsConnection }, false) } ] } AS this0
```

### Expected Cypher Params

```json
{
    "this0_name": "Dan",
    "this0_movies_connect0_node_id": "1",
    "projection_movies_actorsConnection": {
        "args": {
            "where": {
                "node": {
                    "name": "Dan"
                }
            }
        }
    }
}
```

---
