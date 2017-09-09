# Percy for WebdriverIO
#### [percy-webdriverio on GitHub <i class="fa fa-github" aria-hidden="true"></i>](https://github.com/percy/percy-webdriverio)

[![Package Status](https://img.shields.io/npm/v/@percy-io/percy-webdriverio.svg)](https://www.npmjs.com/package/@percy-io/percy-webdriverio)
[![Build Status](https://travis-ci.org/percy/percy-webdriverio.svg?branch=master)](https://travis-ci.org/percy/percy-webdriverio)

This adds [Percy](https://percy.io) visual testing and review to your [**WebdriverIO tests**](http://webdriver.io/).  

<div class="Alert Alert--warning">
  <b>Beta release.</b> Percy for WebdriverIO is in beta. It may change in backwards-incompatible ways until v1.0.0 is released.
</div>

## Getting Started

[!INCLUDE /docs/clients/-do-setup-first]

Install `@percy-io/percy-webdriverio` using `npm`:

```sh
npm install --save-dev @percy-io/percy-webdriverio
```

Or using `yarn`:

```sh
yarn add --dev @percy-io/percy-webdriverio
```

Next, update your `wdio.conf.js`:
```js
  // At the top of the file
  var percy = require('@percy-io/percy-webdriverio');

  // In the plugins section, add the percy-webdriverio plugin.
  plugins: {
    '@percy-io/percy-webdriverio': {}
  },

  // Before your tests run, instruct Percy on where to find and mount your assets,
  // and create the build that snapshots will be added to.
  onPrepare: function (config, capabilities) {
    var assetLoaders = [percy.assetLoader('filesystem', { buildDir: 'compiled-assets-dir', mountPath: '/assets' })];
    percy.createBuild(assetLoaders);
  },

  // After your tests have completed, finalize the build.
  onComplete: function(exitCode) {
    percy.finalizeBuild();
  },
```

The asset loader instructs Percy where to find your compiled assets in your filesystem, and where
to make them available. Configurable options:

* `buildDir`: absolute path to your compiled static assets (_not_ source assets).
* `mountPath`: path prefix to where your webserver serves the assets (ex: `/assets`)

Percy requires the asset loader to be configured correctly so it can quickly find your CSS, images, fonts and other assets required for taking screenshots. If it's misconfigured, you may see screenshots that are missing styles and images.

## Usage

You're now ready to start taking snapshots with `percySnapshot` in your tests:

```js
describe('Homepage', function() {
  it('looks very nice', function () {
    browser.url('http://localhost:3000');
    browser.percySnapshot('Homepage');
  });
});
```

You can provide several options to `percySnapshot`:

```js
browser.percySnapshot('name', { widths: [640, 800], enableJavaScript: true, minimumHeight: 400 });
```
* `widths`: An array of widths to take screenshots at.
* `enableJavaScript`: Boolean that instructs Percy whether to run javascript when rendering the page.
* `minimumHeight`: Adjust the minimum height of your snapshots. The default is 1024px.

You can take multiple snapshots in the same test:

```js
describe('Homepage', function() {
  it('button is clickable', function () {
    browser.url('http://localhost:3000');
    browser.percySnapshot('Homepage');

    browser.click('#myButton');
    browser.percySnapshot('Homepage button is clicked');
  });
});
```


## Ready to go
That's all there is to it.  Run `wdio`, and Percy will take your snapshots and prepare your visual
review for you.

Whether running in your CI service or locally, it will take the snapshots as long as you've supplied the `PERCY_TOKEN` and `PERCY_PROJECT` environment variables.

## Freezing time and dynamic data

As you start to introduce visual testing into your workflow, you might find that the passing of time or generated data from tools like [faker](https://github.com/marak/Faker.js/) can cause visual diffs.

It's fairly easy to stabilize these diffs. Tools like faker often allow you to provide a [seed](https://github.com/marak/Faker.js/#setting-a-randomness-seed) to ensure that the same data is generated each time faker is run. Libraries like [MockDate](https://github.com/boblauer/MockDate) allow you to override the current date, eliminating variations due to the screenshots being taking at a different date and time. If you use Math.random, you can seed it with [seedrandom](https://github.com/davidbau/seedrandom).


## GitHub integration

Percy **automatically integrates with GitHub PRs**, so you can do visual reviews with each PR's code review.

![](https://cloud.githubusercontent.com/assets/75300/13929974/13750b2c-ef5a-11e5-9a87-3ad3b335cc0d.png)

See our [GitHub integration](/docs/integrations/github) docs for more info.


## Contributing

1. Fork it ( https://github.com/percy/percy-webdriverio/fork )
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create a new pull request

[Throw a ★ on it!](https://github.com/percy/percy-webdriverio) :)
