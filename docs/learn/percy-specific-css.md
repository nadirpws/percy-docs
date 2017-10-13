# Percy specific CSS

Sometimes you'll want to apply CSS to a page only when it's rendered in Percy's rendering environment. This can be very helpful for hiding areas that produce false-positive visual diffs, or when you'd like more specific control over the state of UI elements like visualizations and animations.

You can do so using the `@media only percy` CSS media query. CSS that is nested under this media query will _only_ apply in Percy and will not affect your normal pages outside of Percy.

## Example

For example, you might have an element that renders differently each time and you want Percy to ignore that element. You can use Percy specific styles to achieve this.

Let's say you want to apply a `hide-in-percy` class to elements you want hidden in Percy. Here's how you can do that:

```css
@media only percy {
  .hide-in-percy {
    visibility: hidden;
  }
}
```

And your page would have HTML like this:

```html
<div class="hide-in-percy">an element we know we want to ignore in visual tests</div>
```

The class names don't have to be Percy specific, you can put any normal CSS selectors and rules that you want in the media query and they will only be applied when rendering in Percy.
