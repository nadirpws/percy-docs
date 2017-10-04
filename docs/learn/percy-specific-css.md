# Percy specific CSS

Sometimes you'll want to apply CSS to a page only when Percy is taking a snapshot. You can do so
using the `@media only percy` CSS media query.

For example, you might have an element that renders differently each time and you want Percy to
ignore that element. You can use Percy specific styles to achieve this.

Let's say you want to apply a `hideInPercy` class to elements you want hidden in Percy only. Here's
how you can do that:

```css
@media only percy {
  .hideInPercy { visibility: hidden; }
}
```

And your page would have HTML like this:

```html
<div class='hideInPercy'>this will be hidden in Percy</div>
```
