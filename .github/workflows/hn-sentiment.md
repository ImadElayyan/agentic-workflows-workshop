---
# Trigger - when should this workflow run?
on:
  issue_comment:
    types: [created]

if: startsWith(github.event.comment.body, '/hn-sentiment')

# Alternative triggers (uncomment to use):
# on:
#   issues:
#     types: [opened, reopened]
#   pull_request:
#     types: [opened, synchronize]
#   schedule: daily  # Fuzzy daily schedule (scattered execution time)
#   # schedule: weekly on monday  # Fuzzy weekly schedule

# Permissions - what can this workflow access?
# Write operations (creating issues, PRs, comments, etc.) are handled
# automatically by the safe-outputs job with its own scoped permissions.
permissions:
  contents: read
  issues: read
  pull-requests: read

# Tools
tools:
  bash: false
  cli-proxy: false
  web-fetch: {}
#   github:
#     toolsets: [default]

# Network access
network:
  allowed:
    - defaults
    - hacker-news.firebaseio.com

# Outputs - what APIs and tools can the AI use?
safe-outputs:
  add-comment:
    max: 1
  # actions:
  # activation-comments:
  # add-comment:
  # add-labels:
  # add-reviewer:
  # ado-assign-work-item:
  # ado-comment-on-work-item:
  # ado-create-work-item:
  # ado-link-work-items:
  # ado-update-work-item:
  # ado-upload-workitem-attachment:
  # allowed-github-references:
  # approve-workflow-run:
  # assign-milestone:
  # assign-to-agent:
  # assign-to-user:
  # autofix-code-scanning-alert:
  # call-workflow:
  # close-discussion:
  # close-issue:
  # close-pull-request:
  # concurrency-group:
  # create-agent-session:
  # create-agent-task:
  # create-check-run:
  # create-code-scanning-alert:
  # create-discussion:
  # create-project:
  # create-project-status-update:
  # create-pull-request:
  # create-pull-request-review-comment:
  # dismiss-pull-request-review:
  # dismiss-review:
  # dispatch-repository:
  # dispatch-workflow:
  # dispatch_repository:
  # environment:
  # failure-issue-repo:
  # footer:
  # group-reports:
  # hide-comment:
  # id-token:
  # jira-add-comment:
  # jira-add-label:
  # jira-create-issue:
  # jira-update-issue:
  # linear-add-comment:
  # linear-create-issue:
  # linear-token:
  # linear-update-issue:
  # link-sub-issue:
  # mark-pull-request-as-ready-for-review:
  # max-bot-mentions:
  # max-patch-files:
  # mentions:
  # merge-pull-request:
  # missing-data:
  # missing-tool:
  # noop:
  # push-to-pull-request-branch:
  # remove-labels:
  # replace-label:
  # reply-to-pull-request-review-comment:
  # report-failed-jobs:
  # report-failure-as-issue:
  # report-incomplete:
  # resolve-pull-request-review-thread:
  # scripts:
  # set-issue-field:
  # set-issue-type:
  # steer:
  # steps:
  # submit-pull-request-review:
  # threat-detection:
  # unassign-from-user:
  # update-discussion:
  # update-issue:
  # update-project:
  # update-pull-request:
  # update-release:
  # upload-artifact:
  # upload-asset:
  # upload-code-coverage:
  # urls:

---

# hn-sentiment

Create a ChatOps slash command called /hn-sentiment. When a user posts
a comment on a GitHub issue that starts with "/hn-sentiment <url>",
where <url> is a Hacker News story URL (e.g.
https://news.ycombinator.com/item?id=12345), do the following:
1) Extract the Hacker News item ID from the URL.
2) Use the built-in `web_fetch` tool, not shell commands such as `curl`
   or `wget`, to fetch up to 50 top-level comments for that story from
   the Hacker News API.
3) Perform sentiment analysis on the comment text, classifying each
   comment as Positive, Negative, or Neutral.
4) Produce a summary that shows: the overall sentiment (with percentage
   breakdown), the top 3 most positive comments (with excerpt), and
   the top 3 most negative comments (with excerpt).
5) Reply to the original issue comment with the analysis formatted in
   Markdown.
If no URL is provided or the URL is not a valid Hacker News item,
reply with a helpful error message.
