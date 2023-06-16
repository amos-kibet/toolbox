## Ecto Associations ([Read more](https://elixirschool.com/en/lessons/ecto/associations))

- There are three types of associations you can define between Ecto schemas:
  - belongs to/has many association.
  - belongs to/has one association.
  - many to many association

### Belongs to/Has many Association

Take an example of `Movie` and `Character` schemas. A movie `has many` characters and a character `belongs to` a movie.

**Has Many**

The `has_many/3` macro **doesn't add anything to the database itself**. It just uses the foreign key on the associated schema, `characters`, to make a movie's associated characters available. This is what will allow you to call `movie.characters`.

```elixir
# has_many schema
defmodule MyApp.Movies.Movie do
    use Ecto.Schema
    schema "movies" do
        field :title, :string,
        has_many :characters, Character
    end
end
```

**Belongs to**

To declare that a character belongs to a movie, you need the `characters` table to have a `movie_id` column. This column will function as a foreign key. You can do so by creating it this way in the migration file:

```elixir
defmodule MyApp.Repo.Migrations.CreateCharacters do
    use Ecto.Migration
    def change do
        create table(:characters) do
            add :name, :string
            add :movie_id, references(:movies)
        end
    end
end
```

The schema as well needs to define the `belongs to` relationship between a character and its movie.

```elixir
defmodule MyApp.Characters.Character do
    use Ecto.Schema
    schema "characters" do
        field :name, :string,
        belongs_to :movie, Movie
    end
end
```

In addition to adding the foreign key, `movie_id`, to the `characters` schema, it also gives you the ability to access associated `movies` schema through `characters`. It uses the foreign key to make a character's associated movie available when you query for them. This is what will allow you to call `character.movie`.

### Belongs to/Has one Association

Let's say that a movie has one distributor, for example Netflix is the distributor of their original film “Bright”.

To enforce that a movie has exactly one distributor, you are going to add a unique index, apart from a foreign key to the `distributors` table and schema.

```elixir
# priv/repo/migrations/*_create_distributors.exs

defmodule MyApp.Repo.Migrations.CreateDistributors do
  use Ecto.Migration

  def change do
    create table(:distributors) do
      add :name, :string
      add :movie_id, references(:movies)
    end

    create unique_index(:distributors, [:movie_id])
  end
end
```

And the Distributor schema should use the `belongs_to/3` macro to allow you to call `distributor.movie` and look up a distributor’s associated movie using this foreign key.

```elixir
defmodule MyApp.Distributors.Distributor do
    use Ecto.Schema
    schema "ditributors" do
        field :name, :string,
        belongs_to :movies, Movie
    end
end
```

Next up, you will add the "has one" relationship to the `Movie` schema.

```elixir
defmodule MyApp.Movies.Movie do
    use Ecto.Schema
    schema "movies" do
        field :title, :string,
        has_many :characters, Character
        has_one :distributor, Distributor
    end
end
```

The `has_one/3` macro functions just like the `has_may/3` macro. It uses the associated schema’s foreign key to look up and expose the movie’s distributor. This will allow us to call `movie.distributor`.

### Many to many Association

Let’s say that a movie has many actors and that an actor can belong to more than one movie. You’ll build a join table that references both movies and actors to implement this relationship.

The actors migration will look like this:

```elixir
defmodule MyApp.Repo.Migrations.CreateActor do
    use Ecto.Migration

    def change do
        create table(:actors) do
            add :name, :string
        end
    end
end
```

Then, a join table will look like this:

```elixir
defmodule MyApp.Repo.Migrations.CreateMovieActors do
    use Ecto.Migration

    def change do
        create_table(:movies_actors) do
            add :movie_id, references(:movies)
            add :actor_id, references(:actors)
        end

        # add a unique index to enforce unique pairings of actors and movies
        create unique_index(:movie_actors, [:movie_id, :actor_id])
    end
end
```

Next up, you'll add the `many_to_many` macro to the `Movie` schema

