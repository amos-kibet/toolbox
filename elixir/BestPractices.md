## Elixir best practices

### `Map.get/2` and `Keyword.get/2` vs `Access`

`Map.get/2` and `Keyword.get/2` lock you into using a specific data structure. This means that if you want to change the type of structure, you now need to update all of the call sites. Instead of these functions, you should prefer using Access:

```Elixir
# Don't do
opts = %{foo: :bar}
Map.get(opts, :foo)

# Do
opts[:foo]
```

### Don’t pipe results into the following function

Side-effecting functions tend to return “results” like `{:ok, term()} | {:error, term()}`. If your dealing with side-effecting functions, don’t pipe the results into the next function. It’s always better to deal with the results directly using either `case` or `with`.

```Elixir

# Don't do...
def main do
  data
  |> call_service
  |> parse_response
  |> handle_result
end

defp call_service(data) do
# ...
end

defp parse_response({:ok, result}, do: Jason.decode(result)
defp parse_response(error, do: error)

defp handle_result({:ok, decoded}), do: decoded
defp handle_result({:error, error}), do: raise error

# Do...
def main do
  with {:ok, response} <- call_service(data),
       {:ok, decoded}  <- parse_response(response) do
    decoded
  end
end
```

Using pipes forces our functions to handle the previous function’s results, spreading error handling throughout our various function calls. Each of these functions has to know too much information about how it is being called.

If you are in a situation where errors are a vital part of your functions control flow, then it’s best to keep all of the error handling in the calling function using `case` statements.

```Elixir
# Do...
def main(id) do
  case :fuse.check(:service) do
    :ok ->
      case call_service(id) do
        {:ok, result} ->
          :ok = Cache.put(id, result)
          {:ok, result}

        {:error, error} ->
          :fuse.melt(:service)
          {:error, error}
      end

    :blown ->
      cached = Cache.get(id)

      if cached do
        {:ok, result}
      else
        {:error, error}
      end
  end
end
```

### Don't pipe into `case` statements

If you find yourself piping into `case`, it's almost always better to assign intermediate steps to a variable instead.

```Elixir
# Don't do...
build_post(attrs)
|> store_post()
|> case do
  {:ok, post} ->
    # ...

  {:error, _} ->
    # ...
end

# Do...

changeset = build_post(attrs)

case store_post(changeset) do
  {:ok, post} ->
    # ...

  {:error, _} ->
    # ...
end
```

### Don't hide higher-order functions

Higher order functions are functions that take other functions as arguments, or return other functions. If you’re working with collections, you should prefer to write functions that operate on a single entity rather than the collection itself. Then you can use higher-order functions directly in your pipeline.

```Elixir
# Don't do...
def main do
  collection
  |> parse_items
  |> add_items
end

def parse_items(list) do
  Enum.map(list, &String.to_integer/1)
end

def add_items(list) do
  Enum.reduce(list, 0, & &1 + &2)
end

# Do...
def main do
  collection
  |> Enum.map(&parse_item/1)
  |> Enum.reduce(0, &add_item/2)
end

defp parse_item(item), do: String.to_integer(item)

defp add_item(num, acc), do: num + acc
```

With this change, our `parse_item` and `add_item` functions become reusable in the broader set of contexts. These functions can now be used on a single item or can be lifted into the context of `Stream`, `Enum`, `Task`, or any number of other uses. Hiding this logic away from the caller is a worse design because it couples the function to its call site and makes it less reusable.

### Avoid `else` in `with` blocks

Handle both the happy and sad paths in individual functions, then when you combine them in a `with` block, only concern yourself with the happy path. Error handling should be done in individual functions, and not in the `else` clause of your `with` block.

```Elixir
# Don't do...
with {:ok, response} <- call_service(data),
     {:ok, decoded}  <- Jason.decode(response),
     {:ok, result}   <- store_in_db(decoded) do
  :ok
else
  {:error, %Jason.Error{}=error} ->
    # Do something with json error
  {:error, %ServiceError{}=error} ->
    # Do something with service error
  {:error, %DBError{}} ->
    # Do something with db error
end

# Do
with {:ok, response} <- call_service(data),
     {:ok, decoded}  <- Jason.decode(response),
     {:ok, result}   <- store_in_db(decoded) do
  :ok
end

defp call_service(data) do
    case data do
        happy_path -> {:ok, result_from_data}
        sad_path -> {:error, error_from_data}
    end
end

# Json.decode/2 returns {:ok, term()} | {:error, Jason.DecodeError.t()}

defp store_in_db(decoded) do
    case decoded do
        happy_path -> {:ok, result_from_decoded}
        sad_path -> {:error, error_from_decoded}
    end
end
```

### State what you want, not what you don't want.

Be more explicit about what it is you expect. You’d prefer to raise or crash if you receive arguments that would violate your expectations.

```Elixir
# Don't do...
def call_service(%{req: req}) when not is_nil(req) do
  # ...
end

# Do...
def call_service(%{req: req}) when is_binary(req) do
  # ...
end
```

### Only return error tuples when the caller can do something about it, otherwise, prefer to fail/raise an error.

You should only force your user to deal with errors that they can do something about. If your API can error, and there’s nothing the caller can do about it, then raise an exception or throw. Don’t bother making your callers deal with result tuples when there’s nothing they can do.

```Elixir
# Don't do...
def get(table \\ __MODULE__, id) do
  # If the table doesn't exist ets will throw an error. Catch that and return
  # an error tuple
  try do
    :ets.lookup(table, id)
  catch
    _, _ ->
      {:error, "Table is not available"}
  end
end

# Do...
def get(table \\ __MODULE__, id) do
  # If the table doesn't exist, there's nothing the caller can do
  # about it, so just throw.
  :ets.lookup(table, id)
end
```

### Raise exceptions if you receive invalid data.

You should not be afraid of just raising exceptions if a return value or piece of data has violated your expectations. If you’re calling a downstream service that should always return JSON, use `Jason.decode!` and avoid writing additional error handling logic.

```Elixir
# Don't do...
def main do
  {:ok, resp} = call_service(id)
  case Jason.decode(resp) do
    {:ok, decoded} ->
      decoded

    {:error, e} ->
      # Now what?...
  end
end

# Do...
def main do
  {:ok, resp} = call_service(id)
  decoded = Jason.decode!(resp)
end
```

This allows us to crash the process (which is good) and removes the useless error handling logic from the function.

### Use `for` when checking collections in tests

This makes test failures much more helpful.

```Elixir
# Don't do...
assert Enum.all?(posts, fn post -> %Post{} == post end)

# Do...
for post <- posts, do: assert %Post{} == post
```

**Reference**

https://keathley.io/blog/good-and-bad-elixir.html
