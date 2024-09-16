## Absinthe Schema Organization

Absinthe schemas are compiled, meaning that their types are collected, references are resolved, and the overall structure is verified against a set of rules during Elixir's module compilation process.

Absinthe provides two simple tools to help in code organization: `import_types` and `import_fields`.

### Importing Types

When you extract types away from your schema to a separate module, that module is essentially called a type module since it only contains a set of types for your schema.

Unlike schema modules, these modules `use Absinthe.Schema.Notation`, which gives them acces to the general type definition macros (like `object`), but without the top-level compilation and verification mechanism that only schemas need.

Once we have types out of the schema, we can now use `import_types` macro inside our schema, and point it at the type module.

```Elixir
import_types __MODULE__.MenuTypes
```

Note that we're using the `__MODULE__` shorthand, to denote the current module. This is because type modules typically reside beside the schema module. If there are any nesting, probably as a result of namespacing type modules, or for any other reason, make sure to use the full module name instead.

`import_types` macro should only be used from the schema module.

### Importing fields

You can also free up your Absinthe schema by moving your query fields to the fields module, then pointing to them using the `import_fields` macro.

```Elixir
query do
    import_fields :menu_queries
end
```
