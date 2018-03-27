# Tutorial: Using Percy with Rails

This 2 minute guide walks you through cloning the TodoMVC example app, making some changes, and seeing the visual difference in Percy's visual review tool.

The tutorial assumes you have [ruby](https://www.ruby-lang.org/en/documentation/installation/),  [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git), and [phantomjs](http://phantomjs.org/download.html) installed.


**Step 1:** Clone the example app:

```bash
$ git clone https://github.com/percy/example-rails.git
$ cd example-rails
```

**Step 2:** Sign in to Percy, create a new organization, skip the GitHub integration, and create a new project.  You can name both the organization and project 'todo'.

After you've created the project, you'll be shown two environment variables.

**Step 3:** In the shell window you're working in, export the 2 environment variables:

```bash
$ export PERCY_TOKEN=<your token here>
$ export PERCY_PROJECT=<your project here>
```

Note: usually these would only be set up in your [CI environment](/docs), but to keep things simple we'll configure them in your shell so that Percy is enabled in your local environment.

**Step 4:** Install the example app's dependencies:

```bash
$ bundle install && bundle exec rake db:create && bundle exec rake db:migrate
```

**Step 5:** Run the tests:

```bash
$ bundle exec rake test
```

This will run the test suite and send snapshots to Percy. You can view the snapshots in Percy now if you want, but there will be no visual comparisons yet.

![](/images/docs/tutorial-without-diffs.jpg)

**Step 6:** Use your text editor to edit `app/assets/stylesheets/application.css` and change the `#new-todo` background-color from `#ffffff` to `#f5f5f5`.

**Step 7:** Also edit `app/views/layouts/application.html` and edit the footer to add a new paragraph on line 22: `<p>Now tested with Percy</p>`

**Step 8:** Commit the change:

```bash
$ git add . && git commit -m "Changed background color and updated footer."
```

**Step 9:** Run the tests again:

```bash
$ bundle exec rake test
```

This will rerun the tests with your changes and upload new snapshots to Percy.  The new snapshots will be compared with the previous snapshots, showing any visual diffs.

**Step 10:** Visit your project in Percy and you'll see a new build with the visual comparison between the two test runs.  Click anywhere on the Build 2 row that says '2 visual diffs'.  You can see the original snapshot on the left, and the new snapshot on the right.

Percy has highlighted what's changed on the page! You can click on the highlight to reveal the underlying screenshot.

![](/images/docs/tutorial-with-diffs.jpg)

**Finished! 😀**  

From here, you can try making your own changes to the app if you like.  If you do, re-run the tests, and then you'll see your changes reflected in Percy.

This tutorial used TodoMVC with Percy already added—you can see how we [added Percy to TodoMVC](https://github.com/percy/example-rails/compare/pre-percy...master), and check out the [docs](/docs/clients/ruby/capybara) for how to add Percy to your own Rails app!
