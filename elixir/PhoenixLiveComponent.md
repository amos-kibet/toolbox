## Phoenix.LiveComponent ([Read more](https://hexdocs.pm/phoenix_live_view/Phoenix.LiveComponent.html))

- LiveComponents are a mechanism to compartmentalize **state**, **markup**, and **events** in LiveView.
- LiveComponents are defined by using `Phoenix.LiveComponent` and are used by calling `Phoenix.Component.live_component/1` in a parent LiveView.
- LiveComponents run inside the LiveView process but have their own state and life-cycle. This is why they are referred to as "stateful components".
- The smallest LiveComponent only needs to define a `render/1` function:

```elixir
defmodule HeroComponent do
  # If you generated an app with mix phx.new --live,
  # the line below would be: use MyAppWeb, :live_component
  use Phoenix.LiveComponent

  def render(assigns) do
    ~H"""
    <div class="hero"><%= @content %></div>
    """
  end
end
```

    A LiveComponent is rendered as:

```elixir
<.live_component module={HeroComponent} id="hero" content={@content} />
```

- You must always pass the `module` and `id` attributes. The `id` will be available as an assign and it must be used to uniquely identify the component. All other attributes will be available as assigns inside the LiveComponent.

### Life-cycle

\*\*Mount and update

- Stateful components are identified by the component module and their ID.
- We often tie the component ID to some application based ID:

```elixir
<.live_component module={UserComponent} id={@user.id} user={@user} />
```

- When `live_component/1` is called, `mount/1` is called once, when the component is first added to the page. `mount/1` receives the socket as argument. Then `update/2` is invoked with all of the assigns given to `live_component/1`. If `update/2` is not defined all assigns are simply merged into the socket. The assigns received as the first argument of the `update/2` callback will only include the new assigns passed from this function. Pre-existing assigns may be found in `socket.assigns`.
- After the component is updated, `render/1` is called with all assigns. On first render, we get:

```elixir
mount(socket) -> update(assigns, socket) -> render(assigns)
```

    On further rendering:

```elixir
update(assigns, socket) -> render(assigns)
```

- The given id is not automatically used as the DOM ID. If you want to set a DOM ID, it is your responsibility to do so when rendering:

```elixir
defmodule UserComponent do
  use Phoenix.LiveComponent

  def render(assigns) do
    ~H"""
    <div id={"user-\#{@id}"} class="user">
      <%= @user.name %>
    </div>
    """
  end
end
```

### Events

- Stateful components can also implement the `handle_event/3` callback that works exactly the same as in LiveView.
- For a client event to reach a component, the tag must be annotated with a `phx-target`.
- If you want to send the event to yourself, you can simply use the `@myself` assign, which is an internal unique reference to the component instance:

```elixir
<a href="#" phx-click="say_hello" phx-target={@myself}>
  Say hello!
</a>
```

- If you want to target another component, you can also pass an `ID` or a `class` selector to any element inside the targeted component. For example, if there is a UserComponent with the DOM ID of "user-13", using a query selector, we can send an event to it with:

```elixir
<a href="#" phx-click="say_hello" phx-target="#user-13">
  Say hello!
</a>
```

- Any valid query selector for `phx-target` is supported, provided that the matched nodes are children of a LiveView or LiveComponent, for example to send the close event to multiple components:

```elixir
<a href="#" phx-click="close" phx-target="#modal, #sidebar">
  Dismiss
</a>
```

### Preloading and update

- Stateful components also support an optional `preload/` callback, which is useful when multiple components of the same type are rendered on the page and you want to preload or augment their data in batches.
- Once a LiveView renders a LiveComponent, the optional `preload/1` and `update/2` callbacks are called before `render/1`.

```elixir
preload(list_of_assigns) -> mount(socket) -> update(assigns, socket) -> render(assigns)
```

    On subsequent renders, these callbacks will be invoked:

```elixir
preload(list_of_assigns) -> update(assigns, socket) -> render(assigns)
```

### Slots

- LiveComponents can also receive slots, in the same way as `Phoenix.Component`:

```elixir
<.live_component module={MyComponent} id={@data.id} >
  <div>Inner content here</div>
</.live_component>
```

- If the LiveComponent defines an `update/2`, be sure that the socket it returns includes the `:inner_block` assign it received.

### Live patches and live redirects

- A template rendered inside a component can use `<.link patch={...}>` and `<.link navigate={...}>`.
- Patches are always handled by the parent LiveView, as components do not provide `handle_params`.

### Managing state
