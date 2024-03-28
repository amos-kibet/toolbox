## CSS Grid

- It's a 2-dimensional grid-based layout system.
- To get started, you have to build a container element as a grid with `display: grid`, set the column and row sizes with `grid-template-columns` and `grid-template-rows`, and then place its child elements into the grid with `grid-column` and `grid-row`.

### Terminologies

- **Grid container:** Parent element, o which `display: grid` is applied.
- **Grid item:** The children (i.e. direct descendants) of the grid container.
- **Grid line:** The dividing lines that make up the structure of the grid. They can be either vertical (column grid lines) or horizontal (row grid lines) and reside on either side of a row or column.
- **Grid cell:** The space between two adjacent row and two adjacent column grid lines.
- **Grid track:** The space between two adjacent grid lines. You can think of them as the columns or rows of the grid.
- **Grid area:** The total space surrounding by four grid lines.
- **Gap:** It's the space between each column/row. You can adjust the gap size by using one of the following properties:
  - `column-gap`
  - `row-gap`
  - `gap`

### Grid lines

![CSS Grid lines](https://www.w3schools.com/css/grid_lines.png)

- Refer to line numbers when placing a grid item in a grid container.

- Example: Place a grid item at column 1, and let it end on column 3:

```CSS
.item1 {
    grid-column-start: 1;
    grid-column-end: 3;
}
```

Refer [here](https://www.w3schools.com/css/css_grid.asp#:~:text=All%20CSS%20Grid%20Properties) for all CSS Grid properties

### Grid template areas

**Steps**

- Set the number of columns and rows, and their measurements, in grid-template (or separately in grid-template-columns and grid-template-rows).

- Define a template using letters, words, or numbers of your choice in grid-template-areas, putting a space between each and putting each row in a set of quotation marks.

- Map elements to these letters, words, or numbers of your choice by defining grid-area on each element.

- Firstly, in the accompanying CSS, set a grid-template with how many columns and rows you want and their measurements

Take this HTML template

```HTML
<div class="container">
    <header>Header</header>
    <section id="a">Section A</section>
    <section id="b">Section B</section>
    <footer>Footer</footer>
</div>
```

Here's how we use grid on the template:

```CSS
.container {
  display: grid;
  grid-template: repeat(3, 1fr) / repeat(3, 1fr);
}
```

Next, create a layout using `grid-template-areas`, which will contain a coded representation of where your elements fit into a grid, **putting each row in a set of quotation marks**.

```CSS
.container {
  display: grid;
  grid-template: repeat(3, 1fr) / repeat(3, 1fr);
  grid-template-areas:
    "h h h h"
    "a a b b"
    "f f f f"
  ;
}
```

Next, assign each letter in your grid area template to an element within your grid using the `grid-area` property.

```CSS
header {
  grid-area: h;
}

footer {
  grid-area: f;
}
```

### Auto-fit and minmax

Look at the line of code below. From left to right, it reads:

> Repeat as many columns that will fit on a screen. Each column should be a minimum of 100px wide and a maximum of 1fr wide.

```CSS
grid-template-columns: repeat(auto-fit, minmax(100px, 1fr));
```
