# 'XInternal testing runs via comment triggers' - rationale and workflow:

As a first step, 'Org contact user' supplies a list of user names and a unique org comment 'trigger string' which should also conform to user name format and length (alphanumeric characters & dashes, 39 chars).
- usage for all Org user comment triggers is then: **/trigger-string**

Supplied user & trigger information is added to an action repository secret (currently **INTERNAL_TEST_LIST**) where users and comment triggers are stored as follows:

    /user1//trigger1 /user2//trigger1
    /user3//trigger2 /user4//trigger2
    etc

- Each entry is space(s) or newline(s) separated.
- Note that existing secret settings in Github are not visible when being updated so the internal test list content should be stored elsewhere (TBD e.g 1password??) and reapplied in full on each update.
- The list should be periodically audited to confirm that contents are still required.

This registers users as an 'internal-test requester' and binds each to a specific comment trigger e.g. '/trigger2' above.

Org contact user then defines a repo access token allowing:
1. scanning of PR comments, 
2. PR comment-body reads and 
3. PR comment writes.

Note that 'fine-grain' user PATs allow the individual setting of various 'repository permissions' including read/write "pull request" perms (pull requests and related comments, assignees, labels, milestones, and merges). These offer the Internal-side granularity and permissons required (and are used in our own Internal-side implementation).

Registered Org users then request an internal-test run by entering the comment trigger ("/trigger-string") as a PR comment body.
Our 'internal-test-request comment' trigger then fires:
- checks that its a new comment (no edits or deletes).
- checks that its a PR (and not an Issue - Github internal quirk).
- chcks that the comment has correct '/trigger_string' format.
- checks that the Org user exists in the INTERNAL_TEST_LIST and that the comment trigger used is assigned to that user.
- checks that the PR is open.

If any check fails the trigger stops and any remaining trigger action steps and jobs will be silently skipped. The action itself will never fail.

If all checks pass the External-side 'github-actions' bot then issues a validated 'internal-test request' as a comment which includes a body with request data in the following format:

    /trigger-string
    <PR reference> (commit SHA - which, again by Github quirk, is not simple to find.)
    <validated requester @user_name> (e.g. @user1)
    Codeplay Internal Testing: Request Validated

e.g.

    /verify 584e585 @alan-forbes-cp Codeplay Internal Testing: Request Validated

Internal-side bot (via access token) then scans for such comments, runs internal tests and reports final pass/fail to the External-side PR as a comment.
- Suggested 'best practice' for the Internal-side is to respond both with a "Started" comment and with a final "Completed: PASS|FAIL|DID NOT RUN|TIMED OUT|etc." comment, adopting the format used by the External-side. As a minimum, Internal-side should indicate test completion as a handshake between the sides.
- Note that Internal-side comments are not validated or policed by the External-side.

e.g. of Internal-side comments:

    /verify 584e585 @alan-forbes-cp Codeplay Internal Testing: Started
    /verify 584e585 @alan-forbes-cp Codeplay Internal Testing: Completed: PASS

## Notes:

- Comments in PRs and Issues are "the same but different" so its necessary to distinguish between the two in the action code.
- Only trigger actions contained in the mainline workflow files are used (they run "from the mainline" which partly explains why it can be tricky to get PR branch information e.g. PR commit SHA).
- Currently our action requires that any runner handling our trigger has the Github "gh" cli tool installed - however all (non-self-hosted) runners will have this.
- Formal internal-test request data (i.e. External-side bot comment text) can be tailored for Internal-side handshaking reqs as required.
- Security-related aspects for review include:
  - use of secrets
  - use of vanilla 'github.token'
  - use of the extra "pull_requests:write" permission in action
  - use of env vars in action's bash scripting
  - auth perms for Internal-side bots
  - anything else?