```elixir
defmodule MyApp.Movies.Movie do
    use Ecto.Schema
    schema "movies" do
        field :title, :string,
        has_many :characters, Character
        has_one :distributor, Distributor
        many_to_many :actors, Actor, join_through: "movies_actors"
    end
end
```

Finally, you'll create the `Actor` schema with the same `many_to_many` macro.

```elixir
defmodule MyApp.Actors.Actor do
    use Ecto.Schema

    schema "actors" do
        field :name, :string
        many_to_many :movies, Movie, join_through: "movies_actors"
    end
end
```

## Saving Associated Data

### Belongs To

**Saving with Ecto.build_assoc/3**

`build_assoc/3` takes in three arguments:

- the struct of the record you want to save,
- the name of the association, and,
- any attributes you want to assign to the associated record you are saving.

Let's save a movie and an associated character. First, let's create & save a movie record:

```elixir
iex> alias MyApp.Movies.Movie
iex> alias MyApp.Characters.Character
iex> alias MyApp.Repo
iex> movie = %Movie{title: "Ready Player One", tagline: "Something about video games"}

%MyApp.Movies.Movie{
  __meta__: %Ecto.Schema.Metadata<:built, "movies">,
  actors: %Ecto.Association.NotLoaded<association :actors is not loaded>,
  characters: %Ecto.Association.NotLoaded<association :characters is not loaded>,
  distributor: %Ecto.Association.NotLoaded<association :distributor is not loaded>,
  id: nil,
  tagline: "Something about video games",
  title: "Ready Player One"
}

iex> movie = Repo.insert!(movie)
```

Now we’ll build our associated character and insert it into the database:

```elixir
iex> character = Ecto.build_assoc(movie, :characters, %{name: "Wade Watts"})
%MyApp.Characters.Character{
  __meta__: %Ecto.Schema.Metadata<:built, "characters">,
  id: nil,
  movie: %Ecto.Association.NotLoaded<association :movie is not loaded>,
  movie_id: 1,
  name: "Wade Watts"
}
iex> Repo.insert!(character)
%MyApp.Characters.Character{
  __meta__: %Ecto.Schema.Metadata<:loaded, "characters">,
  id: 1,
  movie: %Ecto.Association.NotLoaded<association :movie is not loaded>,
  movie_id: 1,
  name: "Wade Watts"
}
```

Notice that since the Movie schema’s `has_many/3` macro specifies that a movie has many `:characters`, the name of the association that you pass as a second argument to `build_assoc/3` is exactly that: `:characters`. you can see that you’ve created a character that has its `movie_id` properly set to the ID of the associated movie.

### Many to many

**Saving with Ecto.Changeset.put_assoc/4**

The `build_assoc/3` approach won’t work for our many-to-many relationship. That is because neither the movie nor actor tables contain a foreign key. Instead, you need to leverage Ecto Changesets and the `put_assoc/4` function.

Assuming you already have the movie record you created above, create an actor record:

```elixir
iex> alias MyApp.Actors.Actor
iex> actor = %Actor{name: "Tyler Sheridan"}
%MyApp.Actors.Actor{
  __meta__: %Ecto.Schema.Metadata<:built, "actors">,
  id: nil,
  movies: %Ecto.Association.NotLoaded<association :movies is not loaded>,
  name: "Tyler Sheridan"
}
iex> actor = Repo.insert!(actor)
%MyApp.Actors.Actor{
  __meta__: %Ecto.Schema.Metadata<:loaded, "actors">,
  id: 1,
  movies: %Ecto.Association.NotLoaded<association :movies is not loaded>,
  name: "Tyler Sheridan"
}
```

Now you’re ready to associate your movie to your actor via the join table.

First, note that in order to work with changesets, you need to make sure that your movie structure has preloaded associated data. You can preload data this way:

