## Ecto

Ecto is split into 4 main components:

- `Ecto.Repo` - **where** the data is,
- `Ecto.Schema` - **what** the data is,
- `Ecto.Query` - **how to read** the data, and,
- `Ecto.Changeset` - **how to change** the data.

### Ecto.Query

- Provides the Query DSL.
- Queries come in two flavours:
  - keyword-based,
  - macro-based

**Keyword-based Example:**

```elixir
import Ecto.Query, only [from: 2]

# create a query
query = from u in "users", where: u.age > 18, select: u.name

# send the query to the repository
Repo.all(query)
```

**Macro-based variant**

```elixir
User
|> where([u], u.age > 18)
|> select([u], u.name)
|> Repo.all()
```

### Ecto.Query Functions ([Read more here](https://hexdocs.pm/ecto/Ecto.Query.html#functions))

**`from(expr, kw \\ [])`**

- Creates a query.
- It can either be a keyword query or a query expression.
- If it is a keyword query, the first argument must be either an `in` expression, a value that implements the `Ecto.Queryable` protocol, or an `Ecto.Query.API.fragment/1`. If the query needs a reference to the data source in any other part of the expression, then an `in` must be used to create a reference variable. The second argument should be a keyword query where the keys are expression types and the values are expressions.
- If it is a query expression the first argument must be a value that implements the `Ecto.Queryable` protocol and the second argument the expression.

**Examples:**

```elixir
def published(query) do
    from p in query, where: not(is_nil(p.published_at))
end
```

- In the above example, notice we have created a `p` variable to reference the query's original data source. This assumes that the original query only had one source. When the given query has more than one source, positional or named bindings may be used to access the additional sources.

```elixir
def published_multi(query) do
    from [p, o] in query,
    where: not(is_nil(p.published_at)) and not(is_nil(o.published_at))
end
```

**`join(query, qual, binding \\ [], expr, opts \\ [])`**

- Receives a source that is to be joined to the query and a condition for the join.
- The join condition can be any expression that evaluates to a boolean value.
- The qualifier must be one of `:inner`, `:left`, `:right`, `:cross`, `:cross_lateral`, `:full`, `:inner_lateral`, `:left_lateral`, `:array` or `:left_array`.
- For a keyword query the `:join` keyword can be changed to `:inner_join`, `:left_join`, `:right_join`, `:cross_join`, `:cross_lateral_join`, `:full_join`, `:inner_lateral_join`, `:left_lateral_join`, `:array_join` or `:left_array_join`. `:join` is equivalent to `:inner_join`.
- Currently, it is possible to join on:

  - an `Ecto.Schema`, such as `p in Post`.
  - an interpolated Ecto query with zero or more `where` clauses, such as `c in ^(from "posts", where: [public: true])`.
  - an association, such as `c in assoc(post: comments)`.
  - a subquery, such as `c in subquery(another_query)`.
  - a query fragment, such as `c in fragment("SOME COMPLEX QUERY")`.

- Each join accepts the following options:

  - `:on` - a query expression or keyword list to filter the join, defaults to `true`.
  - `:as` - a named binding for the join.
  - `:prefix` - the prefix to be used for the join when issuing a database query.
  - `:hint` - a string or a list of strings to be used as databse hints.

- In the keyword query syntax, those options must be given immediately after the join.
- In the expression syntax, the options are given as the fifth argument.

**Keywords examples:**

```elixir
from c in Comment,
    join: p in Post,
    on: p.id == c.post_id,
    select: {p.title, c.text}

# Keywords can also be given or interpolated as part of `on`:
from c in Comment,
    join: p in Post,
    on: [id: c.post_id],
    select: {p.title, c.text}

from p in Post,
    left_join: c in assoc(p, :comments),
    select: {p, c}

# It is also possible to interpolate an Ecto query on the right-hand side of `in`:
posts = Post
from c in Comment,
    join: p in ^posts,
    on: [id: c.post_id],
    select: {p.title, c.text}

# Interpolating is specially useful to dynamically join on existing queries:
posts =
    if params["drafts"] do
    from p in Post, where: [drafts: true]
    else
    from p in Post, where: [public: true]
    end

from c in Comment,
    join: p in ^posts, on: [id: c.post_id],
    select: {p.title, c.text}
```

**Expressions examples:**

```elixir
Comment
|> join(:inner, [c], p in Post, on: c.post_id == p.id)
|> select([c, p], {p.title, c.text})

Post
|> join(:left, [p], c in assoc(p, :comments))
|> select([p, c], {p, c})
```

**Joining with fragments**

- Used when you need to join on a complex query.
- Although using fragments in joins is discouraged in favor of Ecto Query syntax, they are necessary when writing lateral joins as lateral joins require a subquery that refer to previous bindings:

  ```elixir
  Game
  |> join(:inner_lateral, [g], gs in fragment("SELECT * FROM games_sold AS gs WHERE gs.game_id = ? ORDER BY gs.sold_on LIMIT 2", g.id))
  |> select([g, gs], {g.name, gs.sold_on})
  ```

**select(query, binding \\ [], expr)`**

- Selects which fields will be selected from the schema and any transformations that should be performed on the fields.
- Select allows each expression to be wrapped in `lists`, `tuples` or `maps` as shown in the examples below. A full schema can also be selected.
- **Note:** There can only be one select expression in a query, if the select expression is omitted, the query will by default select the full schema.
- `select` also accepts a list of atoms where each atom refers to a field in the source to be selected.

**Keywords examples:**

```elixir
from(c in City, select: c) # returns the schema as a struct
from(c in City, select: {c.name, c.population})
from(c in City, select: [c.name, c.county])
from(c in City, select: %{n: c.name, answer: 42})
from(c in City, select: %{c | alternative_name: c.name})
from(c in City, select: %Data{name: c.name})
```

**Expressions examples:**

```elixir
City |> select([c], c)
City |> select([c], {c.name, c.country})
City |> select([c], %{"name" => c.name})
City |> select([:name])
City |> select([c], struct(c, [:name]))
City |> select([c], map(c, [:name]))
City |> select([c], %{c | geojson: nil, text: "<redacted>"})
```

**`where(query, binding \\ [], expr)`**

- An `AND` `where` query expression.
- `where` expressions are used to filter the result set. If there is more than one `where` expression, they are combined with an `and` operator.
- All `where` expressions have to evaluate to a boolean value.
- `where` also accepts a keyword list where the field given as key is going to be compared with the given value. The fields will always refer to the source given in `from`.

**Keywords examples:**

```elixir
from(c in City, where: c.country == "Sweden")
from(c in City, where: [country: "Sweden"])

# It is also possible to interpolate the whole keyword list, allowing you to dynamically filter the source:
filters = [country: "Sweden"]
from(c in City, where: ^filters)
```

**Expressions examples:**

```elixir
City |> where([c], c.country == "Sweden")
City |> where([country: "Sweden"])
```
