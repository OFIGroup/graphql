# Cypher -> Connections -> Filtering -> Node -> String

Schema:

```graphql
type Movie {
    title: String!
    actors: [Actor!]!
        @relationship(type: "ACTED_IN", properties: "ActedIn", direction: IN)
}

type Actor {
    name: String!
    movies: [Movie!]!
        @relationship(type: "ACTED_IN", properties: "ActedIn", direction: OUT)
}

interface ActedIn {
    screenTime: Int!
}
```

```env
NEO4J_GRAPHQL_ENABLE_REGEX=1
```

---

## CONTAINS

### GraphQL Input

```graphql
query {
    movies {
        title
        actorsConnection(where: { node: { name_CONTAINS: "Tom" } }) {
            edges {
                screenTime
                node {
                    name
                }
            }
        }
    }
}
```

### Expected Cypher Output

```cypher
MATCH (this:Movie)
CALL {
    WITH this
    MATCH (this)<-[this_acted_in_relationship:ACTED_IN]-(this_actor:Actor)
    WHERE this_actor.name CONTAINS $this_actorsConnection.args.where.node.name_CONTAINS
    WITH collect({ screenTime: this_acted_in_relationship.screenTime, node: { name: this_actor.name } }) AS edges
    RETURN { edges: edges, totalCount: size(edges) } AS actorsConnection
}
RETURN this { .title, actorsConnection } as this
```

### Expected Cypher Params

```json
{
    "this_actorsConnection": {
        "args": {
            "where": {
                "node": {
                    "name_CONTAINS": "Tom"
                }
            }
        }
    }
}
```

---

## NOT_CONTAINS

### GraphQL Input

```graphql
query {
    movies {
        title
        actorsConnection(where: { node: { name_NOT_CONTAINS: "Tom" } }) {
            edges {
                screenTime
                node {
                    name
                }
            }
        }
    }
}
```

### Expected Cypher Output

```cypher
MATCH (this:Movie)
CALL {
    WITH this
    MATCH (this)<-[this_acted_in_relationship:ACTED_IN]-(this_actor:Actor)
    WHERE (NOT this_actor.name CONTAINS $this_actorsConnection.args.where.node.name_NOT_CONTAINS)
    WITH collect({ screenTime: this_acted_in_relationship.screenTime, node: { name: this_actor.name } }) AS edges
    RETURN { edges: edges, totalCount: size(edges) } AS actorsConnection
}
RETURN this { .title, actorsConnection } as this
```

### Expected Cypher Params

```json
{
    "this_actorsConnection": {
        "args": {
            "where": {
                "node": {
                    "name_NOT_CONTAINS": "Tom"
                }
            }
        }
    }
}
```

---

## STARTS_WITH

### GraphQL Input

```graphql
query {
    movies {
        title
        actorsConnection(where: { node: { name_STARTS_WITH: "Tom" } }) {
            edges {
                screenTime
                node {
                    name
                }
            }
        }
    }
}
```

### Expected Cypher Output

```cypher
MATCH (this:Movie)
CALL {
    WITH this
    MATCH (this)<-[this_acted_in_relationship:ACTED_IN]-(this_actor:Actor)
    WHERE this_actor.name STARTS WITH $this_actorsConnection.args.where.node.name_STARTS_WITH
    WITH collect({ screenTime: this_acted_in_relationship.screenTime, node: { name: this_actor.name } }) AS edges
    RETURN { edges: edges, totalCount: size(edges) } AS actorsConnection
}
RETURN this { .title, actorsConnection } as this
```

### Expected Cypher Params

```json
{
    "this_actorsConnection": {
        "args": {
            "where": {
                "node": {
                    "name_STARTS_WITH": "Tom"
                }
            }
        }
    }
}
```

---

## NOT_STARTS_WITH

### GraphQL Input

```graphql
query {
    movies {
        title
        actorsConnection(where: { node: { name_NOT_STARTS_WITH: "Tom" } }) {
            edges {
                screenTime
                node {
                    name
                }
            }
        }
    }
}
```

