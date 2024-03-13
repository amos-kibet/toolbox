## Padding

- It is the spacing between an element's contents and border.
- Unlike many CSS properties, padding is not inherited from parent elements.
- Padding is set by:
  - pixels - sets a padding that will never change, irregardless of the text size.
  - em - sets a padding that is relative to the element's font size. If a `h1` has a font size of `32px`, `1em` of padding would be `32px`
  - `rem` - sets a padding that is relative to the root element (HTML), which is, by default, `16px`. So, a padding of `2rem` on a `p` element is essentially `32px`.
  - a percentage - sets a padding as a percentage value, which is relative to the width of the containing element. **NOTE:** padding percentages are not relative to the width of the element itself; they are relative to the width of its containing element.

**Example**

A shorthand way of setting padding all around an element is following the `top`, `right`, `bottom`, and `left` sequence, as shown below:

```CSS
p {
    padding: 5px 30px 5px 30px;
}
```

An easy way to remember the above sequence is using the `TRBL` acronym, which sounds like "trouble!"

If your top/bottom values are the same, and your left/right values are the same, you can also just set two values: one for the top/bottom padding and another for left/right padding.

```CSS
p {
    padding: 5px 30px;
}
```

## Margin

- It is the CSS property that controls spacing between elements. It gives an element a breathing space against its neighbouring elements.
- Values used to set margin are similar to the ones used for setting padding.
- Additionally, it has an `auto` value that is useful for centering content.
- If you have two adjacent elements, where, say, the element on top has a bottom margin of 30px, and the other below it has a top margin of 20px, instead of the vertical margin between them being the sum (50px), it will be 30px, which is greater of the two margins. This is because **vertical margins collapse**.
- When an element touches its parent (no padding between them), its top and bottom margins will merge with the margins of the parent element. That is, if a parent has no margin on the top or bottom, but the child has some, like say, `10px`, the overal margin top and bottom of the parent will be `10px`. To avoid this scenario, you can add some padding to the parent, so that the child element won't be touching it anymore. Note that this does not happen when using flexbox or grid systems.

Setting this value centers the element according to the width of the containing element, which must have a defined width for the `auto` value to apply properly.

Example:

```CSS
p {
  width: 300px;
  margin: 30px auto;
}
```

## Width & Height

- Elements (p, h1, etc.) have default width and height, which is just enough to accomodate its content (text content).
- You can also explicitly set the width and height of elements by using the `width` and `height` CSS properties.
- Also, border and padding add up to the element's width and height. If you don't want this behavior, you can set `box-sizing: border-box;` property on the element, which means the total width/height of the element will be the ones you specify under `width/height` properties. This property declares that an element's padding and borders should be included in the width of the element.

## Images

To render Retina-friendly and bandwidth-friendly images, you can use the `srcset` attribute on `img` element. You specify different pixel densities, and the user agent (browser) will determine which pixel density to use to render the image, depending on set user preferences or bandwidth constraints. See example below;

```HTML
<img
  src="images/image_1.jpg"
  srcset="images/image_1.jpg 1x, images/image_1.jpg 2x, images/image_1.jpg 3x"
  alt="Considerate image"
/>
```

You can also use `w` (width) instead of `x`, which will require the use of `sizes` `img` attribute.

```HTML
<img
  srcset="images/image_1-480w.jpg 480w, images/image_1-800w.jpg 800w"
  sizes="(max-width: 600px) 480px, 800px"
  src="images/image_1-800w.jpg"
  alt="Considerate image"
/>
```

## Aligning items centrally

- `justify-content: center` - makes the content centered on the main axis (**vertical**)
- `align-items: center` - makes the content centered on the cross axis (**horizontal**)

## CSS Variables

CSS variables are typically defined in the root HTML element, where the specificity is high.

Example:

```CSS
/* Definition */

:root {
  --secondary-color: lightblue;
}

/* Usage */

.cta-secondary-btn {
  background-color: var(--secondary-color, lightblue);
}

.close-btn{
  background-color: var(--secondary-color);
}
```

`:root` is typically the `html` element, but with more specificity than the `html` element.

When using the variable, you use the `var()` function, which takes the variable name as the first argument, and a fallback value, as an optional second argument.

Although not recommended, you can overwrite the variable's value. Example:

```CSS
h2 {
  --secondary-color: green;
}
```
