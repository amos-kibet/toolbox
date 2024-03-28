## Flexbox or Grid?

Flexbox sets base layout/size styles on the parent, and if you need further customization, you most likely need to do it on the child elements. That is to say, flexbox relies on intrinsic sizing of it child elements.

Grid, on the other hand, handles most of the layout/size styling on the parent, and the children are left with less customization to do. Grid sets the sizing on the parent, and the children then take up the available space.

You can mix flexbox and grid, but generally, you would want to use grid to set the outer/main layout of the page, and flexbox for setting displays for items inside grid cells