### Expected Cypher Output

```cypher
MATCH (this:Movie)
CALL {
    WITH this
    MATCH (this)<-[this_acted_in_relationship:ACTED_IN]-(this_actor:Actor)
    WHERE (NOT this_actor.name STARTS WITH $this_actorsConnection.args.where.node.name_NOT_STARTS_WITH)
    WITH collect({ screenTime: this_acted_in_relationship.screenTime, node: { name: this_actor.name } }) AS edges
    RETURN { edges: edges, totalCount: size(edges) } AS actorsConnection
}
RETURN this { .title, actorsConnection } as this
```

### Expected Cypher Params

```json
{
    "this_actorsConnection": {
        "args": {
            "where": {
                "node": {
                    "name_NOT_STARTS_WITH": "Tom"
                }
            }
        }
    }
}
```

---

## ENDS_WITH

### GraphQL Input

```graphql
query {
    movies {
        title
        actorsConnection(where: { node: { name_ENDS_WITH: "Hanks" } }) {
            edges {
                screenTime
                node {
                    name
                }
            }
        }
    }
}
```

### Expected Cypher Output

```cypher
MATCH (this:Movie)
CALL {
    WITH this
    MATCH (this)<-[this_acted_in_relationship:ACTED_IN]-(this_actor:Actor)
    WHERE this_actor.name ENDS WITH $this_actorsConnection.args.where.node.name_ENDS_WITH
    WITH collect({ screenTime: this_acted_in_relationship.screenTime, node: { name: this_actor.name } }) AS edges
    RETURN { edges: edges, totalCount: size(edges) } AS actorsConnection
}
RETURN this { .title, actorsConnection } as this
```

### Expected Cypher Params

```json
{
    "this_actorsConnection": {
        "args": {
            "where": {
                "node": {
                    "name_ENDS_WITH": "Hanks"
                }
            }
        }
    }
}
```

---

## NOT_ENDS_WITH

### GraphQL Input

```graphql
query {
    movies {
        title
        actorsConnection(where: { node: { name_NOT_ENDS_WITH: "Hanks" } }) {
            edges {
                screenTime
                node {
                    name
                }
            }
        }
    }
}
```

### Expected Cypher Output

```cypher
MATCH (this:Movie)
CALL {
    WITH this
    MATCH (this)<-[this_acted_in_relationship:ACTED_IN]-(this_actor:Actor)
    WHERE (NOT this_actor.name ENDS WITH $this_actorsConnection.args.where.node.name_NOT_ENDS_WITH)
    WITH collect({ screenTime: this_acted_in_relationship.screenTime, node: { name: this_actor.name } }) AS edges
    RETURN { edges: edges, totalCount: size(edges) } AS actorsConnection
}
RETURN this { .title, actorsConnection } as this
```

### Expected Cypher Params

```json
{
    "this_actorsConnection": {
        "args": {
            "where": {
                "node": {
                    "name_NOT_ENDS_WITH": "Hanks"
                }
            }
        }
    }
}
```

---

## MATCHES

### GraphQL Input

```graphql
query {
    movies {
        title
        actorsConnection(where: { node: { name_MATCHES: "Tom.+" } }) {
            edges {
                screenTime
                node {
                    name
                }
            }
        }
    }
}
```

### Expected Cypher Output

```cypher
MATCH (this:Movie)
CALL {
    WITH this
    MATCH (this)<-[this_acted_in_relationship:ACTED_IN]-(this_actor:Actor)
    WHERE this_actor.name =~ $this_actorsConnection.args.where.node.name_MATCHES
    WITH collect({ screenTime: this_acted_in_relationship.screenTime, node: { name: this_actor.name } }) AS edges
    RETURN { edges: edges, totalCount: size(edges) } AS actorsConnection
}
RETURN this { .title, actorsConnection } as this
```

### Expected Cypher Params

```json
{
    "this_actorsConnection": {
        "args": {
            "where": {
                "node": {
                    "name_MATCHES": "Tom.+"
                }
            }
        }
    }
}
```

---
