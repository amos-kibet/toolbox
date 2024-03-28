## Absinthe and the Tech Stack

This is a tool specifically for Elixir's implementation of GraphQL. For intro notes on GraphQL, refer to [this](Introduction.md) tool.

At a high-level, this is how a GraphQL request is handled, in Elixir-world:

![Handling GraphQL request in Elixir diagram](../media/graphql_request_in_elixir.png)

### Queries

**Providing Field Argument Values**

There are two ways that a GraphQL user can provide values for an argument: as document literals, and as variables.

**Using Literals**

```GraphQL
{
    menuItems(matching: "reu") {
        name
    }
}
```

**Using Variables**

GraphQL variables act as typed placeholders for values that will be sent along with the request.

GraphQL variables are declared with their types, before they are used, alongside the operation type.

```GraphQL
query ($term: String) {
    menuItems(matching: $term) {
        name
    }
}
```

**Using Enumeration Types**

A GraphQL enumeration is a special type of scalar that has a defined, finite set of possible values.

Examples:

- Available shirt sizes: S, M, L and XL,
- Color components: RED, BLUE, and GREEN
- Ordering: ASC and DESC

### Mutations

**Defining a Root Mutation Type**

To support mutation operations, we need to define a root mutation type, just as we did for queries. This will be used as the entry point for GraphQL mutation operations, and it will define--based on the mutation fields that we add--the complete list of capabilities that the users of our API will have available to modify data.

To create menu items, we'll need to accept information from the client. We'll use an input object to model the data that we will be expecting.

Mutation example:

```GraphQL
mutation CreateMenuItem($menuItem: MenuItemInput!) {
    createMenuItem(input: $menuItem) {
        id
        name
        description
        price
        category { name }
        tags { name }
    }
}
```

### Retrieving multiple data at once using the same fields

Imagine you wanted to search for two meals, one "inHand" and another "inGlass". You can issue one GraphQL document, with aliases and you'll get the data in one request. Here's how you can achieve that.

**GraphQL document**:

```GraphQL
query Meal {
    inHand: search(matching: "reu") { name }
    inGlass: search(matching: "lem") { name }
}
```

**API response**:

```JSON
{
    "data": {
        "inHand": [
            {
                "name": "Reuben"
            }
        ],
        "inGlass": [
            {
                "name": "Lemonade"
            }
        ]
    }
}
```

### Handling Mutation Errors

There are two approaches that you can use in your Absinthe schema to give users more information when they encounter an error: using simple `:error` tuples, and, modelling the errors directly as types.

**Using Tuples**

Here's an example of a resolver function that uses tuples to handle errors:

```Elixir
def create_item(_, %{input: params}, _) do
    case Menu.create_item(params) do
      {:ok, item} ->
        {:ok, item}

      {:error, changeset} ->
        {:error, message: "Could not create menu item", details: error_details(changeset)}
    end
  end

  defp error_details(changeset) do
    Ecto.Changeset.traverse_errors(changeset, fn {msg, _opts} -> msg end)
  end
```

Here's how the return value of the resolver function would look like in case of an error:

```Elixir
{
    :error,
    message: "Could not create menu item",
    details: %{
        "name" => ["has already been taken"]
    }
}
```

And, here is the serialized API response:

```JSON
{
    "data": {
        "createMenuItem": null
    },
    "errors": [
        {
            "message": "Could not create menu item",
            "details": {
                "name": ["has already been taken"]
            },
            "locations": [{"line": 2, "column": 0}],
            "path": [
                "createMenuItem"
            ]
        }
    ]
}
```

**NOTE:** Errors are reported separate of data values in a GraphQL response.

**Errors as data**

We can define the structure of our errors as normal types, so that we support introspection and enable better integration with clients.

An example of a GraphQL document that has errors as type is shown below.

```GraphQL
mutation ($menuItem: MenuItemInput!) {
    createMenuItem(input: $menuItem) {
        errors {
            key
            message
        }
        menuItem {
            name
            description
            price
        }
    }
}
```

When clients receive responses for this document, they can intepret the success of the result by checking the value of `menuItem` and/or `errors`, then give feedback to users appropriately.

Here's an example API response incase of an error.

```JSON
{
    "data": {
        "createMenuItem": {
            "errors": [
                {
                    "key": "name",
                    "message": "has already been taken"
                }
            ],
            "menuItem": nil
        }
    }
}
```

### Understanding Abstract Types

