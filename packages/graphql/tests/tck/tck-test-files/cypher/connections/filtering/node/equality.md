# Cypher -> Connections -> Filtering -> Node -> Equality

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

---

## Equality

### GraphQL Input

```graphql
query {
    movies {
        title
        actorsConnection(where: { node: { name: "Tom Hanks" } }) {
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
    WHERE this_actor.name = $this_actorsConnection.args.where.node.name
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
                    "name": "Tom Hanks"
                }
            }
        }
    }
}
```

---

## Inequality

### GraphQL Input

```graphql
query {
    movies {
        title
        actorsConnection(where: { node: { name_NOT: "Tom Hanks" } }) {
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
    WHERE (NOT this_actor.name = $this_actorsConnection.args.where.node.name_NOT)
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
                    "name_NOT": "Tom Hanks"
                }
            }
        }
    }
}
```

---
