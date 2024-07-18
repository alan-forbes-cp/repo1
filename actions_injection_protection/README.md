# 'Actions Injection Protection' - summary research notes:

The initial intention with this task was to consider 'injection protection' only. However as this is closely related to other aspects of 'security hardening' the scope has broadened slightly. Each topic consists of a brief summary together with an outline of its treatment in relation to our 'comment trigger' action task and Template-Repo (where applicable).

## Useful links:

Github docs on [script injections](https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions#understanding-the-risk-of-script-injections)

General advice on [action security hardening](https://blog.gitguardian.com/github-actions-security-cheat-sheet/)

## Secrets:

- Don't include structured data in secrets.
- Review secrets workflow run usage and logging output for correct redaction.
- Audit secrets periodically to ensure content is still required.
- ***comment trigger handling:*** *** USE A FILE ?? ***

## GITHUB_TOKEN:

- Explicitly limit GITHUB_TOKEN permissions and scope usage minimally.
- ***comment trigger handling:*** Usage is set to allow pull request reads/writes only as required.

## Actions injection protection:

- External actions are preferred to shell 'run' steps where possible.
- Where shells are used, double quote variables to avoid word splitting.
- Prevent injections by setting the value of expressions to an intermediate environment variable.
- Declare env variables at step level (i.e. lowest) where possible.
- Code scan with e.g. CodeQL.
- ***comment trigger handling:*** The action contains shell variables and references vulnerable contexts (.body, .pr_ref). The action is hardened against injection by ensuring variables are double quoted and declared at step level via intermediates. Our Template-Repo also includes CodeQL scans.

## CODEOWNERS file:

- Add actions 'workflows' directory to the code owners list, so that any proposed changes to these files will first require approval from a designated reviewer.
- ***comment trigger handling:*** Our Template-Repo CODEOWNERS includes default owners for everything in the repo.

## 3rd party actions and workflows:

- Minimise or avoid the use of 3rd party actions.
- Enable Dependabot updates for actions.
- ***comment trigger handling:*** The trigger uses only the standard "checkout" action. Our Template-Repo also runs Dependabot.

## Repo-level workflow permissions:
- Set the following (Settings/Actions/General):
-- Read repository contents and packages permissions [ON]
-- Allow GitHub Actions to create and approve pull requests [OFF] 
- ***comment trigger handling:*** These are set in our Template-Repo.

## Fork PR workflow runs:
- Enforce approvals (Settings/Actions/General) before running workflows for pull requests originating from outside collaborators.
- ***comment trigger handling:*** Set in our Template-Repo.
