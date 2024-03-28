## CSS Best Practices

- Add block of comments between logical sections in your stylesheet. It helps in quickly locating the sections in the stylesheet.
- Create logical sections in your stylesheet. It is a good idea to have all of the common styling first in the stylesheet. This means all of the styles which will generally apply unless you do something special with that element.
  - You will typically have rules set up for: `body`, `p`, `h1`, `h2`, `h3`, `h4`, `h5`, `ul`, `ol`, links, etc.
  - After this section, you can define utility classes for common properties that can be reused.
  - Then we can have everything that is used sitewide. That might be things like basic page layout, header, navigation styling, and so on.
  - Finally, we will include CSS for specific things.
- You can define the global styles in the `html` element:

```CSS
/* 1. Base */
html {
  background-color: #5f45bb;
  background-image: linear-gradient(to bottom right, #180cac, #d054e4);
  color: #fff;
  font-family: "Quicksand", sans-serif;
  font-size: 16px;
  -moz-osx-font-smoothing: grayscale;
  -webkit-font-smoothing: antialiased;
  line-height: 1.5;
  min-height: 100vh;
  min-width: 300px;
  overflow-x: hidden;
  text-shadow: 0 3px 5px rgba(0, 0, 0, 0.1);
}
```

### Other best practices

Avoid using `!important` to enforce styles. Rather, make use of proper cascade rules to enforce the desired styles.

Use hyphens as class/id name delimiters. Example:

```CSS
.video-id {}

#audio-id {}
```

Limit shorthand declaration usage to instances where you must explicitly set all available values.

Here is an example where use of shorthand properties makes sense:

```CSS
/* Not recommended */
padding-bottom: 2em;
padding-left: 1em;
padding-right: 1em;
padding-top: 0;

/* Recommended */
padding: 0 1em 2em 1em;
```

and here is an example where use of shorthand properties causes unintended side effects:

```CSS
p {
  background-color: red;
  background: url(images/bg.gif) no-repeat left top;
}
```

The above CSS rule will not set the background color to `red` but to the default value for `background-color`, which is `transparent`. The shorthand `background` property caused the unintended side effect.

Omit unit specification after "0" values, unless required. Example:

```CSS
/* Not recommended */
margin: 0px;

/* Recommended */
margin: 0;
```

Use shorform for Hexadecimal values where possible. Example:

```CSS
/* Not recommended */

background-color: #ffffff;
color: #eebbcc;

/* Recommended */

background-color: #fff;
color: #ebc;
```

Separate selectors and declarations by new lines. Example:

```CSS
/* Not recommended */

a:focus, a:active {
  postion: relative; top: 1px;
}

/* Recommended */

a:focus,
a:active {
  position: relative;
  top: 1px;
}
```

Always separate rules by blank lines. Example:

```CSS
/* Not recommended */

a {
  color: whitesmoke;
}
a:hover {
  color: white;
}

/* Recommended */

a {
  color: whitesmoke;
}

a:hover {
  color: white;
}
```

Use single (`''`) rather than double (`""`) quotation marks for attribute selectors and property values. Also, do not use quotation marks in URI values (`url()`).

Example:

```CSS
/* Not recommended */

@import url("https://www.google.com/css/main.css");

html {
  font-family: "open sans", sans-serif;
}

/* Recommended */

@import url(https://www.google.com/css/main.css);

html {
  font-family: 'open sans', sans-serif;
}
```

Media queries are defined close to their default rules; that is, if you have a CSS rule that sets content width to, say, 90% on mobile screens (default), your media queries that set other values for other screens should be defined below this rule.
Some conventions put all media queries near the end of the CSS file, but it makes it hard to know whether certain base rules have media queries set or not.

Define CSS reset before you start styling your pages. This ensures that default browser styles are removed, and that you start styling without any initial defaults set by individual browsers. Most CSS frameworks have built-in reset rules, so you won't have to do it.
Below are links to sites that hae CSS reset styles you can use:

- https://piccalil.li/blog/a-more-modern-css-reset/
- https://meyerweb.com/eric/tools/css/reset/
- https://necolas.github.io/normalize.css/

Take note of CSS specificity. Example

```CSS
/* Specificity = 0-1-1-0 */
 .container p {
  color: red;
 }

/* Specificity = 0-0-1-0 */

 .text {
  color: blue;
 }
```

In the above example, the color will be red, since the rule has higher specificity than the `.text` rule.

Avoid using ID selectors. They are considered an anti-pattern as they introduce a high specificity level, and they are not reusable.

Avoid setting width & height on block-level elements, if you want them to take up `100%` of the available space. By default, these elements take up all of the available space, so, setting it manually is unnecessary.

Avoid using display flex, unless very necessary.

Name your CSS classes in a manner that makes them easy to reuse. Instead of this class:

```CSS
.btn-1 {
  background-color: red;
}
```

which can only be applied to the first button in your markup, use a class name that can be reused, like `bg-accent`. This utility class can now be appplied on your button, and other places, because the name is a general one. This ensures that you write less CSS which is also easy to maintain.

Avoid using `absolute` positioning, unless it's very necessary. You can achieve the desired effect with other better approaches like `transform`, or `relative` positioning and other settings instead. `absolute` positioning will work, but also introduces other side-effects that you'll have to fix, leading you to write a lot of unnecessary and hard-to-maintain CSS code.

Avoid negative margins, unless very necessary. You can get away with other CSS rules instead.