```elixir
iex> movie = Repo.preload(movie, [:distributor, :characters, :actors])
%MyApp.Movies.Movie{
 __meta__: #Ecto.Schema.Metadata<:loaded, "movies">,
  actors: [],
  characters: [
    %MyApp.Characters.Character{
      __meta__: #Ecto.Schema.Metadata<:loaded, "characters">,
      id: 1,
      movie: #Ecto.Association.NotLoaded<association :movie is not loaded>,
      movie_id: 1,
      name: "Wade Watts"
    }
  ],
  distributor: %MyApp.Distributors.Distributor{
    __meta__: #Ecto.Schema.Metadata<:loaded, "distributors">,
    id: 1,
    movie: #Ecto.Association.NotLoaded<association :movie is not loaded>,
    movie_id: 1,
    name: "Netflix"
  },
  id: 1,
  tagline: "Something about video game",
  title: "Ready Player One"
}
```

Next up, create a changeset for the movie record:

```elixir
iex> movie_changeset = Ecto.Changeset.change(movie)
%Ecto.Changeset<action: nil, changes: %{}, errors: [], data: %MyApp.Movies.Movie<>,
 valid?: true>
```

Next, pass the changeset as the first argument to `Ecto.Changeset.put_assoc/4`:

```elixir
iex> movie_actors_changeset = movie_changeset |> Ecto.Changeset.put_assoc(:actors, [actor])
%Ecto.Changeset<
  action: nil,
  changes: %{
    actors: [
      %Ecto.Changeset<action: :update, changes: %{}, errors: [],
       data: %MyApp.Actors.Actor<>, valid?: true>
    ]
  },
  errors: [],
  data: %MyApp.Movies.Movie<>,
  valid?: true
>
```

This gives you a new changeset that represents the following change:

- add the actors in this list of actors to the given movie record.

Lastly, update the given movie and actor records using your latest changeset:

```elixir
iex> Repo.update!(movie_actors_changeset)
%MyApp.Movies.Movie{
  __meta__: #Ecto.Schema.Metadata<:loaded, "movies">,
  actors: [
    %MyApp.Actors.Actor{
      __meta__: #Ecto.Schema.Metadata<:loaded, "actors">,
      id: 1,
      movies: #Ecto.Association.NotLoaded<association :movies is not loaded>,
      name: "Tyler Sheridan"
    }
  ],
  characters: [
    %MyApp.Characters.Character{
      __meta__: #Ecto.Schema.Metadata<:loaded, "characters">,
      id: 1,
      movie: #Ecto.Association.NotLoaded<association :movie is not loaded>,
      movie_id: 1,
      name: "Wade Watts"
    }
  ],
  distributor: %MyApp.Distributors.Distributor{
    __meta__: #Ecto.Schema.Metadata<:loaded, "distributors">,
    id: 1,
    movie: #Ecto.Association.NotLoaded<association :movie is not loaded>,
    movie_id: 1,
    name: "Netflix"
  },
  id: 1,
  tagline: "Something about video game",
  title: "Ready Player One"
}
```

You can use this same approach to create a brand new actor that is associated with the given movie. Instead of passing a saved actor struct into `put_assoc/4`, you simply pass in a map of attributes describing a new actor that we want to create:

```elixir
iex> changeset = movie_changeset |> Ecto.Changeset.put_assoc(:actors, [%{name: "Gary"}])
%Ecto.Changeset<
  action: nil,
  changes: %{
    actors: [
      %Ecto.Changeset<
        action: :insert,
        changes: %{name: "Gary"},
        errors: [],
        data: %MyApp.Actors.Actor<>,
        valid?: true
      >
    ]
  },
  errors: [],
  data: %MyApp.Movies.Movie<>,
  valid?: true
>
iex> Repo.update!(changeset)
%MyApp.Movies.Movie{
  __meta__: %Ecto.Schema.Metadata<:loaded, "movies">,
  actors: [
    %MyApp.Actors.Actor{
      __meta__: %Ecto.Schema.Metadata<:loaded, "actors">,
      id: 2,
      movies: %Ecto.Association.NotLoaded<association :movies is not loaded>,
      name: "Gary"
    }
  ],
  characters: [],
  distributor: nil,
  id: 1,
  tagline: "Something about video games",
  title: "Ready Player One"
}
```
