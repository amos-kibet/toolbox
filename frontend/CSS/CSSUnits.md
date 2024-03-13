## CSS Units

- Font sizes, use `rem`s
- `rem` is different from `em` in that it's relative to the root document, the HTML, which is usually 16px.
- For width, percentages is better, coupled with `max-width` of a fixed value.
- Don't set the height, but if you must, use `min-height` instead of `height`.
- For paddings or margin, mostly use `em` or `rem`.

### `em` vs `rem`

- `em` is relative to the parent element's font size. For example, you can use `em` when you need to set a size relative to its parent, such as for a button that needs to grow or shrink within its container.
- `rem` is relative to the root (HTML) font size. You can use `rem` for font sizes to make it more accessible to users who have changed their browsers' default font size.
- Use `rem` for consistency and predictability,and `em` if you want to scale your page on a modular level. Really, you would most often want to use `rem` generally, and `em` for specific requirements like growing or shrinking a button's font size relative to it's container.
- `rem` is mostly used for global values such as font-sizes, margins, and padding.
- `em` is more suited for values that are specific to aparticular element and its children, like a navbar, header, footer, etc.
