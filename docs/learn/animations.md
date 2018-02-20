# Animations

A big challenge of visual regression testing is **avoiding false-positive visual diffs**. False-positives can be caused by many variables (including system and hardware differences between browser environments), but are commonly caused by page animations.

For example, take this awesome pure-CSS animation:

<iframe height="425" scrolling="no" src="//codepen.io/fotinakis/embed/xVXXpo/?height=425&amp;theme-id=0&amp;default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style="width: 100%;"></iframe>

If you captured a page with this animation multiple times, you would end up with noisy and false-positive visual diffs:

![](/images/examples/animation-diffs.jpg)

Though fun to look at, this doesn’t really help us identify visual regressions!

## Automatic animation freezing in Percy

Percy does a lot of work behind the scenes to make sure that pages are rendered consistently and deterministically. One of the things we do is freeze different kinds of animations that can cause false-positive visual diffs.

**Percy will automatically:**

*   Freeze animated GIFs on the first frame.
*   Freeze most CSS `animation` and `transition` styles.

![](/images/examples/animation-no-diffs.jpg)

Much better. **Percy automatically freezes CSS animations and animated GIFs.**

Other than animations, Percy does other server-side tricks to create a consistent rendering environment and avoid other kinds of false-positives (such as font rendering differences, sub-pixel antialiasing, width and height changes, etc.)

## jQuery animation handling

Percy does not by default execute JavaScript in our rendering pipeline, but JavaScript is likely enabled in the browser that runs your acceptance tests locally or on CI. You might be unknowingly running jQuery animations in tests simply by using them in your app.

In addition to causing false-positives if the DOM is captured in the middle of animation, jQuery animations can also have the adverse effect of slowing down your acceptance tests—assertions might wait for animations to complete before an element exists on the page. **Temporarily disabling jQuery animations might help speed up your acceptance tests, as well as will help Percy avoid capturing the DOM state while elements are still animating.**

You can set a jQuery config to disable animations:

```js
$.fx.off = true;
```

<div class="Alert Alert--warning">
  <strong>NOTE:</strong> You should only include this snippet in your testing environment!
</div>

As the [jQuery docs](https://api.jquery.com/jquery.fx.off/) say: "When this property is set to `true` all animation methods will immediately set elements to their final state when called, rather than displaying an effect."

## Freeze Highcharts animations

```js
Highcharts.setOptions({
  chart: {
    animation: false
  },
  plotOptions: {
    series: {
      animation: false
    }
  }
});
```

* https://api.highcharts.com/highcharts/chart.animation
* https://api.highcharts.com/highcharts/plotOptions.series.animation

## Velocity animation handling

If you are using the [Velocity](https://github.com/julianshapiro/velocity) animation accelerator, you can mock time when animating elements to disable the animation tweening entirely. This allows you to snapshot the end state of your animation. To do so, add the following to your Velocity config to disable animations:

```
$.Velocity.mock = true;
```

This will force all Velocity animations to run with 0ms duration and 0ms delay on the next animation tick. For more information on how this works, see the [Velocity documentation](http://velocityjs.org/#mock)
