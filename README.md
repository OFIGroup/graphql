# Forked from @neo4j/graphql
This was forked so that @auth directives could be added to implment node level authorization, as this was not implmented at the time of writing.  PR: 500 was used as a starting point but only the code from create-auth-and-params.ts was brought across. https://github.com/neo4j/graphql/pull/500

The custom code has been added on v2.3.0 of the library: 
https://github.com/neo4j/graphql/tree/%40neo4j/graphql%402.3.0

## Deploying this Fork
1. In the root folder, run: `yarn`
1. Ensure that all tests pass: `yarn test`
1. Then run: `yarn build`
1. From the `packages/graphql` folder, copy the `dist` folder and the `package.json` file into project you want to use it in
1. Then run: `npm i`
---
---
---

# Neo4j GraphQL Library

💡 Welcome to the Monorepo for [Neo4j](https://neo4j.com/) + [GraphQL](https://graphql.org/).

![Neo4j + GraphQL](./docs/modules/ROOT/images/banner.png)

<p align="center">
  <a href="https://discord.gg/neo4j">
    <img alt="Discord" src="https://img.shields.io/discord/787399249741479977?logo=discord&logoColor=white">
  </a>
  <a href="https://community.neo4j.com/c/drivers-stacks/graphql/33">
    <img alt="Discourse users" src="https://img.shields.io/discourse/users?logo=discourse&server=https%3A%2F%2Fcommunity.neo4j.com">
  </a>
</p>

## Contributing

Want to contribute to `@neo4j/graphql`? See our [contributing guide](./docs/markdown/CONTRIBUTING.md) and [development guide](./docs/markdown/DEVELOPING.md) to get started!

## Links

1. [Documentation](https://neo4j.com/docs/graphql-manual/current/)
2. [Discord](https://discord.gg/neo4j)
3. [Examples](./examples)

## Navigating

This is a TypeScript Monorepo managed with [Yarn Workspaces](https://classic.yarnpkg.com/en/docs/workspaces/). To learn more on how to; setup, test and contribute to Neo4j GraphQL then please visit the [Contributing Guide](./CONTRIBUTING.md).

1. [`@neo4j/graphql`](./packages/graphql) - Familiar GraphQL generation, for usage with an API such as [Apollo Server](https://www.apollographql.com/docs/apollo-server/)
2. [`@neo4j/graphql-ogm`](./packages/ogm) - Use GraphQL Type Definitions to drive interactions with the database

## Media

Blogs, talks and other content surrounding Neo4j GraphQL. Sign up for [NODES 2021](https://neo4j.brand.live/c/2021nodes-live) to view even more Neo4j GraphQL content.

1. [Neo4j and GraphQL The Past, Present and Future](https://youtu.be/sZ-eBznM71M)
2. [Securing Your Graph With Neo4j GraphQL](https://medium.com/neo4j/securing-your-graph-with-neo4j-graphql-91a2d7b08631)
3. [Best Practices For Using Cypher With GraphQL](https://youtu.be/YceBpk01Gxs)
4. [Migrating To The Official Neo4j GraphQL Library](https://youtu.be/4_rp1ikvFKc)
5. [Announcing the Stable Release of the Official Neo4j GraphQL Library 1.0.0](https://medium.com/neo4j/announcing-the-stable-release-of-the-official-neo4j-graphql-library-1-0-0-6cdd30cd40b)
6. [Announcing the Neo4j GraphQL Library Beta Release](https://medium.com/neo4j/announcing-the-neo4j-graphql-library-beta-99ae8541bbe7)
