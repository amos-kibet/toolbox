## Phoenix.Component ([Read more](https://hexdocs.pm/phoenix_live_view/Phoenix.Component.html))

- Defines reusable function components with HEEx templates.
- A function component is any function that receives an assigns map as an argument and returns a rendered struct built with the `~H` sigil:

```elixir
defmodule MyComponent do
    use Phoenix.Component

    def greet(assigns) do
        ~H"""
        <p>Hello, <%= @name %>!</p>
        """
    end
end
```

    When invoked within a `~H` sigil or `HEEx` teemplate file:

```elixir
<MyComponent.greet name="Jane" />
```

    The following `HTML` is rendered:

```html
<p>Hello, Jane!</p>
```

### Attributes ([Read more](https://hexdocs.pm/phoenix_live_view/Phoenix.Component.html#attr/3))

- `Phoenix.Component` provides the `attr/3` macro to declare what attributes the proceeding function component expects to receive when invoked.

```elixir
attr :name, :string, required: true
attr :age, :integer, required: true
attr :rest, :global
def celebrate(assigns) do
    ~H"""
    <p>Happy birthday <%= @name %>! You are <%= @age %> years old.</p>
    """
end
```

```elixir
<.celebrate name={"Genevieve"} age={34} class="bg-green-300" phx-click="close">
```

```html
<p class="bg-green-300" phx-click="close">
  Happy birthday Genevieve! You are 34 years old.
</p>
```

- If an attribute is not required, you must explicitly declare `default: nil` or assign a value programmatically with the `assign_new/3` function.

### Global Attributes

