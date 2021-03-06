# Bitrise Workflow Editor

## How to install & use the Workflow Editor on your Mac/Linux

1. Install [Go](https://golang.org) `brew install go`(on macOS)
1. Install the latest [Bitrise CLI](https://www.bitrise.io/cli) - it's a single binary command line tool
1. Run `bitrise setup` just to be sure everything's prepared
1. `cd` into a directory where you have your `bitrise.yml`, and run: `bitrise :workflow-editor`

That's all. The Workflow Editor is now part of the Bitrise CLI core plugins, so you don't have to install it manually.

To upgrade to the latest version of the Workflow Editor run:

```
bitrise plugin update workflow-editor
```

*Join the Workflow Editor's discussion at: [https://discuss.bitrise.io/t/workflow-editor-v2-offline-workflow-editor/39](https://discuss.bitrise.io/t/workflow-editor-v2-offline-workflow-editor/39)*


## Development

### Build a stand-alone binary, with embedded resources

```
bitrise run go-install
```

### Run in development mode

1. In the Workflow Editor's directory, run `docker-compose up`.
1. In your browser, you can reach the Workflow Editor on `localhost:1234`. Be aware that you usually have to wait a while.
1. By default, the Workflow Editor will open the bitrise.yml and .bitrise.secrets.yml found in this folder. For testing purposes, you probably want to be able to edit custom files. This can be achieved by setting the `TEST_BITRISE_CONFIG_PATH` and `TEST_BITRISE_SECRETS_PATH` environment variables with the path pointing to the custom files' paths.

### Run tests

1. In the Workflow Editor's directory, run `up-middleman-jasmine`.
1. In your browser, you can reach the tests on `localhost:4567/jasmine`.
1. Every time you make a change to the code, you have to exit the running workflow and start it up again. You can make changes to the specs without having to do this.

## New version release

- Generate a GitHub personal access token for your user (one who has rights to create releases on the repository) - you can generate one here: https://github.com/settings/tokens
- Generate a Discuss API key: you need to be a Discourse admin for this, then you can generate an API key for yourself at: https://discuss.bitrise.io/admin/api/keys
- Ensure clean git
- If new release requires Bitrise CLI to be updated, in `bitrise-plugin.yml` change `min_version` requirement of the `bitrise` tool to the required CLI version
- Optional: set the following secrets: $GITHUB_RELEASE_API_TOKEN, $GITHUB_USERNAME, $DISCUSS_API_KEY, $DISCUSS_USERNAME
- Call `bitrise run create-release`
- During the build you will need to specify a new version number, and if you did not specify any of the secrets above, you will need to specify those as well.
- After the build has finished, close the related GitHub issues, and milestones if the issues were assigned to any.

## Testing if version release works, without actually releasing

- In bitrise.yml, create a workflow e. g. `test-release`
- From the `create-release` workflow, copy-paste the *GitHub release* and *Create Discuss topic* steps.
- In the GitHub release step, remove the `files_to_upload` input, set the `$NEW_RELEASE_VERSION` everywhere to something arbitrary, same for the `body`, and **most importantly set `draft: 'yes'`**
- In the Create Discuss topic step, **change the `DISCUSS_CHANGELOG_CATEGORY_ID` to the ID of one our discuss.bitrise.io's internal channels' ID** (you can find an ID using the Discourse API with a cURL request) so that it is only visible to us; also change the `title` and the `raw` parameter to something arbitrary.
- After the test release process, don't forget to delete the draft release and the internal changelog topic.
