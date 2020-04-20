# GitHub Action to run Webpack and Sync S3 Bucket

This is a customized version of the s3-sync-action that uses the [vanilla AWS CLI](https://docs.aws.amazon.com/cli/index.html) to sync a directory (either from your repository or generated during your workflow) with a remote S3 bucket. It also runs the command: npx webpack --mode development to build the main.js file for deployment. This allows you to install packages that are scoped to your private org in GitHub or NPM

## Usage

### `main.yml` Example

Place in a `.yml` file such as this one in your `.github/workflows` folder. [Refer to the documentation on workflow YAML syntax here.](https://help.github.com/en/articles/workflow-syntax-for-github-actions)

```yaml
name: CI
on: [push]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@master
      - uses: brockmdavis/s3-sync-webpack-private-org-action@master
        env:
          SOURCE_DIR: ${{ github.workspace }}
          DIST_DIR: ${{ github.workspace }}/dist/
          AWS_DEFAULT_REGION: "us-east-1"
          AWS_S3_BUCKET: ${{ secrets.AWS_S3_BUCKET }}
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          CI_CD_TOKEN: ${{secrets.CI_CD_TOKEN}}
          ORG_NAME: ${{secrets.ORG_NAME}}
```

### Configuration

The following settings must be passed as environment variables as shown in the example. Sensitive information, especially `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, and `CI_CD_TOKEN` should be [set as encrypted secrets](https://help.github.com/en/articles/virtual-environments-for-github-actions#creating-and-using-secrets-encrypted-variables) — otherwise, they'll be public to anyone browsing your repository's source code and CI logs.

| Key                   | Value                                                                                                                                                                                               | Suggested Type        | Required | Notes                                                                    |
| --------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------- | -------- | ------------------------------------------------------------------------ |
| AWS_DEFAULT_REGION    | The region where you created your bucket. Set to [Full list of regions here.](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html#concepts-available-regions) | `env`                 | Yes      |                                                                          |
| AWS_S3_BUCKET         | The bucket name you want to publish the site to                                                                                                                                                     | `env` or `secret env` | Yes      | This does not have to be in the secrets but it makes it easier to manage |
| AWS_ACCESS_KEY_ID     | Your AWS Access Key. [More info here.](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html)                                                                                 | `secret env`          | Yes      |                                                                          |
| AWS_SECRET_ACCESS_KEY | Your AWS Secret Access Key. [More info here.](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html)                                                                          | `secret env`          | Yes      |                                                                          |
| CI_CD_TOKEN           | This needs to be a personal access token that is scoped to your org so it can install private packages                                                                                              | `secret env`          | Yes      |                                                                          |
| ORG_NAME              | This needs to be your org name, so if in your local `.npmrc` you have @turnerlabs, this should only be turnerlabs do not include the @                                                              | `env` or `secret env` | Yes      | This does not have to be in the secrets but it makes it easier to manage |
