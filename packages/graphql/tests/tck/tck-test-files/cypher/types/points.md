# Cypher Points

Tests Cypher generation for arrays of spatial types. [Point] and [CartesianPoint] are processed equivalently when it comes to Cypher translation, so only one needs to be extensively tested.

Schema:

```graphql
type PointContainer {
    id: String
    points: [Point]
}
```

---

## Simple Points query

### GraphQL Input

```graphql
{
    pointContainers(where: { points: [{ longitude: 1.0, latitude: 2.0 }] }) {
        points {
            longitude
            latitude
            crs
        }
    }
}
```

### Expected Cypher Output

```cypher
MATCH (this:PointContainer)
WHERE this.points = [p in $this_points | point(p)]
RETURN this { points: [p in this.points | { point:p, crs: p.crs }] } as this
```

### Expected Cypher Params

```json
{
    "this_points": [
        {
            "longitude": 1,
            "latitude": 2
        }
    ]
}
```

---

## Simple Points NOT query

### GraphQL Input

```graphql
{
    pointContainers(
        where: { points_NOT: [{ longitude: 1.0, latitude: 2.0 }] }
    ) {
        points {
            longitude
            latitude
        }
    }
}
```

### Expected Cypher Output

```cypher
MATCH (this:PointContainer)
WHERE (NOT this.points = [p in $this_points_NOT | point(p)])
RETURN this { points: [p in this.points | { point:p }] } as this
```

### Expected Cypher Params

```json
{
    "this_points_NOT": [
        {
            "longitude": 1,
            "latitude": 2
        }
    ]
}
```

---

## Simple Points INCLUDES query

### GraphQL Input

```graphql
{
    pointContainers(
        where: { points_INCLUDES: { longitude: 1.0, latitude: 2.0 } }
    ) {
        points {
            longitude
            latitude
            crs
        }
    }
}
```

### Expected Cypher Output

```cypher
MATCH (this:PointContainer)
WHERE point($this_points_INCLUDES) IN this.points
RETURN this { points: [p in this.points | { point:p, crs: p.crs }] } as this
```

### Expected Cypher Params

```json
{
    "this_points_INCLUDES": {
        "longitude": 1,
        "latitude": 2
    }
}
```

---

## Simple Points NOT INCLUDES query

### GraphQL Input

```graphql
{
    pointContainers(
        where: { points_NOT_INCLUDES: { longitude: 1.0, latitude: 2.0 } }
    ) {
        points {
            longitude
            latitude
            crs
        }
    }
}
```

### Expected Cypher Output

```cypher
MATCH (this:PointContainer)
WHERE (NOT point($this_points_NOT_INCLUDES) IN this.points)
RETURN this { points: [p in this.points | { point:p, crs: p.crs }] } as this
```

### Expected Cypher Params

```json
{
    "this_points_NOT_INCLUDES": {
        "longitude": 1,
        "latitude": 2
    }
}
```

---

## Simple Points create mutation

### GraphQL Input

```graphql
mutation {
    createPointContainers(
        input: { points: [{ longitude: 1.0, latitude: 2.0 }] }
    ) {
        pointContainers {
            points {
                longitude
                latitude
                crs
            }
        }
    }
}
```

### Expected Cypher Output

```cypher
CALL {
    CREATE (this0:PointContainer)
    SET this0.points = [p in $this0_points | point(p)]
    RETURN this0
}

RETURN this0 { points: [p in this0.points | { point:p, crs: p.crs }] } AS this0
```

### Expected Cypher Params

```json
{
    "this0_points": [
        {
            "longitude": 1,
            "latitude": 2
        }
    ]
}
```

---

## Simple Points update mutation

### GraphQL Input

```graphql
mutation {
    updatePointContainers(
        where: { id: "id" }
        update: { points: [{ longitude: 1.0, latitude: 2.0 }] }
    ) {
        pointContainers {
            points {
                longitude
                latitude
                crs
            }
        }
    }
}
```

### Expected Cypher Output

```cypher
MATCH (this:PointContainer)
WHERE this.id = $this_id
SET this.points = [p in $this_update_points | point(p)]
RETURN this { points: [p in this.points | { point:p, crs: p.crs }] } AS this
```

### Expected Cypher Params

```json
{
    "this_id": "id",
    "this_update_points": [
        {
            "longitude": 1,
            "latitude": 2
        }
    ]
}
```
