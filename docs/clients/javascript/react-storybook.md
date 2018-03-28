# Percy for Storybook for React
#### [percy-storybook on GitHub <i class="fa fa-github" aria-hidden="true"></i>](https://github.com/percy/percy-storybook)

[![Package Status](https://img.shields.io/npm/v/@percy-io/percy-storybook.svg)](https://www.npmjs.com/package/@percy-io/percy-storybook)
[![Build Status](https://travis-ci.org/percy/percy-storybook.svg?branch=master)](https://travis-ci.org/percy/percy-storybook)

This adds [Percy](https://percy.io) visual testing and review to your [**Storybook for React**](https://storybook.js.org/).  If you use React without Storybook see our [React](/docs/clients/javascript/react) page.

## Installation

[!INCLUDE /docs/clients/-do-setup-first]

1. Add as a dev dependency: `npm i --save-dev @percy-io/percy-storybook`
1. Open your **package.json**, and add a script: `"snapshot": "build-storybook && percy-storybook --widths=320,1280"`
1. Update your **.storybook/config.js**:

Adjust the existing line that imports `@storybook/react`:

```javascript
// On the line that imports from @storybook/react, add getStorybook and setAddon
import { configure, getStorybook, setAddon } from '@storybook/react';
```

Still in config.js, add the following before calling `configure` to configure your stories:

```javascript
import createPercyAddon from '@percy-io/percy-storybook';
const { percyAddon, serializeStories } = createPercyAddon();
setAddon(percyAddon);  
```

Finally, add the following at the end of your config.js file:

```javascript
// NOTE: if you're using the Storybook options addon, call serializeStories *BEFORE* the setOptions call
serializeStories(getStorybook);
```


## Usage

After you've setup the `PERCY_TOKEN` and `PERCY_PROJECT` environment variables, run:

```bash
$ npm run snapshot
```

This will run Storybook's build-storybook command to create a static site from your storybook, and will upload your stories to Percy to generate screenshots from them.  You'll want to add `storybook-static` to your .gitignore file if you run this locally.

## Freezing time and dynamic data

As you start to introduce visual testing into your workflow, you might find that the passing of time or generated data from tools like [faker](https://github.com/marak/Faker.js/) can cause visual diffs.

It's fairly easy to stabilize these diffs. Tools like faker often allow you to provide a [seed](https://github.com/marak/Faker.js/#setting-a-randomness-seed) to ensure that the same data is generated each time faker is run. Libraries like [MockDate](https://github.com/boblauer/MockDate) allow you to override the current date, eliminating variations due to the screenshots being taking at a different date and time. If you use Math.random, you can seed it with [seedrandom](https://github.com/davidbau/seedrandom).

Percy provides an `inPercy` function that you can use in your Storybook's config.js if you'd prefer that these adjustments only have an effect when running in Percy's rendering environment.  Add the [@percy-io/in-percy](https://www.npmjs.com/package/@percy-io/in-percy) package if you'd like to use inPercy.

You can see an example of how this type of stabilization can be done in this  [storybook/config.js](https://github.com/percy/percy-storybook/blob/master/integration-tests/storybook-for-react/storybook/config.js).


## Global Options

These options can be appended to the percy-storybook command in the script tag in your **package.json** file:

* **widths** - Specify multiple widths to screenshot your stories at.  eg. `--widths=320,1280`
* **minimum_height** - Specify the minimum height of story screenshots.  eg. `--minimum_height=300`
* **build_dir** - By default, percy-storybook looks for the static storybook in the `storybook-static` directory.  If you use build-storybook with a custom output directory, use build_dir instruct percy-storybook where to find it. eg. `--build_dir=my-static-storybook`
* **rtl** - Process all stories a second time in RTL.  eg. `--rtl`
* **rtl_regex** - Process stories with matching names a second time in RTL. eg. `--rtl_regex=unidirectional`
* **debug** - Provides additional debugging information. eg. `--debug`


## Per-Story Options

You can **override** options on a per-story basis by adding stories with `addWithPercyOptions` instead of `add`.  These options will trump the Global Options specified for the storybook.

`addWithPercyOptions` takes 3 parameters: the story name, the percyOptions, and the story function.  If you've been calling `add` to add the story previously, simply change it to `addWithPercyOptions` and insert percyOptions as the second parameter.

#### Available options:

* **widths** - An array of widths as integers for this story.
* **rtl** - A boolean value specifying whether this story should additionally be run in a RTL direction.
* **skip** - A boolean value specifying whether this story should be skipped (default: false).  You might want to use this for stories that use Storybook's knobs addon.

#### Examples:
Have a look at the storybook in [percy-storybook](https://github.com/percy/percy-storybook/tree/master/integration-tests/storybook-for-react) for an example of how to use `.addWithPercyOptions`. including ways to use it in conjunction with other add-ons.  Here's a simple example:

```javascript
storiesOf('My basic span', module)
  .addWithPercyOptions('with multiple widths', { widths: [222, 333], rtl: true }, () =>
    <span>This span renders in widths of 222px and 333px</span>,
  )
```

## RTL Direction Support

Percy supports taking screenshots of your stories a second time in the RTL direction.

* To process all stories in the RTL direction, use the `--rtl` option.  To process only a subset in RTL, use the `--rtl_regex` option and provide a regex that will match the names of the stories you want to capture in RTL.
* Stories you have selected for RTL processing will be processed twice.  Once normally, and then a second time with `[RTL]` appended to their name, and with a `direction=rtl` url param provided.
* It's up to the stories to process the direction url param and respond appropriately.  You can see a basic example of how this can be done in our [Direction Demo test story](https://github.com/percy/percy-storybook/blob/master/integration-tests/storybook-for-react/stories/index.js).

## Percy-specific CSS

If you use [Percy-specific CSS](/docs/learn/percy-specific-css) to override CSS in Percy, add the `@media only percy` code inside a style tag in your [preview-head.html](https://storybook.js.org/configurations/add-custom-head-tags/) file to ensure that it will be applied in the Percy rendering environment.

## GitHub integration

Percy **automatically integrates with GitHub pull requests**, so you can do visual reviews with each PR's code review.

![](/images/docs/docs-github.jpg)

See our [GitHub integration](/docs/integrations/github) docs for more info.

## Troubleshooting

### Debugging locally

If you're seeing different screenshots on Percy from what you see in Storybook locally, you'll find `build-storybook` helpful.

During installation, you added the "snapshots" script command `"build-storybook && percy-storybook --widths=320,1280"`.  The first half of that, `build-storybook`, is a built-in Storybook command to convert your storybook to a set of static assets in a folder called `storybook-static`.

Inside `storybook-static` you'll find an index.html file. Open index.html in your browser, and you'll see the exact storybook that Percy receives and renders screenshots from. Reviewing this locally as you make configuration changes can help with troubleshooting, and shorten the debugging cycle.  After you make changes, you can re-run build-storybook, and refresh index.html to see if you've resolved the issue.

If the built storybook differs from what you see when viewing the live storybook, it may be due to a configuration issue.  Perhaps you need to supply build-storybook with the -c option to provide a different config-dir, or supply the -s option to specify a different static folder.  i.e. `"build-storybook -c my_storybook -s my_static_folder && percy-storybook --widths=320,1280"`

### Storybook object not found on window.

If you see an error message `Storybook object not found on window.` and followed all of the installation instructions, then please add the debug flag to the script command in your **package.json file**:

```json
  "snapshot": "build-storybook && percy-storybook --width=320,1280 --debug"
```

Run `npm run snapshot` again, and email the output to [hello@percy.io](mailto:hello@percy.io).


## Contributing

1. Fork it ( https://github.com/percy/percy-storybook/fork )
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create a new pull request

[Throw a ★ on it!](https://github.com/percy/percy-storybook) :)
