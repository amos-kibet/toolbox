# GraphQL Resolvers

GraphQL resolvers act as the bridge between your GraphQL schema and the data stored in the database, fetching the exact data requested by a query. When you ask for specific data, like a user's name and age, the resolver first gets the user info, then grabs the name and age details. It's like following a map where each stop gives you a piece of the overall picture you're trying to build.

## GraphQL Field Resolvers

A GraphQL field resolver is basically a function that figures out what data you get when you ask for a specific piece of information in a GraphQL query. Imagine you're asking a GraphQL server for some info; this server then uses resolver functions to go and fetch the data you requested from wherever it's stored.

The resolver function gets four pieces of information to work with:

```JavaScript
fieldName(parent, args, context, info) {}
```

- **parent** - this is the data that came from the previous step. If this is the first step, it is called `rootValue`.
- **args** - these are the details you specify in your query, like asking for a user by their ID.
- **context** - this is shared info for all the resolvers to use during a request. It's used for keeping track of things like who's asking for data and where to get it from.
- **info** - this gives more details about the query, like what part of the query you're dealing with.

**Referrences:**

1. https://daily.dev/blog/graphql-field-resolver-essentials
