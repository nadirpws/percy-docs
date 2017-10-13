# Percy for React

#### [react-percy on GitHub <i class="fa fa-github" aria-hidden="true"></i>](https://github.com/percy/react-percy)

[![Package Status](https://img.shields.io/npm/v/@percy/react.svg)](https://www.npmjs.com/package/@percy/react)
[![Build Status](https://travis-ci.org/percy/react-percy.svg?branch=master)](https://travis-ci.org/percy/react-percy)

Visual regression testing for React components with Percy.

If you use Storybook for React, please see [Percy for Storybook](/docs/clients/javascript/react-storybook).<br>
If you use react-rails, please see the [Percy for Rails](/docs/clients/ruby/capybara-rails).

<div class="Alert Alert--warning">

**Beta release.** Percy for React is in beta. It may change in backwards-incompatible ways until v1.0.0 is released.

Please submit any feedback or issues on the [GitHub issue tracker](https://github.com/percy/react-percy/issues).

</div>

## Setup

[!INCLUDE /docs/clients/-do-setup-first]

Install `@percy/react` using `npm`:

```sh
npm install --save-dev @percy/react
```

Or using `yarn`:

```sh
yarn add --dev @percy/react
```


Add the following section to your `package.json`:

```json
{
  "scripts": {
    "percy": "react-percy"
  }
}
```

## Usage

Let's get started by creating a snapshot for a React component in your project. Suppose you have a `Button` component in a file called `Button.js`:

```javascript
import React from 'react';

export default class Button extends React.Component {
  render() {
    return <button>{this.props.children}</button>;
  }
}
```

Create a new file `Button.percy.js`. This will contain our Percy snapshot:

```javascript
import React from 'react';
import Button from './Button';

percySnapshot('Button', () => {
  return <Button>My Button</Button>
});
```

That's it!

Now, whenever CI runs, a snapshot of the `Button` component will be uploaded to Percy for visual regression testing!

[!INCLUDE /docs/clients/-do-setup-first]

## ESLint Configuration

If your project uses ESLint, you will want to whitelist the `react-percy` globals to avoid "percySnapshot is not defined" errors. The easiest way to do this is using `eslint-plugin-react-percy`.

Install `eslint-plugin-react-percy` using `npm`:

```sh
npm install --save-dev eslint-plugin-react-percy
```

Or using `yarn`:

```sh
yarn add --dev eslint-plugin-react-percy
```

Add `react-percy` to the plugins section of your `.eslintrc` configuration file:

```json
{
  "plugins": [
    "react-percy"
  ]
}
```

You can whitelist the environment variables provided by `react-percy` by doing:

```json
{
  "env": {
    "react-percy/globals": true
  }
}
```

If you're using ESLint v4.1.0 or later, you can instead scope the `react-percy` environment variables to only `react-percy` snapshot files by doing:

```json
{
  "overrides": [
    {
      "files": [
        "**/*.percy.{js,jsx}"
      ],
      "env": {
        "react-percy/globals": true
      }
    }
  ]
}
```

## Advanced configuration

### Webpack configuration

The default Webpack configuration of `react-percy` will run Babel on all `.js` and `.jsx` files in your project, with support for all modern JS language features.

If your project is a non-ejected `create-react-app`, we also automatically include `create-react-app` Webpack rules for CSS and image files.

Otherwise, if you already have your own Webpack setup, `react-percy` will not use it by default. However, we allow you to customize our Webpack setup by creating a `percy.config.js` file in your project's root directory and including an object with any custom Webpack settings.

#### Reusing webpack config

If you have a particularly complex Webpack config, you may wish to simply reuse it rather than rewriting your rules and plugins in `percy.config.js`.

To do so, you can import your Webpack config in `percy.config.js` and set it as the `webpack` field like so:

```javascript
import config from './webpack.config.js';

export default {
  webpack: config
};
```

Keep in mind that we ignore the `entry` and `output` fields of your Webpack settings, so if your Webpack config has any plugins that rely on these you may encounter errors. For example, the Webpack `CommonsChunkPlugin` often refers to specific `entry` names, but as we ignore your original `entry` settings it will not be able to find them.

Also remember that the `webpack` field must be a plain object; it cannot be an array or a function.

#### Custom Webpack config

For example, let's say you wanted to add SCSS support in your snapshots. Simply add the following to a file called `percy.config.js` in your project's root directory:

```javascript
export default {
  webpack: {
    module: {
      rules: [
        {
          test: /\.scss$/,
          loaders: ['style-loader', 'css-loader', 'sass-loader']
        }
      ]
    }
  }
};
```

You can also provide plugins:

```javascript
import webpack from 'webpack';

export default {
  webpack: {
    plugins: [
      new webpack.DefinePlugin({
        'process.env.CUSTOM_ENV': '"foo"',
      }),
    ]
  }
};
```

#### Supported Webpack options

There are a few things to keep in mind when customizing our Webpack configuration.

You can add almost any Webpack config options in `percy.config.js`, including rules, plugins, and aliases. But you will not be able to modify the entry or output fields.

Also note that the `webpack` field in `percy.config.js` must be an object. Unlike a plain `webpack.config.js` file, it cannot be a function or an array.

#### Including entry files

As noted above, `react-percy` ignores the `entry` section of your Webpack settings and instead only builds Percy snapshot files and their imports. If your original Webpack `entry` includes additional global files like `babel-polyfill` or CSS files that you want to include in all snapshots, you can include them by creating a `percy.config.js` file in your project's root directory and adding an `includeFiles` setting like so:

```javascript
export default {
  includeFiles: [
    'babel-polyfill',
    './styles/global.css'
  ]
};
```

These files can be package names or relative paths to files within your project. Note that relative paths will be resolved from the root directory of your project.

Any packages or files listed there will be loaded before any snapshots.

## Best practices

### Percy snapshot files

`react-percy` looks for files in your project ending with `.percy.js` or `.percy.jsx`.

We recommend keeping your Percy snapshots close to the components being tested, but it's entirely up to you!

One common pattern is to add Percy snapshot files right next to the component files. So if you have a component in `src/components/Button.js`, you would add its snapshots in `src/components/Button.percy.js`.

Another common pattern is to keep Percy snapshots in a `__percy__` folder. For the above example, that would be `src/components/__percy__/Button.percy.js`.

### Basic snapshots

```javascript
percySnapshot(name, fn)
```

The first argument is the snapshot name; the second argument is a function that returns the React markup to snapshot. For example:

```javascript
percySnapshot('Button', () => {
  return <Button>My Button</Button>;
});
```

The name is what you will see when reviewing snapshots in Percy's web interface, and must be unique across all snapshots in your project. In the above example, the snapshot will be named "Button".

The function may include any necessary setup, but must ultimately return the React markup to snapshot.

### Testing multiple breakpoints

```javascript
percySnapshot(name, options, fn)
```

A powerful feature of Percy is visual regression testing for responsive designs. To take snapshots of your component at multiple breakpoints, simply specify the widths to use via the optional middle `options` argument:

```javascript
percySnapshot('Button', { widths: [320, 768, 1024] }, () => {
  return <Button>My Button</Button>;
});
```

This will take snapshots of "Button" at widths of 320px, 768px, and 1024px.

### Hooks

In some cases while writing Percy snapshots you may have some setup work that needs to happen before snapshots run. This is often useful for setting up props that you'd like to share across multiple snapshots.

For example, suppose you had a `PlayerInfo` component with multiples variations that share many of the same props. You could initialize the common props in `beforeEach` and use them in each snapshot:

```javascript
let props;

beforeEach(() => {
  props = {
    firstName: 'Venus',
    lastName: 'Williams',
    age: 37
  };
});

percySnapshot('PlayerInfo: basic', () => {
  return <PlayerInfo {...props} />;
});

percySnapshot('PlayerInfo: expanded', () => {
  return <PlayerInfo {...props} country="USA" sport="Tennis" />;
});
```

### Grouping snapshots

You may also group snapshots together using a `suite` block.

`suite` blocks have the added benefit of prefixing the names of all snapshots within them. For example:

```javascript
suite('Button', () => {
  percySnapshot('primary', () => {
    return <Button primary>Primary Button</Button>;
  });

  percySnapshot('secondary', () => {
    return <Button secondary>Secondary Button</Button>;
  });

  percySnapshot('inverse', () => {
    return <Button inverse>Inverse Button</Button>;
  });
});
```

This will generate three snapshots: `Button: primary`, `Button: secondary`, and `Button: inverse`.

You can also set the widths for all snapshots within a `suite` block:

```javascript
suite('Button', { widths: [320, 768] }, () => {
  percySnapshot('primary', () => {
    return <Button primary>Primary Button</Button>;
  });

  percySnapshot('secondary', () => {
    return <Button secondary>Secondary Button</Button>;
  });

  percySnapshot('inverse', () => {
    return <Button inverse>Inverse Button</Button>;
  });
});
```

This will generate snapshots of each button at both 320px and 768px wide.

You can override the widths specified on a `suite` on individual snapshots:

```javascript
suite('Button', { widths: [320, 768] }, () => {
  percySnapshot('primary', () => {
    return <Button primary>Primary Button</Button>;
  });

  percySnapshot('secondary', () => {
    return <Button secondary>Secondary Button</Button>;
  });

  percySnapshot('inverse', { widths: [480, 1024] }, () => {
    return <Button inverse>Inverse Button</Button>;
  });
});
```

This will generate snapshots of `Button: primary` and `Button: secondary` at 320px and 768px wide, and "Button: inverse" at 480px and 1024px wide.

When they are inside a `suite` block, `beforeEach` blocks only apply to the snapshots within that `suite` block:

```javascript
beforeEach(() => {
  // this will run before each snapshot in this file
});

suite('Button', () => {
  beforeEach(() => {
    // this will only run before "Button: primary" and "Button: secondary"
  });

  percySnapshot('primary', () => {
    return <Button primary>Primary Button</Button>;
  });

  percySnapshot('secondary', () => {
    return <Button secondary>Secondary Button</Button>;
  });
});

suite('Textbox', () => {
  beforeEach(() => {
    // this will only run before "Textbox: disabled"
  });

  percySnapshot('disabled', () => {
    return <Textbox disabled />;
  });
});
```

Additionally, you can nest `suite` blocks within each other, and the names and widths will cascade down:

```javascript
suite('Button', { widths: [320, 768] }, () => {
  percySnapshot('primary', () => {
    return <Button primary>Primary Button</Button>;
  });

  percySnapshot('secondary', () => {
    return <Button secondary>Secondary Button</Button>;
  });

  suite('submit', () => {
    percySnapshot('enabled', () => {
      return <Button submit>Submit Button</Button>;
    });

    percySnapshot('disabled', () => {
      return <Button submit disabled>Disabled Submit Button</Button>;
    });
  });
});
```

This will generate 320px and 768px wide snapshots of `Button: primary`, `Button: secondary`, `Button: submit: enabled`, and `Button: submit: disabled`.

## Troubleshooting

### Percy is rendering a blank white page

If you're seeing blank white snapshots in Percy, it usually means an error occurred client-side when loading your snapshot. To debug this, you can run `react-percy` locally with the `--debug` flag so you can load snapshots in your local browser.

```sh
$ react-percy --debug
```

After running `react-percy` with the `--debug` flag, you'll find a `.percy-debug` folder at the root of your project. Open the `index.html` file inside in your web browser and specify the snapshot you'd like to view as the `snapshot` query parameter.

For example, if you wanted to debug a snapshot named "Button: primary", you would open `file:///path/to/project/.percy-debug/index.html` in your browser and append `?snapshot=Button%3A%20primary` to the URL. Note that the snapshot name is URL encoded. The console output of running `react-percy` with the `--debug` flag will give you the URL-encoded query strings to use to preview any snapshot.


### Invariant Violation: Element type is invalid: expected a string (for built-in components) or a class/function (for composite components) but got: undefined

This error typically occurs when you are incorrectly importing your component.

For example, if your component is exported as a named export, be sure to import it that way:

```javascript
export class MyComponent extends React.Component {
  render() {
    return <div>My component</div>;
  }
}
```

```javascript
import { MyComponent } from './MyComponent';
```

You may also see this error if you are mixing ES module imports/exports with CommonJS modules. For example, if your component is using an ES default export but your snapshot file is written in CommonJS, remember to append `.default` to your require import:

```javascript
export default class MyComponent extends React.Component {
  render() {
    return <div>My component</div>;
  }
}
```

```javascript
const MyComponent = require('./MyComponent').default;
```

## GitHub integration

Percy **automatically integrates with GitHub PRs**, so you can do visual reviews with each PR's code review.

![](https://cloud.githubusercontent.com/assets/75300/13929974/13750b2c-ef5a-11e5-9a87-3ad3b335cc0d.png)

See our [GitHub integration](/docs/integrations/github) docs for more info.

## Contributing

1. Fork it ( https://github.com/percy/react-percy/fork )
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create a new pull request

[Throw a ★ on it!](https://github.com/percy/react-percy) :)