- Global attributes are attributes common to all HTML elements;.`class`, `id`, `hidden`, `data-*`, `autofocus`, [etc](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes).
- You can declare a `:global` attribute, which enables your functional component to accept any number of global HTML attributes, and Phoenix bindings; `phx-click`, `phx-click-away`, `phx-value-*`, `phx-hook`, [etc](https://hexdocs.pm/phoenix_live_view/bindings.html).
- **Included globals** - You may specify which attributes are included, in addition to the known globals with the `:include` option:

```elixir
# <.button form="my-form" />
attr :rest, :global, include: ~w(form)
slot :inner_block
def button(assigns) do
~H"""
<button {@rest}><%= render_slot(@inner_block %></button>
"""
end
```

- **Custom global attribute prefixes** - Like the default attributes common to all HTML elements,any number of attributes that start with a global prefix will be accepted by function components invoked by the current module. By default, the following prefixes are supported: `phx-`, `aria-`, and `data-`. For example, to support the `x-` prefix used by Alpine.js, you can pass the `:global_prefixes` option to `use Phoenix.Component`:

```elixir
use Phoenix.Component, global_prefixes: ~w(x-)
```

    In you Phoenix application, this is typically done in your lib/my_app_web.ex file, inside the def html definition:

```elixir
def html do
    quote do
        use Phoenix.Component, global_prefixes: ~w(x-)
    end
end
```

### Slots ([Read more](https://hexdocs.pm/phoenix_live_view/Phoenix.Component.html#slot/3))

- In addition to attributes, function components can accept blocks of HEEx content, referred to as `slots`.
- Slots enable further customization of the rendered HTML, as the caller can pass the function component HEEx content they want the component to render.

```elixir
slot :inner_block, required: true
def button(assigns) do
    ~H"""
    <button>
        <%= render_slot(@inner_block) %>
    </button>
    """
end
```

    You can invoke this function component like so:

```elixir
<.button>
    This renders <strong>inside</strong> the button!
</.button>
```

    Which renders the following HTML:

```elixir
<button>
    This renders <strong>inside</strong> the button!
</button>
```

- **The default slot** - The example above uses the default slot, accessible as `inner_block`. If the values rendered in the slot need to be dynamic, you can pass a second value back to the HEEx content by calling `render_slot/2`:

```elixir
slot :inner_block, required: true
attr :entries, :list, default: []
def unordered_list(assigns) do
    ~H"""
    <ul>
        <%= for entry <- @entries do %>
            <li><%= render_slot(@inner_block, entry) %></li>
        <% end %>
    </ul>
    """
end
```

    When invoking the function component, you can use the special attribute (:let) to take the value that the function component passes back and bind it to a variable:

```elixir
<.unordered_list :let={fruit} entries={~w(apples bananas cherries)}>
 I like <%= fruit %>!
</.unordered_list>
```

    Rendering the following HTML:

```HTML
<ul>
  <li>I like apples!</li>
  <li>I like bananas!</li>
  <li>I like cherries!</li>
</ul>
```

- **Named slots** - In addition to the default slot, function components can accept multiple, named slots of HEEx content. For example, imagine you want to create a modal that has a header, body, and footer:

```elixir
slot :header
slot :inner_block,required: true
slot :footer, required: true

def modal(assigns) do
  ~H"""
  <div class="modal">
    <div class="modal-header">
      <%= render_slot(@header) || "Modal" %>
    </div>
    <div class="modal-body">
      <%= render_slot(@inner_block) %>
    </div>
    <div class="modal-footer">
      <%= render_slot(@footer) %>
    </div>
  </div>
  """
end
```

    You can invoke this function component using the named slot HEEx syntax:

```elixir
<.modal>
  This is the body, everything not in a named slot is rendered in the default slot.
  <:footer>
    This is the bottom of the modal.
  </:footer>
</.modal>
```

    Rendering the following HTML:

```HTML
<div class="modal">
  <div class="modal-header">
    Modal.
  </div>
  <div class="modal-body">
    This is the body, everything not in a named slot is rendered in the default slot.
  </div>
  <div class="modal-footer">
    This is the bottom of the modal.
  </div>
</div>
```

- **Slot attributes** - Unlike the default slot, it is possible to pass a named slot multiple pieces of HEEx content. Named slots can also accept attributes, defined by passing a block to the `slot/3` macro. If multiple pieces of content are passed, `render_slot/2` will merge and render all the values. Below is a table component illustrating multiple named slots with attributes:

```elixir
slot :column, doc: "Columns with column labels" do
  attr :label, :string, required: true, doc: "Column label"
end

attr :rows, :list, default: []

def table(assigns) do
  ~H"""
  <table>
    <tr>
      <%= for col <- @column do %>
        <th><%= col.label %></th>
      <% end %>
    </tr>
    <%= for row <- @rows do %>
      <tr>
        <%= for col <- @column do %>
          <td><%= render_slot(col, row) %></td>
        <% end %>
      </tr>
    <% end %>
  </table>
  """
end
```

    You can invoke this function component like so:

```elixir
<.table rows={[%{name: "Jane", age: "34"}, %{name: "Bob", age: "51"}]}>
  <:column :let={user} label="Name">
    <%= user.name %>
  </:column>
  <:column :let={user} label="Age">
    <%= user.age %>
  </:column>
</.table>
```

    Rendering the following HTML:

```HTML
<table>
  <tr>
    <th>Name</th>
    <th>Age</th>
  </tr>
  <tr>
    <td>Jane</td>
    <td>34</td>
  </tr>
  <tr>
    <td>Bob</td>
    <td>51</td>
  </tr>
</table>
```

### Embedding external template files ([Read more](https://hexdocs.pm/phoenix_live_view/Phoenix.Component.html#embed_templates/1))

- The `embed_template/1` macro can be used to embed `.html.heex` files as function components.
- The directory path is based on the current module (`__DIR__`), and a wildcard pattern may be used to select all files within a directory tree. For example, imagine a ddirectory listing:

```bash
├── components.ex
├── cards
│   ├── pricing_card.html.heex
│   └── features_card.html.heex
```

    Then you can embed the page templates in your "components.ex" module and call them like any other function component:

```elixir
defmodule MyAppWeb.Components do
    use Phoenix.Component

    embed_templates "cards/*"

    def landing_hero(assigns) do
        ~H"""
        <.pricing_card />
        <.features_card />
        """
    end
end
```
