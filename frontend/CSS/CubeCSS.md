## CUBE CSS

- A CSS methodology geared towards simplicity, pragmatism, and consistency.
- It stands for **Composition Utility Block Exception**.
- It is a tool-agnostic CSS methodology.

### Concept

- Common styles styles (typography, colors, etc) are defined in the global level (HTML body, or `*` CSS selector)
- Then, the more specific styles are defined in the places where they need to be applied.

**Composition**

- Composition deals with how the page flows and how the different elements on the page connect and interact with each other.
- The composition layer handles the creation of flexible layouts that support as many content variants as possible; it acts as a skeleton for the other elements on a page. It does not care what content goes into the page, because it's sole responsibility is the layout of the content and not the content itself.
- This layer is responsible for:

  - Providing high-level flexible layouts
  - Determining how elements interact with each other
  - Creating consistent flow and rhythm

- It should not:
  - Provide visual treatment, such as colors or font styles
  - Provide decorative styles such as shadows and patterns

**Utilities**

- A utility class is a class that only does one thing. It is single-responsibility class.

```CSS

.btn-cta-secondary {
    background-color: red;
}

.margin-x-auto {
    margin-left: auto;
    margin-right: auto;
}
```

- This layer is resposnible for:

  - Applying a single CSS property, or a concise group of related properties to create re-usable helpers
  - Extending design tokens to maintain a single source of truth
  - Abstracting repeatability away from the CSS and applying it in the HTML instead.

- It should not:
  - Define a large group of unrelated CSS properties. For example, a utility that defines `color`, `font-size` and `padding` would make more sense to be a **block**.

**Block**

- A block is a skeletal component or organisational structure. To compare it to common user interface elements: it is a card element or a button element.
- A block is skeletal because by the time you get to the block-level in CUBE CSS, most of the work has already been done by the global CSS, composition and utility layers.
- This layer can:

  - Extend the work already done by the global CSS, composition and utility layers
  - Apply a collection of design tokens within a concise group
  - Create a namespace or specificity boost to control a specific context

- This layer should not:
  - Grow to anything larger than a handful of CSS rules (max 80-100 lines)
  - Solve more than one contextual problem. For example: styling a card and a button in one file

**Exceptions**

- These are little variations to a block.
- Usually, an exception is related to a state change. For example, you might have a "reversed" state or an "inactive" state. For these, CUBE CSS recommends using **data attributes**.
- **Why data attributes?** Because exceptions are often caused by outside influence, such as JavaScript, and so a mechanism that both CSS and JavaScript can use efficiently makes sense to be used, hence the use of data attributes.
- This layer should:

  - Provide a concise variation to a block
  - Use data attributes

- This layer should not:
  - Variate a block to the point where it isn't recognisable anymore. This is where a new block should be created
  - Use CSS classes

```CSS
/* Normal state button */

button {
  background-color: red;
  cursor: pointer;
}

/* Disabled variant of the button */

button[data-state="disabled"] {
 background-color: brown;
 cursor: not-allowed;
}
```
