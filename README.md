# Rationale for 'internal testing access via comment triggers' workflow:

'Org contact user' supplies a list of login names and a unique org 'trigger string' (as per login names, this should contain alphanumeric characters and dashes)
- usage for trigger strings in comments is then: **/<trigger-string>**

Supplied user & trigger information is added to an action repository secret (currently **INTERNAL_TEST_LIST**) where users/trigger_strings are stored as follows:

    /user1/string1
    /user2/string1
    /user3/string2
    /user4/string2
    etc

- Each entry is space or newline separated.
- This registers users as an 'internal test requester' and binds each to a specific trigger string.

Org contact user is provided with an access token - details TBD but allowing: 
1. scanning of PR comments, 
2. PR comment-body reads and 
3. PR comment writes.

Registered user then requests an internal-test run by entering the string "/<trigger-string>" as a PR comment body.
Our 'internal-test-request comment' trigger then fires:
- checks that its a new comment (no edits or deletes)
- checks that its a PR (and not an Issue - Github internal quirk)
- checks that the user exists in the INTERNAL_TEST_LIST secret and that the trigger string used is assigned to that user.
- checks that the PR is open

If any checks fail the comment trigger stops and any remaining action jobs or steps will be silently skipped. The action will never fail.

If all checks pass the 'github-actions' bot then issues a formal 'internal-test request' as a comment including the following request data in the comment body: 

    /<trigger-string>
    PR reference (commit SHA) - again by Github quirk, this is not simple to find.
    registered requester @login_name (e.g. @user1)

Internal-test-side bot then scans for such comments, runs internal tests and reports pass/fail to the external PR as a comment
- (<Scott's stuff goes here>)

## Notes:

- Comments in PRs and Issues are "the same but different" so its sometimes necessary to distinguish between the two
- Only comment trigger actions contained in the mainline workflow files are used (they run "from the mainline" which partly explains why it can be tricky to get PR branch information e.g. PR commit SHA) 
- Currently our action requires that any runner handing our comment trigger has the Github "gh" cli tool installed - however all (non-self-hosted) runners will have this.
- Formal internal-test request data (i.e. external bot comment text) can be tailored for Internal-side handshaking reqs as required.
- Security-related aspects for review include:
  - use of secrets
  - use of vanilla 'github.token'
  - use of the extra "pull_requests:write" permission in action
  - use of env vars in action's bash scripting
  - anything else?
