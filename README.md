<p>
  <a href="https://github.com/cgladish/cypress-slack-video-upload-action/actions"><img alt="typescript-action status" src="https://github.com/actions/typescript-action/workflows/build-test/badge.svg"></a>
</p>

# Upload Cypress videos and screenshots directly to Slack

This Github action enables you to take the screenshots and videos generated by Cypress after a test run and upload them directly to a Slack channel of your choice. All files are contained within a single message thread to minimize noise.

## Inputs

### `token`

**Required** Slack app token. See [Internal app tokens](https://slack.com/intl/en-ru/help/articles/215770388-Create-and-regenerate-API-tokens#internal-app-tokens)

- Create an app
- Under **Bot Token Scopes**, add `files:write` and `chat:write` permissions
- Install the app into your workspace
- Invite the bot to whatever channels you want to send the videos and screenshots to `/invite <botname>`
- Grab the `Bot User OAuth Token` from the `OAuth & Permissions` page
- Add that token as a secret to your Github repo's `Actions Secrets` found under `Settings -> Secrets` (in the examples below we call it `SLACK_TOKEN`)

### `channels`

**Required** Slack channels to upload to

### `workdir`

**Optional** The folder where Cypress stores screenshots and videos on the build machine.

Default: `cypress`

(this relative path resolves to `/home/runner/work/<REPO_NAME>/<REPO_NAME>/cypress`)

If your project uses Cypress from the project root folder, the default value will work for you.
But if your project uses Cypress in a subfolder (like most monorepos), you'll need to provide the relative path to that folder
(i.e. `e2e/cypress`).
(Don't include a trailing slash on your path!)

### `message-text`

**Optional** Custom Slack message text.

Default: `A Cypress test just finished. I've placed the screenshots and videos in this thread. Good pie!`

## Examples

### Upload results after every push

```yml
on: [push]

jobs:
  test-and-upload-results:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2

      - name: 'Run tests'
        uses: cypress-io/github-action@v2

      - name: 'Upload screenshots and videos to Slack'
        uses: cgladish/cypress-slack-video-upload-action@v1.3.0
        with:
          token: ${{ secrets.SLACK_TOKEN }}
          channels: 'engineering-ops'
```

### Only upload when open PRs fail

```yml
on: [pull_request]

jobs:
  test-and-upload-results-on-fail:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2

      - name: 'Run tests'
        uses: cypress-io/github-action@v2

      - name: 'Upload screenshots and videos to Slack'
        uses: cgladish/cypress-slack-video-upload-action@v1.3.0
        if: failure()
        with:
          token: ${{ secrets.SLACK_TOKEN }}
          channels: 'engineering-ops'
          message-text: 'Cypress tests failed! They have been placed in this thread, good luck.'
```
