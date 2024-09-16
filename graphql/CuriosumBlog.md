## Curiosum GraphQL Blog Post

### Configuring the formatter to work with Absinthe GraphQL files

Add these to the `.formatter.exs` file:

```Elixir
import_deps: [..., :absinthe],
plugins: [..., Absinthe.Formatter],
inputs: [..., "{lib,priv}/**/*.{gpl,grpaghql}"]
```

### Configuring Plug's Parser

To configure the parser we have to add `Absinthe.Plug.Parser` to the parsers option, this will add support for incoming requests of type `application/graphql` by extracting the query from the body.

```Elixir
# lib/Myapp_web/endpoint.ex
plug.Plug.Parsers,
    parsers: [..., Absinthe.Plug.Parser],

    ...
```

### Configuring router

We'll create a dedicated Plug router for GraphQL, to keep things clean. This also becomes handy if you need to create both GraphQL and RESTful API endpoints, where in the case of REST APIs, you can decide to create a dedicated router, or use the default one.

```Elixir
# lib/app_web/graphql/router.ex
defmodule AppWeb.GraphQl.Router do
  use Plug.Router

  plug :match
  plug :dispatch

  forward "/graphql",
    to: Absinthe.Plug,
    init_opts: []
end
```

As you can see, the `init_opts` is left empty; it will be expanded on later. Now, forward GraphQL requests to this router.

```Elixir
# lib/app_web/router.ex
scope "/", AppWeb do
    forward "/api", GraphQL.Router
end
```

### Tests

GraphQL tests can be approached in three ways:

- Testing resolver functions,
- Testing GraphQL document execution, using the `Absinthe.run` function, and,
- Testing the full HTTP request/response cycle. This is the preferred approach as it exercises most of your code.

**Creating a helper function to POST GraphQL requests**

```Elixir
# test/support/helpers/graphql_helpers.ex
defmodule AppWeb.GraphQLHelpers do
    import Phoenix.ConnTest
    import Plug.Conn

    # Required by the post function from Phoenix.ConnTest
    @endpoint AppWeb.Endpoint

    def gql_post(options, response \\ 200) do
        build_conn()
        |> put_resp_content_type("application/json")
        |> post("/api/graphql", options)
        |> json_response(response)
    end
end
```

**Creating GraphQL test case for queries and mutations**

```Elixir
# test/support/graphql_case.ex
defmodule AppWeb.GraphQLCase do
    use ExUnit.CaseTemplate

    using do
        quote do
            use AppWeb.ConnCase

            import AppWeb.QgraphQLHelpers
        end
    end
end
```

**Example: testing queries**

```Elixir
# test/app_web/graphql/queries/blog/post_queries_test.exs
defmodule AppWeb.GraphQl.Blog.PostQueriesTest do
  use AppWeb.GraphQlCase

  alias App.BlogFixtures

  describe "get_post" do
    @get_post """
    query($id: ID!) {
      getPost(id: $id) {
        id
        title
      }
    }
    """

    test "returns post when found" do
      post = BlogFixtures.post_fixture()

      assert %{
               "data" => %{
                 "getPost" => %{
                   "id" => to_string(post.id),
                   "title" => post.title
                 }
               }
             } ==
               gql_post(%{
                 query: @get_post,
                 variables: %{"id" => post.id}
               })
    end

    test "returns 'not_found' error when missing" do
      assert %{
               "data" => nil,
               "errors" => [%{"message" => "not_found"}]
             } =
               gql_post(%{
                 query: @get_post,
                 variables: %{"id" => -1}
               })
    end
  end
end
```

**Example: testing mutations**

```Elixir
# test/app_web/graphql/mutations/blog/post_mutations_test.exs
defmodule AppWeb.GraphQl.Blog.PostMutationsTest do
  use AppWeb.GraphQlCase

  alias App.AccountsFixtures

  describe "create_post" do
    @create_post """
    mutation($input: CreatePostInput!) {
      createPost(input: $input) {
        id
        title
        userId
      }
    }
    """

    test "creates post when input is valid" do
      user = AccountsFixtures.user_fixture()

      variables = %{
        "input" => %{
          "title" => "valid title",
          "userId" => user.id
        }
      }

      assert %{
               "data" => %{
                 "createPost" => %{
                   "id" => _,
                   "title" => "valid title",
                   "userId" => user_id
                 }
               }
             } =
               gql_post(%{
                 query: @create_post,
                 variables: variables
               })

      assert to_string(user.id) == user_id
    end

    test "returns error when input is invalid" do
      user = AccountsFixtures.user_fixture()

      variables = %{
        "input" => %{
          "title" => "bad",
          "userId" => user.id
        }
      }

      assert %{
               "data" => nil,
               "errors" => [
                 %{
                   "details" => %{"field" => ["title"]},
                   "message" => "should be at least 4 character(s)"
                 }
               ]
             } =
               gql_post(%{
                 query: @create_post,
                 variables: variables
               })
    end
  end
end
```

