marp: true

## Writing Better CSS

### Key Points

- Apply CSS reset. More on CSS reset [here](#css-reset)
- Write common CSS styles in the global-level HTML elements, i.e. `*`, `body` or `html` CSS selectors.
- Then, the more specific styles are set in the places where they need to be applied., e.g., if you want to italicize a specific `h1` element, you should target that element in a CSS rule set specifically for that element.
- Add block of comments between logical sections in your stylesheet. It helps in quickly locating the sections in the stylesheet. These logical sections can be: `base/general styles`, `typography`, `utilities`, `header`, `navigation`, `main`, `footer`, etc.
- Consistency - follow a pattern, be it in the naming of classes & IDs, or ordering your CSS rules. This results in easy-to-read/extend CSS code. A combination of use of comments, setting common CSS properties in the global level, and use of constistent naming convention for classes & ids forms a pattern, and the resulting CSS code is easy to maintain and scale.

### CSS Reset

Browsers have varying default values for properties like margins, line heights, font sizes for different headings (h1, h2, etc), list items styles, etc.
This makes it hard to guarantee consistent UIs across different browsers.

CSS reset is a set of CSS rules that remove these default browser styles, so that you, the developer, can start implementing your design from scratch without worrying about how your site will look like on different browsers.
There are a couple of CSS reset styles available on the internet. Below are some examples:

- https://piccalil.li/blog/a-more-modern-css-reset/
- https://meyerweb.com/eric/tools/css/reset/
- https://necolas.github.io/normalize.css/

Luckily, since we're using Tailwind CSS, this has already been done for us by Tailwind's set of base styles [(preflight)](https://tailwindcss.com/docs/preflight).

When using Tailwind you can use a `h1`, or a `h2--h6` elements anywhere you would use a `p` element, without worrying about the font-size. Properties like font size, line heights are left for you to style according to the constraints of your design.

Also, ordered/unordered lists are unstyled by default. This seems to put you in a position where you have to write a lot of CSS rules for default styling, but the upside is that it guarantees that the rules you set will be consistent across browsers.

More on Tailwind's CSS reset [here](https://tailwindcss.com/docs/preflight).

### CSS Methodologies

There are [a number](https://valoremreply.com/post/5-css-methodologies/) of CSS methodologies that can be adopted in order to write maintanable & scalable CSS code. I'll briefly touch on **BEM** & **CUBE CSS** methodologies.

**[CUBE CSS](CUBECSS.md)**

**[BEM](BEMCSS.md)**

### [Flexbox or Grid?](Displays.md)

### Other concepts

**1. Responsive web design**

There are tons of screens and devices with different heights and widths, so it is hard to create an exact breakpoint for each device.

To keep things simple you could target five groups, as shown below:

| Screen                                                          | Media query                                        |
| --------------------------------------------------------------- | -------------------------------------------------- |
| Extra small devices (phones, 600px and down)                    | `@media only screen and (max-width: 600px) {...}`  |
| Small devices (portrait tablets and large phones, 600px and up) | `@media only screen and (min-width: 600px) {...}`  |
| Medium devices (landscape tablets, 768px and up)                | `@media only screen and (min-width: 768px) {...}`  |
| Large devices (laptops/desktops, 992px and up)                  | `@media only screen and (min-width: 992px) {...}`  |
| Extra large devices (large laptops and desktops, 1200px and up) | `@media only screen and (min-width: 1200px) {...}` |

More on media queries [here](MediaQueries.md).

**2. Questions**

**3. The end - thank you!**
