# 'Actions Security Hardening Miscellany' - summary research notes:

The initial intention with this task was to consider 'actions injection protection' only. However as this is closely related to other aspects of 'security hardening' the scope has broadened slightly. Each topic consists of a very brief advice summary together with an outline of its treatment in relation to our 'comment trigger' action task and Template-Repo (both where applicable).

## Useful links:

Github docs on [script injections](https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions#understanding-the-risk-of-script-injections)

General advice on [action security hardening](https://blog.gitguardian.com/github-actions-security-cheat-sheet/)

## Secrets:

- Don't include structured data in secrets.
- Review secrets workflow run usage and logging output for correct redaction.
- Audit secrets periodically to ensure content is still required.
- ***comment trigger action handling / Template-Repo:*** *** USE A FILE ?? ***

## GITHUB_TOKEN:

- Explicitly limit GITHUB_TOKEN permissions and scope its usage minimally.
- ***comment trigger action handling / Template-Repo:*** Trigger action usage is set to allow pull request reads/writes only as required. The best practice recommendation for Template-Repo actions is to always explicitly set the permissions.

## Injection protection:

- External actions are preferred to inline 'run' shell steps where possible.
- Where shells are used, double quote variables to avoid word splitting.
- Prevent injections by setting the value of expressions to an intermediate environment variable.
- Declare env variables at step level (i.e. lowest) where possible.
- Code scan with e.g. CodeQL.
- ***comment trigger action handling / Template-Repo:*** The action contains shell variables and references vulnerable contexts (.body, .pr_ref). The action is hardened against injection by ensuring variables are double quoted and declared at step level via intermediates. Our Template-Repo also includes CodeQL scans.

## CODEOWNERS file:

- Add actions 'workflows' directory to the code owners list, so that any proposed changes to these files will first require approval from a designated reviewer.
- ***comment trigger action handling / Template-Repo:*** Our Template-Repo CODEOWNERS includes default owners for everything in the repo.

## 3rd party actions and workflows:

- Minimise or avoid the use of 3rd party actions.
- Enable Dependabot updates for actions.
- ***comment trigger action handling / Template-Repo:*** The trigger uses no 3rd party actions. Our Template-Repo also runs Dependabot.

## Repo-level workflow permissions:
- Set the following (Settings/Actions/General):
    - Read repository contents and packages permissions [ON]
    - Allow GitHub Actions to create and approve pull requests [OFF] 
- ***comment trigger action handling / Template-Repo:*** These are set in our Template-Repo.

## Fork PR workflow runs:
- Enforce approvals (Settings/Actions/General) before running workflows for pull requests originating from outside collaborators.
- ***comment trigger action handling / Template-Repo:*** Set in our Template-Repo.
