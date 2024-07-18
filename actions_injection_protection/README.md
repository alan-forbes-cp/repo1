# 'Actions Injection Protection' - research notes:

The initial intention with this task was to consider 'injection protection' only. However as this is very closely related to other aspects of 'security hardening' the scope has broadened slightly. Each topic consists of a brief overview together with an outline of its handling in relation to our 'comment trigger' action task where applicable.

## Useful links:

Github docs on [script injections](https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions#understanding-the-risk-of-script-injections)
General advice on [action security hardening](https://blog.gitguardian.com/github-actions-security-cheat-sheet/)

## Secrets:

- don't include structured data in secrets
- review secrets workflow run usage and logging output for correct redaction
- audit secrets periodically to ensure content is still required
- ***comment trigger handling:*** *** USE A FILE ?? ***

## GITHUB_TOKEN:

- explicitly limit GITHUB_TOKEN permissions and scope usage minimally
- ***comment trigger handling:*** usage is set to allow pull request reads/writes only as required

## Actions injection protection:

- Vulnerable contexts in our case: .body & .pr_ref
- set the value of the expressions to an intermediate environment variable (all done)
- use double quote shell variables to avoid word splitting (all done)
- use CodeQL code scanning
- declare env vars at step level where possible
- avoid 'run' steps and use external actions instead
- ***comment trigger handling:***

## Codeowners:

- add actions 'workflows' directory to the code owners list, so that any proposed changes to these files will first require approval from a designated reviewer.
- ***comment trigger handling:***

## 3rd party actions and workflows

- minimise or avoid use
- enable dependabot updates for actions
- uses only the standard "checkout" action
- ***comment trigger handling:***

## Set the following Repo-level Workflow permissions (repo: Settings/Actions/General)

- Read repository contents and packages permissions [ON]
- Allow GitHub Actions to create and approve pull requests [OFF] 
- ***comment trigger handling:***

## Disable workflow runs from forked repos

- enforce manual approvals before running GitHub workflows for pull requests originating from outside collaborators (in forks)
- ***comment trigger handling:***
