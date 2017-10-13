# Percy for Ruby apps
#### [ Percy::Capybara on GitHub <i class="fa fa-github" aria-hidden="true"></i>](https://github.com/percy/percy-capybara)

[![](https://travis-ci.org/percy/percy-capybara.svg?branch=master)](https://travis-ci.org/percy/percy-capybara)
[![](https://badge.fury.io/rb/percy-capybara.svg)](https://rubygems.org/gems/percy-capybara)

Percy::Capybara is a library for integrating Percy's visual regression testing into your existing [Capybara](https://github.com/teamcapybara/capybara) feature tests in any Ruby web framework—including Rails, Sinatra, etc.

If you've written feature tests (or "UI tests", "acceptance tests", etc.), you know how hard it can be to get them right and to get your app in the correct UI state. Percy::Capybara lets you take all the time you've spent building your feature tests and expand them with screenshots and visual regression tests to cover all the visual changes in your app, even behind complex UI states.

The examples below assume you are using RSpec, but they could be easily adapted for other testing frameworks. If you are using Rails, see the [Percy Rails integration](/docs/clients/ruby/capybara-rails).

## Installation

[!INCLUDE /docs/clients/-do-setup-first]

Add this line to your application's Gemfile:

```ruby
gem 'percy-capybara'
```

And then run:

```bash
$ bundle install
```

Or install it yourself with `gem install percy-capybara`.

## Setup

Test suites must call two hooks:

* `Percy::Capybara.initialize_build` before running the test suite.
* `Percy::Capybara.finalize_build` after the test suite is finished, even if tests fail.

<div class="Alert Alert--warning">

**NOTE:** If your builds get stuck <i>"Receiving..."</i>, check this configuration first.

</div>

### RSpec

Add these setup lines to `spec_helper.rb`:

```ruby
require 'percy/capybara'

RSpec.configure do |config|
  # ...

  config.before(:suite) { Percy::Capybara.initialize_build }
  config.after(:suite) { Percy::Capybara.finalize_build }
end
```

### Cucumber

Add these lines to `features/support/percy.rb`:

```ruby
require 'percy/capybara'

Percy::Capybara.initialize_build
at_exit { Percy::Capybara.finalize_build }
```

### Other test frameworks (MiniTest, etc.)

For example, with MiniTest you can add this to your `test_helper.rb`:

```ruby
require 'percy/capybara'

Percy::Capybara.initialize_build
MiniTest.after_run { Percy::Capybara.finalize_build }
```

## Non-Rails asset setup (Webpack, Sinatra/Rack apps, etc.)

Percy automatically supports the Rails asset pipeline with no configuration, but non-Rails apps will need to add a `use_loader` configuration in order to load assets correctly. This loader config must be setup before `initialize_build` is called.

### Filesystem loader

Percy::Capybara can be used with any asset pipeline, including [webpack](https://webpack.github.io/), by simply pointing it at a directory of compiled assets.

```ruby
require 'percy/capybara'

assets_dir = File.expand_path('../../dist/', __FILE__)
Percy::Capybara.use_loader(:filesystem, assets_dir: assets_dir, base_url: '/assets')
```

* `assets_dir`: absolute path to your compiled static assets (_not_ source assets).
* `base_url`: (optional) path prefix to where your webserver serves the assets (ex: `/assets`)

## Usage

Now the fun part!

You can integrate with Percy by adding one line to your existing feature specs:

```ruby
Percy::Capybara.snapshot(page, name: 'homepage with dropdown')
```

For example:

```ruby
describe 'a feature', type: :feature, js: true do
  it 'shows the dropdown menu when clicked' do
    visit '/'
    first('.dropdown-toggle').click
    expect(page).to have_selector('#main-dropdown', visible: true)

    Percy::Capybara.snapshot(page, name: 'homepage with dropdown')
  end
end
```

**Done!** 🚀 That's it! Now commit and push your branch to run your tests in your CI service, or create a GitHub PR. Snapshots will be uploaded to Percy for rendering and processing visual diffs.

<div class="Alert Alert--info">

Best practices for Capybara and writing feature specs are outside the scope of these docs. See the [Capybara docs](https://github.com/teamcapybara/capybara#using-capybara-with-rspec) for more Capybara usage examples.

</div>

### Responsive visual diffs

You can use Percy [responsive visual diffs](/docs/learn/responsive) to test pages at different CSS breakpoints.

In your `spec_helper.rb`, simply set the default widths for all snapshots:

```ruby
Percy.config.default_widths = [375, 1280]
```

With the above configuration, all snapshots will render at both mobile and desktop breakpoints by default.

You can also override widths per-snapshot:

```ruby
Percy::Capybara.snapshot(page, name: 'homepage', widths: [375, 1280])
```

### Local dev environments

You may want to enable Percy locally while getting set up. See [Local development](/docs/setup/local) setup for more info.

### Identifying snapshots

Percy needs to be able to uniquely identify the same snapshot across builds to provide visual diffs.

To accomplish this, `Percy::Capybara` uses the current page URL as the name of the snapshot. We assume that your app has been built with stateful navigation and that the URL fully identifies the page state.

However, there are many cases where this is not enough—for example, populating test data in the page, or performing a UI interaction that doesn't change the URL (like clicking the dropdown in the example above).

To manually identify a snapshot, you can provide the `name` parameter:

```ruby
Percy::Capybara.snapshot(page, name: 'homepage (with dropdown clicked)')
```

The `name` param can be any string that makes sense to you to identify the page state. It should be unique and remain the same across builds. It is **required** if you are snapshotting a page multiple times with the same URL.

## Advanced

### Including iframes

Percy::Capybara disables iframes by default. We've found that iframes usually don't affect the rendering of the page, and can sometimes timeout and break builds. However, if you'd like to include iframes, use the `include_iframes` option.

```ruby
Percy::Capybara.use_loader(... , include_iframes: true)
```


## Troubleshooting

### Debug mode

Run with the `PERCY_DEBUG=1` environment variable to see what build resources and snapshots are being created for your build.

### WebMock/VCR users

If you use [VCR](https://github.com/vcr/vcr) to mock and record HTTP interactions, you need to allow connections to the Percy API:

```ruby
VCR.configure do |config|
  config.ignore_hosts 'percy.io'
end
```

If you use [WebMock](https://github.com/bblimke/webmock) to stub out HTTP connections, you need to allow connections to the Percy API:

```ruby
WebMock.disable_net_connect!(allow: 'percy.io')
```

If you still experience problems, and you are using a Webmock version older than 2.0.0, try adding `require: false` to the line in your Gemfile so that Webmock won't block the HTTP request in `initialize_build`:

```ruby
gem 'webmock', require: false
```

## Other resources

*   [Percy::Capybara Reference](http://www.rubydoc.info/gems/percy-capybara/Percy/Capybara) on RubyDoc

## Changelog

*   [percy-capybara releases on GitHub](https://github.com/percy/percy-capybara/releases)

## Contributing

1.  Fork it ([https://github.com/percy/percy-capybara/fork](https://github.com/percy/percy-capybara/fork))
2.  Create your feature branch (`git checkout -b my-new-feature`)
3.  Commit your changes (`git commit -am 'Add some feature'`)
4.  Push to the branch (`git push origin my-new-feature`)
5.  Create a new Pull Request

[Throw a ★ on it!](https://github.com/percy/percy-capybara) :)
