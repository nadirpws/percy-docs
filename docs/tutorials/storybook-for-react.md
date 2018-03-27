# Tutorial: Using Percy with Storybook for React

This 2 minute guide walks you through cloning an example storybook, making some changes, and seeing the visual difference in Percy's visual review tool.

The tutorial assumes you're already familiar with [Storybook](https://storybook.js.org/) and focuses on using it with Percy.  You'll still be able to follow along if you're not familiar with Storybook, but we won't spend time introducing Storybook's concepts.

The tutorial also assumes you have [node with npm](https://nodejs.org/en/download/) and [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) installed.


**Step 1:** Clone the example storybook and install dependencies:

```bash
$ git clone https://github.com/percy/example-storybook-for-react.git
$ cd example-storybook-for-react
$ npm install
```

The example app and it's storybook will now be ready to go. Optionally, you can run `npm run start` to start the app, and explore it in your browser by visiting [http://localhost:3000/](http://localhost:3000/).  

You can also optionally run `npm run storybook` to start storybook, and view it in your browser at [http://localhost:9001/](http://localhost:9001/).

**Step 2:** Sign in to Percy, create a new organization, skip the GitHub integration, and create a new project.  You can name both the organization and project 'todo'.

After you've created the project, you'll be shown two environment variables.

**Step 3:** In the shell window you're working in, export the 2 environment variables:

```bash
$ export PERCY_TOKEN=<your token here>
$ export PERCY_PROJECT=<your project here>
```

Note: usually these would only be set up in your [CI environment](/docs), but to keep things simple we'll configure them in your shell so that Percy is enabled in your local environment.

**Step 4:** Snapshot the storybook:

```bash
$ npm run snapshot
```

This will run storybook's build-storybook command to create a static storybook, and then send it to Percy to generate screenshots for comparison.  You can view the screenshots in Percy now if you want, but there will be no visual comparisons yet.

![](/images/docs/tutorial-react-for-storybook-without-diffs.jpg)

**Step 5:** Use your text editor to edit `components/Footer.js` and remove line 59 that reads `{this.renderClearButton()}`.

**Step 7:** Also edit `components/stories/MainSection.js` and insert a new line 18: `{ id: 'three', text: 'Item Three', completed: false },`

**Step 8:** Commit the change:

```bash
$ git add . && git commit -m "Removed clear button and added item."
```

**Step 9:** Run the snapshots again:

```bash
$ npm run snapshot
```

This will rebuild and upload the modified storybook to Percy.  New snapshots will be taken and compared with the previous snapshots, showing any visual diffs.

**Step 10:** Visit your project in Percy and you'll see a new build with the visual comparisons between the two runs.  Click anywhere on the Build 2 row that says '10 visual diffs'.  You can see the original snapshots on the left, and the new snapshots on the right.

Percy has highlighted what's changed in the components! Component snapshots with the largest changes are shown first, so the story with the Item Three added is shown at the top of the list. You can click on the highlight to reveal the underlying screenshot.

![](/images/docs/tutorial-react-for-storybook-with-diffs.jpg)

If you scroll down, you'll see the other stories that were impacted by removing the 'Clear completed' button.  Finally, you'll see the unchanged snapshots grouped together at the bottom of the list.

**Finished! 😀**

From here, you can try making your own changes to the storybook if you like.  If you do, re-run the tests, and then you'll see your changes reflected in Percy.

This tutorial used a storybook with Percy already added. You can see how we [added Percy to the example storybook](https://github.com/percy/example-storybook-for-react/commit/89b55a0b3d55f6df5a437cb9049a0b15271a25ba), or check out the [docs](/docs/clients/javascript/react-storybook) for how to add Percy to your own Storybook!
