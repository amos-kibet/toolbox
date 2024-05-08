## Media Queries

These are the rules to be applied to change the design of a website based on the screen's characteristics

### Applying media queries

One way is to load a different CSS stylesheet based on the rule (e.g. "if the resolution is less than 1280px wide, load the small_resolution.css file").

Example:

```HTML
<link rel="stylesheet" media="screen and (max-width: 1280px)" href="css/small_resolution.css">
```

The other common way is to write the media queries directly on the usual stylesheet.

Example:

```CSS
@media screen and (max-width: 1280px) {
    /* Write your CSS properties here */
}
```

### Possible rules

There are many rules for building media queries, but here are a few common ones:

- `color`
- `height`
- `width`
- `device-height`
- `device-width`
- `orientation`
- `media`
- `handheld`
- `print`
- `tv`
- `projector`
- `all`

The prefix `min-` or `max-` can be added in front of most of these rules.

These rules can also be combined using the following words:

- `only`
- `and`
- `not`

**Example**

```CSS
/* On screens with a maximum window width of 1280px */
@media screen and (max-width: 1280px) {
    /* Add your rules here */
}

/* On all screen types with a window width of between 1024px and 1280px */
@media all and (min-width: 1024px) and (max-width: 1280px) {
    /* Add your rules here */
}

/* On TVs */
@media tv {
    /* Add your rules here */
}

/* On all vertically oriented types of screens */
@media all and (orientation: portrait) {
    /* Add your rules here */
}
```

### Breakpoint settings for commonly used screen sizes

| Breakpoint setting | For device |
|--------|--------|
| max-width 320p px | Smartwatches |
| max-width 420 px | Smaller devices |
| max-width 600 px | Phones |
| min-width 600 px | Tablets and large phones |
| min-width 768 px | Tablets |
| min-width 992 px | Laptops and desktops |
| min-width 1200 px | Monitors, desktops |
