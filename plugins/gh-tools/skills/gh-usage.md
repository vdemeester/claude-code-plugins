---
description: This skill should be used when the user needs to work with GitHub CLI (gh) for issues, pull requests, workflows, labels, or projects. Examples include "list open PRs", "check workflow status", "filter issues by label", "get PR details", "list failed workflows".
---

# GitHub CLI (gh) Usage Guide

Comprehensive guide for using the `gh` command line tool to work with GitHub issues, pull requests, workflows, labels, and projects.

---

## Table of Contents

1. [Issues](#issues)
2. [Pull Requests](#pull-requests)
3. [Workflows](#workflows)
4. [Labels](#labels)
5. [Projects](#projects)
6. [Common Patterns](#common-patterns)

---

## Issues

### Listing Issues

**List all open issues:**
```bash
gh issue list
```

**List all issues (including closed):**
```bash
gh issue list --state all
```

**List only closed issues:**
```bash
gh issue list --state closed
```

**Limit number of results:**
```bash
gh issue list --limit 50
```

### Filtering Issues

**Filter by assignee:**
```bash
gh issue list --assignee @me
gh issue list --assignee username
gh issue list --assignee ""  # unassigned issues
```

**Filter by author:**
```bash
gh issue list --author username
```

**Filter by label:**
```bash
gh issue list --label bug
gh issue list --label "bug,help wanted"  # multiple labels (AND logic)
```

**Filter by milestone:**
```bash
gh issue list --milestone "v1.0"
```

**Search with query:**
```bash
gh issue list --search "error in:title"
gh issue list --search "is:open is:issue assignee:@me"
```

### Issue Details

**View issue details:**
```bash
gh issue view 123
gh issue view 123 --web  # open in browser
```

**View issue with comments:**
```bash
gh issue view 123 --comments
```

**Get JSON output for scripting:**
```bash
gh issue view 123 --json number,title,state,labels,assignees
```

### Creating and Managing Issues

#### Basic Issue Creation

**Simple issue creation:**
```bash
gh issue create --title "Bug report" --body "Description"
gh issue create --title "Feature request" --label enhancement --assignee @me
```

**Interactive issue creation (recommended for detailed issues):**
```bash
gh issue create
# Opens editor for title and body
# Prompts for labels, assignees, milestone, projects
```

**Create with multiple options:**
```bash
gh issue create \
  --title "Add user authentication" \
  --body "Need to implement JWT-based auth for API security" \
  --label "enhancement,security" \
  --assignee @me \
  --milestone "v2.0" \
  --project "Backend Improvements"
```

#### Issue Creation Best Practices

**1. Use Issue Templates**

If your repository has issue templates, you can use them:

```bash
# Interactive selection from available templates
gh issue create

# Use specific template
gh issue create --template bug_report.md
gh issue create --template feature_request.md
```

**2. Create Issue from File**

For complex issues with detailed descriptions:

```bash
# Create issue-body.md with your content
gh issue create --title "Complex feature proposal" --body-file issue-body.md
```

Example `issue-body.md`:
```markdown
## Problem Statement
Users cannot reset their passwords when logged out.

## Proposed Solution
Add a "Forgot Password" flow with email verification.

## Implementation Details
- Add password reset endpoint
- Integrate with email service
- Create reset token with 1-hour expiry

## Acceptance Criteria
- [ ] User receives reset email within 1 minute
- [ ] Token expires after 1 hour
- [ ] User can set new password successfully
```

**3. Well-Structured Bug Reports**

```bash
gh issue create --title "Login fails with valid credentials" --body "$(cat <<'EOF'
## Bug Description
Users cannot log in even with correct username/password.

## Steps to Reproduce
1. Navigate to /login
2. Enter valid credentials
3. Click "Sign In"
4. Error: "Invalid credentials"

## Expected Behavior
User should be logged in and redirected to dashboard.

## Actual Behavior
Login fails with error message despite correct credentials.

## Environment
- Browser: Chrome 120
- OS: macOS 14.1
- App version: 2.3.4

## Additional Context
Error appears in console: "TypeError: Cannot read property 'token' of undefined"

## Logs
```
[2024-01-15 10:23:45] POST /api/login 500 Internal Server Error
```
EOF
)" --label bug --assignee @me
```

**4. Feature Requests with Context**

```bash
gh issue create --title "Add dark mode support" --body "$(cat <<'EOF'
## Feature Description
Add dark mode theme option to improve user experience in low-light environments.

## User Story
As a user who works at night, I want a dark mode option so that I can reduce eye strain.

## Proposed Implementation
- Add theme toggle in settings
- Store preference in localStorage
- Apply dark theme CSS variables
- Support system preference detection

## Benefits
- Reduced eye strain for users
- Better battery life on OLED screens
- Modern UX expectation

## Alternatives Considered
- Browser extension (rejected: not all users can install extensions)
- System theme only (rejected: users want manual control)

## References
- [Material Design Dark Theme](https://material.io/design/color/dark-theme.html)
- Similar feature in competitor apps
EOF
)" --label enhancement,ux --milestone "Q1 2024"
```

**5. Task/Chore Issues**

```bash
gh issue create --title "Update dependencies to latest versions" --body "$(cat <<'EOF'
## Task
Update all npm dependencies to resolve security vulnerabilities.

## Checklist
- [ ] Update React to v18.2.0
- [ ] Update Express to v4.18.2
- [ ] Update testing libraries
- [ ] Run full test suite
- [ ] Update documentation

## Dependencies to Update
- react: 17.0.2 → 18.2.0
- express: 4.17.1 → 4.18.2
- jest: 28.0.0 → 29.3.1

## Security Notes
2 high-severity vulnerabilities will be resolved.
EOF
)" --label chore,security
```

#### Issue Creation Patterns

**Pattern 1: Create Issue from Error Log**

```bash
# Capture error and create issue
ERROR_LOG=$(grep "ERROR" app.log | tail -20)
gh issue create \
  --title "Application errors in production" \
  --body "$(cat <<EOF
## Error Summary
Multiple errors detected in production logs.

## Error Logs
\`\`\`
$ERROR_LOG
\`\`\`

## Investigation Needed
- Identify root cause
- Implement fix
- Add monitoring
EOF
)" --label bug,production
```

**Pattern 2: Create Issue from Failed Workflow**

```bash
# After identifying failed workflow
gh issue create \
  --title "CI/CD pipeline failing on main branch" \
  --body "Workflow run https://github.com/owner/repo/actions/runs/123456 failed. \
Investigate test failures in auth module." \
  --label ci,bug \
  --assignee @me
```

**Pattern 3: Bulk Issue Creation from CSV/List**

```bash
# For creating multiple related issues
while IFS=',' read -r title body label; do
  gh issue create --title "$title" --body "$body" --label "$label"
done < issues.csv
```

**Pattern 4: Create Issue and Link to PR**

```bash
# Create issue, then reference in PR
ISSUE_NUM=$(gh issue create --title "Refactor auth module" --body "See PR for details" --label refactor --json number --jq .number)
echo "Created issue #$ISSUE_NUM"

# Later when creating PR
gh pr create --title "Refactor auth module" --body "Fixes #$ISSUE_NUM"
```

#### Managing Issues

**Close an issue:**
```bash
gh issue close 123
gh issue close 123 --comment "Fixed in PR #456"
gh issue close 123 --reason "completed"  # or "not planned"
```

**Reopen an issue:**
```bash
gh issue reopen 123
gh issue reopen 123 --comment "Issue persists, reopening for investigation"
```

**Edit an issue:**
```bash
gh issue edit 123 --title "Updated title"
gh issue edit 123 --body "Updated description"
gh issue edit 123 --add-label "help wanted"
gh issue edit 123 --remove-label "wontfix"
gh issue edit 123 --add-assignee user1,user2
gh issue edit 123 --remove-assignee user3
gh issue edit 123 --milestone "v2.0"
```

**Pin an issue:**
```bash
gh issue pin 123
gh issue unpin 123
```

**Lock/Unlock an issue:**
```bash
gh issue lock 123 --reason "spam"  # reasons: spam, off-topic, too heated, resolved
gh issue unlock 123
```

**Transfer issue to another repo:**
```bash
gh issue transfer 123 --repo owner/other-repo
```

**Add comment to issue:**
```bash
gh issue comment 123 --body "Update: bug confirmed, working on fix"
gh issue comment 123 --body-file update.md
```

#### Issue Templates

**Using Repository Issue Templates**

Many repositories have `.github/ISSUE_TEMPLATE/` with predefined templates:

```bash
# List available templates
ls .github/ISSUE_TEMPLATE/

# Create issue with specific template
gh issue create --template bug_report.yml
gh issue create --template feature_request.yml
gh issue create --template custom_template.md
```

**Common Template Types:**
- `bug_report.md` - For bug reports
- `feature_request.md` - For feature requests
- `question.md` - For questions
- `custom.md` - For other types

#### Issue Creation Tips

**DO:**
- ✅ Write clear, descriptive titles
- ✅ Include reproduction steps for bugs
- ✅ Add relevant labels and assignees
- ✅ Link to related issues or PRs
- ✅ Use code blocks for logs/errors
- ✅ Include environment details for bugs
- ✅ Add acceptance criteria for features
- ✅ Use checklists for tasks

**DON'T:**
- ❌ Create duplicate issues (search first)
- ❌ Use vague titles like "It doesn't work"
- ❌ Omit reproduction steps
- ❌ Mix multiple unrelated issues
- ❌ Skip labeling (makes triaging harder)
- ❌ Forget to assign if you know who should handle it

---

## Pull Requests

### Listing Pull Requests

**List all open PRs:**
```bash
gh pr list
```

**List all PRs (including closed/merged):**
```bash
gh pr list --state all
gh pr list --state closed
gh pr list --state merged
```

**Limit number of results:**
```bash
gh pr list --limit 50
```

### Filtering Pull Requests

**Filter by author:**
```bash
gh pr list --author @me
gh pr list --author username
```

**Filter by assignee:**
```bash
gh pr list --assignee @me
gh pr list --assignee username
```

**Filter by label:**
```bash
gh pr list --label "ready for review"
gh pr list --label "bug,needs-testing"
```

**Filter by base branch:**
```bash
gh pr list --base main
gh pr list --base develop
```

**Filter by head branch:**
```bash
gh pr list --head feature-branch
```

**Search with query:**
```bash
gh pr list --search "is:pr is:open review:required"
gh pr list --search "is:pr is:open draft:false"
```

### Pull Request Details

**View PR details:**
```bash
gh pr view 456
gh pr view 456 --web  # open in browser
```

**View PR with comments:**
```bash
gh pr view 456 --comments
```

**Get JSON output:**
```bash
gh pr view 456 --json number,title,state,isDraft,mergeable,reviews,statusCheckRollup
```

**Check PR status:**
```bash
gh pr status  # shows PRs related to current branch
```

**View PR diff:**
```bash
gh pr diff 456
gh pr diff 456 --patch  # in patch format
```

**List PR checks/status:**
```bash
gh pr checks 456
gh pr checks  # for current branch
```

### Creating and Managing Pull Requests

**Create a PR:**
```bash
gh pr create --title "Add feature" --body "Description"
gh pr create --draft  # create as draft
gh pr create --base develop  # target different base branch
```

**Create PR and assign reviewers:**
```bash
gh pr create --title "Fix bug" --reviewer user1,user2 --assignee @me
```

**Mark PR as ready for review:**
```bash
gh pr ready 456
```

**Merge a PR:**
```bash
gh pr merge 456
gh pr merge 456 --squash  # squash merge
gh pr merge 456 --rebase  # rebase merge
gh pr merge 456 --merge   # merge commit
```

**Close a PR without merging:**
```bash
gh pr close 456
```

**Reopen a PR:**
```bash
gh pr reopen 456
```

---

## Workflows

### Listing Workflows

**List all workflows in the repository:**
```bash
gh workflow list
```

**View workflow runs:**
```bash
gh run list
gh run list --limit 50
```

**Filter workflow runs by status:**
```bash
gh run list --status completed
gh run list --status failure
gh run list --status in_progress
gh run list --status success
gh run list --status cancelled
```

**Filter by workflow name:**
```bash
gh run list --workflow "CI"
gh run list --workflow ".github/workflows/test.yml"
```

**Filter by branch:**
```bash
gh run list --branch main
gh run list --branch feature-branch
```

**Filter by event:**
```bash
gh run list --event push
gh run list --event pull_request
gh run list --event schedule
```

**Filter by user:**
```bash
gh run list --user username
```

### Workflow Run Details

**View workflow run details:**
```bash
gh run view 123456
gh run view 123456 --web  # open in browser
```

**View workflow run with logs:**
```bash
gh run view 123456 --log
gh run view 123456 --log-failed  # only failed jobs
```

**Get JSON output:**
```bash
gh run view 123456 --json status,conclusion,workflowName,headBranch,event
```

**Watch a running workflow:**
```bash
gh run watch 123456
```

**Download workflow run artifacts:**
```bash
gh run download 123456
gh run download 123456 --name artifact-name
```

### Workflows Attached to Pull Requests

**View PR checks (includes workflow runs):**
```bash
gh pr checks 456
gh pr checks 456 --watch  # watch in real-time
```

**Get detailed PR check information with JSON:**
```bash
gh pr view 456 --json statusCheckRollup
```

**Example JSON query to get workflow details for a PR:**
```bash
gh pr view 456 --json number,statusCheckRollup --jq '.statusCheckRollup[] | select(.workflowRun) | {name: .name, conclusion: .conclusion, status: .status, url: .detailsUrl}'
```

**List all runs for the PR's head branch:**
```bash
# First get the PR's head branch
HEAD_BRANCH=$(gh pr view 456 --json headRefName --jq .headRefName)
# Then list runs for that branch
gh run list --branch "$HEAD_BRANCH"
```

**Get the latest workflow run for a PR:**
```bash
gh pr view 456 --json headRefName --jq .headRefName | xargs -I {} gh run list --branch {} --limit 1
```

### Managing Workflows

**Trigger a workflow manually:**
```bash
gh workflow run "CI"
gh workflow run .github/workflows/deploy.yml
gh workflow run deploy.yml --ref main  # on specific ref
```

**Re-run a failed workflow:**
```bash
gh run rerun 123456
gh run rerun 123456 --failed  # only failed jobs
```

**Cancel a running workflow:**
```bash
gh run cancel 123456
```

**Delete a workflow run:**
```bash
gh run delete 123456
```

---

## Labels

### Listing Labels

**List all repository labels:**
```bash
gh label list
```

**Limit number of results:**
```bash
gh label list --limit 100
```

**Get JSON output:**
```bash
gh label list --json name,description,color
```

### Creating and Managing Labels

**Create a label:**
```bash
gh label create "bug" --description "Something isn't working" --color FF0000
```

**Edit a label:**
```bash
gh label edit "bug" --description "Updated description"
gh label edit "bug" --color 00FF00
gh label edit "bug" --name "defect"  # rename
```

**Delete a label:**
```bash
gh label delete "old-label"
```

### Using Labels with Issues and PRs

**Add labels to an issue:**
```bash
gh issue edit 123 --add-label "bug,help wanted"
```

**Remove labels from an issue:**
```bash
gh issue edit 123 --remove-label "wontfix"
```

**Add labels to a PR:**
```bash
gh pr edit 456 --add-label "ready for review"
```

**Remove labels from a PR:**
```bash
gh pr edit 456 --remove-label "work in progress"
```

---

## Projects

**Note:** GitHub Projects V2 has a different API structure. The `gh` CLI has evolving support for projects.

### Listing Projects

**List organization projects:**
```bash
gh project list --owner org-name
```

**List user projects:**
```bash
gh project list --owner username
```

**Get project details:**
```bash
gh project view 1 --owner org-name
```

### Working with Project Items

**Add an issue to a project:**
```bash
gh project item-add 1 --owner org-name --url https://github.com/org/repo/issues/123
```

**Add a PR to a project:**
```bash
gh project item-add 1 --owner org-name --url https://github.com/org/repo/pull/456
```

**List project items:**
```bash
gh project item-list 1 --owner org-name
gh project item-list 1 --owner org-name --limit 50
```

**Get JSON output for project items:**
```bash
gh project item-list 1 --owner org-name --format json
```

### Project Fields and Values

**Note:** For advanced project field manipulation, you may need to use the GitHub GraphQL API via:
```bash
gh api graphql -f query='...'
```

**Example: Query project fields:**
```bash
gh api graphql -f query='
  query($org: String!, $number: Int!) {
    organization(login: $org) {
      projectV2(number: $number) {
        fields(first: 20) {
          nodes {
            ... on ProjectV2Field {
              id
              name
            }
            ... on ProjectV2SingleSelectField {
              id
              name
              options {
                id
                name
              }
            }
          }
        }
      }
    }
  }
' -f org='org-name' -F number=1
```

---

## Common Patterns

### Pattern 1: Find All Failed Workflow Runs

```bash
# Recent failed runs
gh run list --status failure --limit 20

# Failed runs for specific workflow
gh run list --workflow "CI" --status failure
```

### Pattern 2: Get PR Status with All Checks

```bash
# Simple check status
gh pr checks 456

# Detailed JSON output
gh pr view 456 --json statusCheckRollup,reviews --jq '
{
  checks: [.statusCheckRollup[] | {name: .name, conclusion: .conclusion}],
  reviews: [.reviews[] | {author: .author.login, state: .state}]
}
'
```

### Pattern 3: List My Open PRs Needing Review

```bash
gh pr list --author @me --search "is:open review:required"
```

### Pattern 4: Monitor Workflow Run in Real-Time

```bash
# Watch a specific run
gh run watch 123456

# Watch PR checks
gh pr checks 456 --watch
```

### Pattern 5: Get All Open Issues by Label

```bash
gh issue list --label bug --state open --json number,title,createdAt
```

### Pattern 6: Find PRs That Are Ready to Merge

```bash
gh pr list --search "is:open review:approved status:success draft:false"
```

### Pattern 7: List Workflow Runs for Current Branch

```bash
CURRENT_BRANCH=$(git branch --show-current)
gh run list --branch "$CURRENT_BRANCH"
```

### Pattern 8: Export Issues to JSON for Analysis

```bash
gh issue list --limit 1000 --json number,title,labels,state,createdAt,closedAt,assignees > issues.json
```

### Pattern 9: Find Recently Merged PRs

```bash
gh pr list --state merged --limit 20
```

### Pattern 10: Get Workflow Run Logs for Debugging

```bash
# View logs for specific run
gh run view 123456 --log

# Only failed job logs
gh run view 123456 --log-failed

# Download logs locally
gh run download 123456
```

---

## Advanced Usage

### Using JQ with JSON Output

The `gh` CLI supports `--json` and `--jq` for powerful filtering:

**Get specific fields from PRs:**
```bash
gh pr list --json number,title,author,labels --jq '.[] | select(.author.login == "username")'
```

**Find PRs with specific label:**
```bash
gh pr list --json number,title,labels --jq '.[] | select(.labels[].name == "bug")'
```

**Count issues by label:**
```bash
gh issue list --limit 1000 --json labels --jq '[.[].labels[].name] | group_by(.) | map({label: .[0], count: length})'
```

### Using GitHub API Directly

For operations not supported by `gh` subcommands:

```bash
# GET request
gh api repos/{owner}/{repo}/issues

# POST request
gh api repos/{owner}/{repo}/issues -f title="New issue" -f body="Description"

# GraphQL
gh api graphql -f query='query { viewer { login } }'
```

---

## Best Practices

1. **Use `--json` for scripting**: When parsing output in scripts, always use `--json` for stable, parseable output
2. **Combine with `--jq`**: Filter JSON output using jq queries for precise data extraction
3. **Limit results**: Use `--limit` to avoid overwhelming output for large repositories
4. **Use search queries**: The `--search` flag supports GitHub's powerful search syntax
5. **Check authentication**: Ensure you're authenticated with `gh auth status`
6. **Use `--web` for detailed views**: When you need to interact with the UI, use `--web` to open in browser

---

## Troubleshooting

**Check authentication status:**
```bash
gh auth status
```

**Login/re-authenticate:**
```bash
gh auth login
```

**Check gh version:**
```bash
gh --version
```

**Enable debug output:**
```bash
GH_DEBUG=1 gh pr list
```

**Get help for any command:**
```bash
gh issue --help
gh pr --help
gh workflow --help
```

---

## Reference Links

- [GitHub CLI Manual](https://cli.github.com/manual/)
- [GitHub Search Syntax](https://docs.github.com/en/search-github/searching-on-github)
- [GitHub GraphQL API](https://docs.github.com/en/graphql)

---

**Remember:** When using `gh` commands:
- Always check authentication first
- Use `--help` to explore available options
- Combine with standard Unix tools (grep, awk, jq) for powerful workflows
- Consider using `--json` with `--jq` for complex filtering
- Use `--web` when you need the full GitHub UI experience