**Creating GraphQL test case for subscriptions**

Testing the subscription is different than testing queries or mutations. We have to create a socket, subscribe to a subscription, trigger the subscription with mutation, and lastly check if the subscription returns what we want.

We have to define a `channel_case` helper module. Documentation on how `channels` are tested is found [here](https://hexdocs.pm/phoenix/testing_channels.html#the-channelcase).

```Elixir
# test/support/channel_case.ex
defmodule AppWeb.ChannelCase do
  use ExUnit.CaseTemplate

  using do
    quote do
      # Import conveniences for testing with channels
      import Phoenix.ChannelTest
      import AppWeb.ChannelCase

      # The default endpoint for testing
      @endpoint AppWeb.Endpoint
    end
  end

  setup tags do
    App.DataCase.setup_sandbox(tags)
    :ok
  end
end
```

and now, let's create a `subscription_case` module.

```Elixir
# test/support/subscription_case.ex
defmodule AppWeb.SubscriptionCase do
  use ExUnit.CaseTemplate

  alias Absinthe.Phoenix.SubscriptionTest
  alias Phoenix.ChannelTest
  alias Phoenix.Socket

  require Phoenix.ChannelTest

  using do
    quote do
      use AppWeb.ChannelCase
      use Absinthe.Phoenix.SubscriptionTest, schema: AppWeb.GraphQl.Schema

      import AppWeb.GraphQlHelpers

      # TODO: maybe make some functions here public; the ones being used outside of this module.

      defp create_socket(params \\ %{}) do
        {:ok, socket} = ChannelTest.connect(AppWeb.UserSocket, params)
        {:ok, socket} = SubscriptionTest.join_absinthe(socket)

        socket
      end

      defp subscribe(socket, query, options \\ []) do
        ref = push_doc(socket, query, options)
        assert_reply(ref, :ok, %{subscriptionId: subscription_id})

        subscription_id
      end

      defp close_socket(%Socket{} = socket) do
        # Socket will be closed automatically but we can do it manually
        # It's useful when we want to test, for example, users with different roles in a loop
        Process.unlink(socket.channel_pid)
        close(socket)
      end
    end
  end
end
```

Finally, let's write tests for subscriptions.

```Elixir
# test/app_web/graphql/subscriptions/blog/post_subscriptions_test.exs
defmodule AppWeb.GraphQl.Blog.PostSubscriptionsTest do
  use AppWeb.SubscriptionCase

  alias App.AccountsFixtures

  describe "post_created" do
    @create_post """
    mutation($input: CreatePostInput!) {
      createPost(input: $input) {
        id
        title
      }
    }
    """

    @post_created """
    subscription($userId: ID) {
      postCreated(userId: $userId) {
        id
        title
      }
    }
    """

    test "returns post when created" do
      socket = create_socket()
      # Subscribe to a post creation
      subscription_id = subscribe(socket, @post_created)
      user = AccountsFixtures.user_fixture()
      post_params = %{title: "valid title", user_id: user.id}

      # Trigger the subscription by creating a post
      gql_post(%{
        query: @create_post,
        variables: %{"input" => post_params}
      })

      # Check if something has been pushed to the given subscription
      assert_push "subscription:data", %{result: result, subscriptionId: ^subscription_id}

      # Pattern match the result
      assert %{
               data: %{
                 "postCreated" => %{
                   "id" => _,
                   "title" => title
                 }
               }
             } = result

      assert post_params.title == title

      # Ensure that no other pushes were sent
      refute_push "subscription:data", %{}
    end

    test "returns user when created with provided user id" do
      socket = create_socket()
      user_one = AccountsFixtures.user_fixture()
      subscription_id = subscribe(socket, @post_created, variables: %{"userId" => user_one.id})

      gql_post(%{
        query: @create_post,
        variables: %{"input" => %{title: "valid title", user_id: user_one.id}}
      })

      assert_push "subscription:data", %{result: result, subscriptionId: ^subscription_id}

      assert %{
               data: %{
                 "postCreated" => %{
                   "id" => _,
                   "title" => "valid title"
                 }
               }
             } = result

      # Ensure that no other pushes were sent
      refute_push "subscription:data", %{}
      user_two = AccountsFixtures.user_fixture()

      gql_post(%{
        query: @create_post,
        variables: %{"input" => %{title: "valid title", user_id: user_two.id}}
      })

      # Ensure that no pushes were sent
      refute_push "subscription:data", %{}
    end
  end
end
```
