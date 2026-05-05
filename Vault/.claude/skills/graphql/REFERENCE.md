# graphql — Reference

## Schema Approach Comparison

| | SDL-first | Pothos (code-first) |
|---|---|---|
| Schema source | `.graphql` / SDL string | TypeScript builder API |
| Type safety | Via codegen | Native TypeScript inference |
| Codegen required | Yes (graphql-codegen) | No |
| Schema as contract | Natural fit | Export SDL when needed |
| Best for | Public/multi-team APIs | Internal TypeScript teams |
| Adoption | Traditional standard | Mainstream (Airbnb, Netflix) |

---

## BUILD — Apollo Server v5

```bash
npm install @apollo/server graphql
```

```ts
// server.ts
import { ApolloServer } from '@apollo/server'
import { startStandaloneServer } from '@apollo/server/standalone'

const typeDefs = `#graphql
  type Query {
    users: [User!]!
    user(id: ID!): User
  }
  type User {
    id: ID!
    name: String!
    posts: [Post!]!
  }
  type Post { id: ID!; title: String! }
`

const resolvers = {
  Query: {
    users: (_, __, ctx) => ctx.dataSources.userLoader.loadMany([]),
    user: (_, { id }, ctx) => ctx.dataSources.userLoader.load(id),
  },
  User: {
    // DataLoader — never resolve N+1 inline
    posts: (user, _, ctx) => ctx.dataSources.postsByUserLoader.load(user.id),
  },
}

const server = new ApolloServer({ typeDefs, resolvers })
const { url } = await startStandaloneServer(server, {
  context: async ({ req }) => ({
    user: await verifyToken(req.headers.authorization), // auth in context
    dataSources: buildLoaders(),
  }),
})
```

---

## BUILD — GraphQL Yoga v5

```bash
npm install graphql-yoga graphql
```

```ts
import { createYoga, createSchema } from 'graphql-yoga'
import { createServer } from 'node:http'

const yoga = createYoga({
  schema: createSchema({ typeDefs, resolvers }),
  context: async ({ request }) => ({
    user: await verifyToken(request.headers.get('authorization')),
    loaders: buildLoaders(),
  }),
  // Security defaults:
  maskedErrors: true,        // hide internal errors in production
  graphiql: process.env.NODE_ENV !== 'production',
})

createServer(yoga).listen(4000)
```

---

## BUILD — Pothos (Code-First)

```bash
npm install @pothos/core graphql @apollo/server
```

```ts
import SchemaBuilder from '@pothos/core'

const builder = new SchemaBuilder<{
  Context: { user: User | null }
}>({})

const UserType = builder.objectType('User', {
  fields: (t) => ({
    id: t.exposeID('id'),
    name: t.exposeString('name'),
    posts: t.field({
      type: [PostType],
      resolve: (user, _, ctx) => ctx.loaders.postsByUser.load(user.id),
    }),
  }),
})

builder.queryType({
  fields: (t) => ({
    user: t.field({
      type: UserType,
      nullable: true,
      args: { id: t.arg.id({ required: true }) },
      resolve: (_, { id }, ctx) => {
        if (!ctx.user) throw new Error('Unauthenticated') // auth in resolver
        return ctx.loaders.user.load(id)
      },
    }),
  }),
})

export const schema = builder.toSchema()
```

---

## DataLoader — N+1 Solution

```bash
npm install dataloader
```

```ts
import DataLoader from 'dataloader'

// Batch function: called once per tick with all accumulated keys
const batchUsers = async (ids: readonly string[]) => {
  const users = await db.query('SELECT * FROM users WHERE id = ANY($1)', [ids])
  return ids.map(id => users.find(u => u.id === id) ?? null)
}

export function buildLoaders() {
  return {
    user: new DataLoader(batchUsers),
    postsByUser: new DataLoader(async (userIds: readonly string[]) => {
      const posts = await db.query(
        'SELECT * FROM posts WHERE user_id = ANY($1)', [userIds]
      )
      return userIds.map(id => posts.filter(p => p.userId === id))
    }),
  }
}
// Instantiate per request — never share loaders across requests
```

---

## SUBSCRIPTIONS — graphql-ws

```bash
npm install graphql-ws ws graphql
```

```ts
// Server (Yoga has built-in support)
import { useServer } from 'graphql-ws/lib/use/ws'
import { WebSocketServer } from 'ws'

const wsServer = new WebSocketServer({ path: '/graphql', server: httpServer })
useServer({ schema, context: async (ctx) => ({ user: await verifyWsToken(ctx) }) }, wsServer)
```

```ts
// Client
import { createClient } from 'graphql-ws'
const client = createClient({ url: 'ws://localhost:4000/graphql' })

const sub = client.subscribe(
  { query: `subscription { messageAdded { id text } }` },
  { next: (data) => console.log(data), error: console.error, complete: () => {} }
)
// Cleanup
sub() // call to unsubscribe
```

**Migration from subscriptions-transport-ws:** Apollo Client v3.5+ supports both protocols via `split` link. Migrate server first, then clients. Full migration guide: apollographql.com/docs/apollo-server/data/subscriptions.

---

## FEDERATION — Cosmo Router (recommended open-source)

```bash
# Install wgc CLI
npm install -g wgc@latest

# Define subgraphs
wgc subgraph create users --label env=prod --routing-url http://users:4001/graphql
wgc federated-graph create my-graph --routing-url https://router.example.com/graphql

# Publish schema
wgc subgraph publish users --schema ./users.graphql
```

```ts
// Subgraph (any GraphQL server with federation directives)
const typeDefs = `#graphql
  extend schema @link(url: "https://specs.apollo.dev/federation/v2.0", import: ["@key"])
  type User @key(fields: "id") {
    id: ID!
    name: String!
  }
  type Query {
    user(id: ID!): User
  }
`
```

**Apollo Router** — use for existing Apollo GraphOS orgs. Warning: Apollo GraphOS free tier is limited; enterprise pricing kicks in at scale. Evaluate Cosmo Router before committing.

---

## graphql-codegen

```bash
npm install -D @graphql-codegen/cli @graphql-codegen/typescript @graphql-codegen/typescript-operations
```

```yaml
# codegen.yml
schema: http://localhost:4000/graphql
documents: 'src/**/*.graphql'
generates:
  src/generated/types.ts:
    plugins:
      - typescript
      - typescript-operations
```

Run `npx graphql-codegen` to generate TypeScript types for all operations. For Pothos projects: skip codegen — TypeScript types are native.

---

## REVIEW — Security Checklist

| Check | Pass condition |
|---|---|
| Safelisting active | Production queries allowlisted by hash; arbitrary operations rejected |
| Introspection disabled | `introspection: false` in production server config |
| Query depth limit | Max depth configured (recommend: 7 for most APIs) |
| Query complexity limit | Complexity budget configured; expensive resolvers assigned weight |
| Auth in resolver context | `ctx.user` checked before data access — not schema directives only |
| DataLoader on all relations | No `findMany` inside resolver loops — all batched |
| Error masking | `maskedErrors: true` or equivalent — no stack traces in production |
| Rate limiting | Per-IP or per-user rate limit on `/graphql` endpoint |
| subscriptions-transport-ws removed | Only `graphql-ws` used — check `package.json` |
| Apollo Server version | v5+ only — v4 is EOL January 2026 |