These types give you the flexibility to resolve API client requests that do not point to an exact schema type, like searching for data that can be found across multiple database tables.

GraphQL specification gives two ways to work with abstract types: unions and interfaces.

**Unions**

This is an abstract type that represents a set of specific concrete types. For instance, in a search example, a `:search_result` could be a union for both `:menu_item` and `:category`.

Since API response resulting from unions may be coming from different sources (database tables) you may want to know which pieces of data belong to which source. `__typename` is a GraphQL's built-in introspection field that returns the concrete GraphQL type for the surrounding scope of data.

**Interfaces**

GraphQL interfaces are similar to unions, with one key difference: they add a requirement that any member types must define a set of included fields.

### Using fragments

Named fragments are just like inline fragments, but they're reusable.

Example:

```GraphQL
query Search($term: String!) {
    search(matching: $term) {
        ... MenuItemFields
        ... CategoryFields
    }
}

fragment MenuItemFields on MenuItem {
    name
}

fragment CategoryFields on Category {
    name
    items {
        ... MenuItemFields
    }
}
```

### Absinthe.Resolution

`%Absinthe.Resolution{}` is a struct that carries information about the current resolution.

Just like `Plug`'s `%Conn{}`, it is the piece of information that is passed along from middleware to middleware as part of resolution.

**Contents:**

- `adapter` - The adapter used for any name conversions.
- `definition` - The blueprint definition for this field.
- `context` - The context passed to `Absinthe.run`.
- `root_value` - The root value passed to `Absinthe.run`.
- `parent_type` - The parent type for the field.
- `private` - Operates similarly to the `:private` key on a `%Plug.Conn{}` struct.
- `schema` - The current schema.
- `source` - The resolved parent objectl source of this field.

### Subscriptions

Subscriptions let a client submit a GraphQL document that, instead of being executed immediately, is executed on the basis of some event in the system.

The first thing to do is adding an `Absinthe.Subscription` supervisor to the application's supervision tree. The supervisor starts a number of processes that will handle broadcasting results and hold on to subscription documents.

**Event modeling**

This involves building out the schema, migration and context modules for your data.

### Resolvers

A resolver is a function that sits between your schema and your context modules. It relays data from your schema to your context, and vice versa.

A resolver can take two forms:

- A function with an arity of 3 (taking a parent, arguments, and a resolution struct),
- An alternative, short form with an arity of 2 (omitting the first parameter, the parent).

The first argument (parent) of a resolver matches the parent object of a field.

The second argument (arguments) is a map of user input data.

The third argument (resolution struct) carries along with it the Absinthe context, a data structure that serves as the integration point with external mechanisms--like Plug that authenticates the current user.

### Dataloader

It's a tool that provides an easy way to load data in batches.

The core concept of dataloader is a datasource which is just a struct that encodes a way of retrieving data.

### Testing Absinthe GraphQL APIs

There are three main approaches to testing GraphQL APIs built with Absinthe:

- Testing resolver functions, since they do most of the work,
- Testing GraphQL document execution directly via `Absinthe.run`, and,
- Outside-in, testing the full HTTP request/response cycle.

**Outside-in testing, with Absinthe Plug**

To test HTTP requests with Absinthe, we'll need `absinthe_plug` library.

**Example:**

Say we want to test the folowing example:

```Elixir
defmodule MyAppWeb.Schema do
  use Absinthe.Schema

  @fakedb %{
    "1" => %{name: "Bob", email: "bubba@foo.com"},
    "2" => %{name: "Fred", email: "fredmeister@foo.com"}
  }

  query do
    field :user, :user do
      arg :id, non_null(:id)

      resolve &find_user/2
    end
  end

  object :user do
    field :name, :string
    field :email, :string
  end

  defp find_user(%{id: id}, _) do
    {:ok, Map.get(@fakedb, id)}
  end
end
```

The test could look something like this:

```Elixir
defmodule MyAppWeb.SchemaTest do
  use MyAppWeb.ConnCase

  @user_query """
  query getUser($id: ID!) {
    user(id: $id) {
      name
      email
    }
  }
  """

  test "query: user", %{conn: conn} do
    conn =
      post(conn, "/api", %{
        "query" => @user_query,
        "variables" => %{id: 1}
      })

    assert json_response(conn, 200) == %{
             "data" => %{"user" => %{"email" => "bubba@foo.com", "name" => "Bob"}}
           }
  end
end

```
