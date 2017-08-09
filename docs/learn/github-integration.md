# GitHub integration

If you enable the Github integration in your organization, Percy automatically integrates with GitHub pull requests. Percy will automatically set commit statuses whenever your CI service runs a build.

https://github.com/marketplace/percy

## Approval workflow

Percy provides a workflow for **visual review alongside code review**. If a build has visual diffs, it will be marked as a failure in the Pull Request. You can review and approve visual diffs in one click as part of your code review process.

Approving a build is not required, you can still merge the Pull Request in GitHub. Approving a build in Percy simply sets the GitHub commit status to green/successful, so that your team can see that any visual changes were reviewed and approved.

![](https://cloud.githubusercontent.com/assets/75300/13929974/13750b2c-ef5a-11e5-9a87-3ad3b335cc0d.png)

You can use this workflow as a way to incorporate a simple visual review process into your current code review process.
