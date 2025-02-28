# Cypher -> Connections -> Filtering -> Relationship -> Points

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
    location: Point!
}
```

---

## DISTANCE

### GraphQL Input

```graphql
query {
    movies {
        title
        actorsConnection(
            where: {
                edge: {
                    location_DISTANCE: {
                        point: { longitude: 1.0, latitude: 2.0 }
                        distance: 3.0
                    }
                }
            }
        ) {
            edges {
                screenTime
                location {
                    latitude
                    longitude
                }
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
    WHERE distance(this_acted_in_relationship.location, point($this_actorsConnection.args.where.edge.location_DISTANCE.point)) = $this_actorsConnection.args.where.edge.location_DISTANCE.distance
    WITH collect({ screenTime: this_acted_in_relationship.screenTime, location: { point: this_acted_in_relationship.location }, node: { name: this_actor.name } }) AS edges
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
                "edge": {
                    "location_DISTANCE": {
                        "distance": 3,
                        "point": {
                            "latitude": 2,
                            "longitude": 1
                        }
                    }
                }
            }
        }
    }
}
```

---
