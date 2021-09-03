# GitHub Organization Git Audit Log Action

> A GitHub Action to generate a report that contains the total amount of Git clones, pushes and fetches per repository for a set interval.

## Usage

The example [workflow](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions) below runs on a weekly [schedule](https://docs.github.com/en/actions/reference/events-that-trigger-workflows#scheduled-events) and can be executed manually using a [workflow_dispatch](https://docs.github.com/en/actions/reference/events-that-trigger-workflows#manual-events) event.

```yml
name: Git Audit Log Report

on:
  workflow_dispatch:
  schedule:
    - cron: '0 0 * * 0' # Runs every Sunday at 00:00

jobs:
  git-audit-log-report:
    
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout
        uses: actions/checkout@v2

      - name: Get Git Audit Log
        uses: nicklegan/github-org-git-audit-log-action@v1.0.0
        with:
          token: ${{ secrets.ORG_TOKEN }}
```

## GitHub secrets

| Name                 | Value                                                            | Required |
| :------------------- | :--------------------------------------------------------------- | :------- |
| `ORG_TOKEN`          | A `repo`, `read:org`scoped [Personal Access Token]               | `true`   |
| `ACTIONS_STEP_DEBUG` | `true` [Enables diagnostic logging]                              | `false`  |

[personal access token]: https://github.com/settings/tokens/new?scopes=repo,read:org&description=Git+Audit+Log+Action 'Personal Access Token'
[enables diagnostic logging]: https://docs.github.com/en/actions/managing-workflow-runs/enabling-debug-logging#enabling-runner-diagnostic-logging 'Enabling runner diagnostic logging'

:bulb: Disable [token expiration](https://github.blog/changelog/2021-07-26-expiration-options-for-personal-access-tokens/) to avoid failed workflow runs when running on a schedule.


## Action inputs

| Name              | Description                                                    | Default                     | Options                          | Required |
| :---------------- | :------------------------------------------------------------- | :-------------------------- | :--------------------------------| :------- |
| `org`             | Organization different than workflow context                   |                             |                                  | `false`  |
| `days`            | Amount of days in the past to collect data for (max  7 days)   | `7`                         |                                  | `false`  |
| `sort`            | Column used to sort the acquired contribution data             | `gitClone`                  | `gitClone`, `gitPush` `gitFetch` | `false`  |
| `committer-name`  | The name of the committer that will appear in the Git history  | `github-actions`            |                                  | `false`  |
| `committer-email` | The committer email that will appear in the Git history        | `github-actions@github.com` |                                  | `false`  |

:bulb: The audit log retains [Git events for 7 days](https://docs.github.com/organizations/keeping-your-organization-secure/reviewing-the-audit-log-for-your-organization#using-the-rest-api). This is shorter than other audit log events.

## CSV layout

The results of all except the first column will be the sum of [Git audit log events](https://docs.github.com/organizations/keeping-your-organization-secure/reviewing-the-audit-log-for-your-organization#git-category-actions) for the requested interval per organization owned repository.

| Column      | Description                                           |
| :-----------| :---------------------------------------------------- |
| Repository  | Repository part of a requested organization           |
| Git clones  | Sum of Git clones for a set interval per repository   |
| Git pushes  | Sum of Git pushes for a set interval per repository   |
| Git fetches | Sum of Git fetches for a set interval per repository  |

A CSV report file be saved in the repository `reports` folder using the following naming format: `organization-date-interval.csv`.