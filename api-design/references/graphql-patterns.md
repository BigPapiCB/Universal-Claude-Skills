# GraphQL Patterns Reference

## Schema Design

```graphql
type Query {
  user(id: ID!): User
  users(filter: UserFilter, first: Int, after: String): UserConnection!
}

type Mutation {
  createUser(input: CreateUserInput!): CreateUserPayload!
  updateUser(id: ID!, input: UpdateUserInput!): UpdateUserPayload!
}
```

## Conventions

- Use `input` types for mutations (not inline args)
- Return payload types from mutations (not raw objects)
- Use Relay-style connections for pagination
- Nullable by default, use `!` only when guaranteed

## Pagination (Relay Connection Spec)

```graphql
type UserConnection {
  edges: [UserEdge!]!
  pageInfo: PageInfo!
  totalCount: Int!
}

type PageInfo {
  hasNextPage: Boolean!
  hasPreviousPage: Boolean!
  startCursor: String
  endCursor: String
}
```

## Error Handling

```graphql
type CreateUserPayload {
  user: User
  errors: [UserError!]!
}

type UserError {
  field: String!
  message: String!
}
```

## Anti-Patterns

- **No depth limiting** → Add query depth limits or `@cost` directives
- **N+1 in resolvers** → Use DataLoader for batching
- **Exposing internal columns** → Curate your schema
