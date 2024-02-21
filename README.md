# Workflow Conclusion Status

Fork of [technote-space/workflow-conclusion-action](https://github.com/technote-space/workflow-conclusion-action) GitHub action to get workflow conclusion. 

## Table of Contents

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
<details>
<summary>Details</summary>

- [Workflow Conclusion Status](#workflow-conclusion-status)
  - [Table of Contents](#table-of-contents)
  - [Usage](#usage)
    - [Success](#success)
    - [Failure](#failure)
  - [Author](#author)

</details>
<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Usage
e.g. Lint => Test => Publish (only tagged) => slack (only if any job fails)
```yaml
on: push

name: CI

jobs:
  lint:
    name: ESLint
    runs-on: ubuntu-latest
    ...

  test:
    name: Coverage
    needs: lint
    strategy:
      matrix:
        node: ['11', '12']
    ...

  publish:
    name: Publish Package
    needs: test
    if: startsWith(github.ref, 'refs/tags/v')
    ...

  slack:
    name: Slack
    needs: publish # set "needs" only last job except this job
    runs-on: ubuntu-latest
    if: always() # set "always"
    steps:
        # run this action to get the workflow conclusion
        # You can get the conclusion via env (env.WORKFLOW_CONCLUSION)
      - uses: AbsoLouie/workflow-conclusion-status@v1.0.1

        # run other action with the workflow conclusion
      - uses: 8398a7/action-slack@v3
        with:
          # status: ${{ env.WORKFLOW_CONCLUSION }} # neutral, success, skipped, cancelled, timed_out, action_required, failure
          status: failure
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
        if: env.WORKFLOW_CONCLUSION == 'failure' # notify only if failure
```

### Success
![Success](https://raw.githubusercontent.com/AbsoLouie/workflow-conclusion-status/images/success.png)

Slack action step is skipped because all jobs are success.

### Failure
![Failure](https://raw.githubusercontent.com/AbsoLouie/workflow-conclusion-status/images/failure.png)

Slack action step has been executed even if some jobs were skipped.

## Author
[GitHub (Technote)](https://github.com/AbsoLouie)  
[Blog](https://technote.space)
