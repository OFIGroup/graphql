# Cypher -> Connections -> Filtering -> Relationship -> Temporal

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
    startDate: Date
    endDateTime: DateTime
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
                    startDate_GT: "2000-01-01"
                    endDateTime_LT: "2010-01-01T00:00:00.000Z"
                }
            }
        ) {
            edges {
                startDate
                endDateTime
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
    WHERE this_acted_in_relationship.startDate > $this_actorsConnection.args.where.edge.startDate_GT AND this_acted_in_relationship.endDateTime < $this_actorsConnection.args.where.edge.endDateTime_LT
    WITH collect({ startDate: this_acted_in_relationship.startDate, endDateTime: apoc.date.convertFormat(toString(this_acted_in_relationship.endDateTime), "iso_zoned_date_time", "iso_offset_date_time"), node: { name: this_actor.name } }) AS edges
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
                    "endDateTime_LT": {
                        "day": 1,
                        "hour": 0,
                        "minute": 0,
                        "month": 1,
                        "nanosecond": 0,
                        "second": 0,
                        "timeZoneId": null,
                        "timeZoneOffsetSeconds": 0,
                        "year": 2010
                    },
                    "startDate_GT": {
                        "day": 1,
                        "month": 1,
                        "year": 2000
                    }
                }
            }
        }
    }
}
```

---
