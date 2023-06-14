## ExMachina

- Add it to the list of deps in `mix.exs` and isntall it.
- Next, be sure to start the application in your `test/test_helpers.exs` before `ExUnit.start`:

```elixir
{:ok, _} = Application.ensure_all_stsrted(:ex_machina)
```

- Define factories:

  ```elixir
  defmodule MyApp.Factory do
      # with Ecto
      use ExMachina.Ecto, repo: MyApp.Repo

      # without Ecto
      use ExMachina

      def user_factory do
          %MyApp.User{
              name: "John Doe",
              email: sequence(:email, &"email-#{&1}@example.com"),
              role: sequence(:role, ["admin", "user", "other"])
          }
      end
  end
  ```

- `build` returns an unsaved comment, and can be used with non-Ecto projects -`insert` returns an inserted comment, and can ony be used with `ExMachina.Ecto`
- **NOTE:** `build` and `insert` perform any existing Ecto associations, like `belongs_to`, `has_many` etc.
- You can use `insert_list` to insert a list of items:

  ```elixir
  users = insert_list(3, :user, %{email: "johndoe@example.com"})
  ```

- `params_for` returns a plain map without any Ecto-specific attributes.

### Best practices - Brooklin Myers

- Explicitly pass intentional data. This is the data that is neccessary for the specific test to pass, e.g. user_name, or email:

  ```elixir
  user = build(:user, user_name: "John Doe")
  ``
  ```

- Incidental data (data that is not explicitly required for the specific test) can be randomized. When testing against username, incidental data can be email, password, etc, while username is intentional adn should be passed as in the example above. `Faker` library can be used in this case to generate random data, for example:

  ```elixir
  def user_factory do
      email: Faker.Internet.email(),
      birthday: Faker.Date.between(~D[1980-01-01], Date.utc_today())
  end
  ```

- Avoid nesting resources. Instead of creating factories this way:

      ```elixir
      def user_factory do
          user_name: Faker.Person.name(),
          article: build(:article)
      end
      ```

  you should avoid creating related resources in factories, and instead explicitly create them in the specific test files. This adds the flexibility of defining intentional (required) fields, and randomizing the rest:

      ```elixir
      # some test file dot exs
      article = build(:article, title: "Example Title")
      user = build(:user, user_name: "John Doe", articles: article)
      ```

- Centralize data creation in your factories. These could be emails, passwords, usernames. Centralizing them in the factory module provides a convenient interface, and in case you need to, you would change it in one place, as opposed to when scattered all over your specific test files:

  ```elixir
  def email do
      sequence(:email, &"email-#{&1}@example.com")
      # or
      Faker.Internet.email()
  end
  def role do
      sequence(:role, ["admin", "user", "other"])
  end

  def user_factory do
      %MyApp.User{
          name: "John Doe",
          email: email(),
          role: role()
      }
  end
  ```
