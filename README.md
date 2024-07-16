# Rationale for 'internal testing runs via comment triggers' workflow:

'Org contact user' supplies a list of login names and a unique org 'trigger string' (as per login names, this should contain only alphanumeric characters and dashes).
- usage for trigger strings in comments is then: **/trigger-string**

Supplied user & trigger information is added to an action repository secret (currently **INTERNAL_TEST_LIST**) where users/trigger_strings are stored as follows:

    /user1/string1
    /user2/string1
    /user3/string2
    /user4/string2
    etc

- Each entry is space or newline separated.
- This registers users as an 'internal-test requester' and binds each to a specific trigger string.
- Note that existing secret settings in Github are not visible when being updated so the internal test list content should be saved elsewhere (TBD e.g 1password??) and reapplied in full on each update.

Org contact user defines a repo access token allowing:
1. scanning of PR comments, 
2. PR comment-body reads and 
3. PR comment writes.

Note that 'fine-grain' user PATs allow the setting of 'repository permissions' including read/write "pull request" perms (pull requests and related comments, assignees, labels, milestones, and merges). These offer the internal-side granularity and permissons required (i.e. as used in Scott's internal-side implementation).

Registered user then requests an internal-test run by entering the string "/trigger-string" as a PR comment body.
Our 'internal-test-request comment' trigger then fires:
- checks that its a new comment (no edits or deletes).
- checks that its a PR (and not an Issue - Github internal quirk).
- checks that the user exists in the INTERNAL_TEST_LIST secret and that the trigger string used is assigned to that user.
- checks that the PR is open.

If any checks fail the comment trigger stops and any remaining action jobs or steps will be silently skipped. The action itself will never fail.

If all checks pass the 'github-actions' bot then issues a validated 'internal-test request' as a comment including the following request data in the comment body:

    /trigger-string
    PR reference (commit SHA - which, again by Github quirk, is not simple to find.)
    validated requester @login_name (e.g. @user1)
    Codeplay Internal Testing: Request Validated

Internal-test-side bot (via access token) then scans for such comments, runs internal tests and reports final pass/fail to the external-side PR as a comment.
- (Scott's stuff goes here)

## Notes:

- Comments in PRs and Issues are "the same but different" so its necessary to distinguish between the two in the action code.
- Only comment trigger actions contained in the mainline workflow files are used (they run "from the mainline" which partly explains why it can be tricky to get PR branch information e.g. PR commit SHA).
- Currently our action requires that any runner handling our comment trigger has the Github "gh" cli tool installed - however all (non-self-hosted) runners will have this.
- Formal internal-test request data (i.e. external-side bot comment text) can be tailored for internal-side handshaking reqs as required.
- Security-related aspects for review include:
  - use of secrets
  - use of vanilla 'github.token'
  - use of the extra "pull_requests:write" permission in action
  - use of env vars in action's bash scripting
  - auth perms for internal-side bots
  - anything else?
