# GitHub Integration

As you're setting up Percy, you will be prompted to add our GitHub app to integrate with GitHub pull requests. Once you've added the app, Percy will automatically update the pull request summary if there are visual differences waiting for review, and details of which team member approved them.

## Approval workflow

Percy provides a workflow for **visual review alongside code review**. If a build has visual diffs, it will be marked as a failure in the Pull Request. You can review and approve visual diffs in one click as part of your code review process.

Approving a build is not required, you can still merge the Pull Request in GitHub. Approving a build in Percy simply sets the GitHub commit status to green/successful, so that your team can see that any visual changes were reviewed and approved.

![](/images/docs/docs-github.jpg)

You can use this workflow as a way to incorporate a simple visual review process into your current code review process.
