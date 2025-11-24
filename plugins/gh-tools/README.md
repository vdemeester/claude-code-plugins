# GitHub CLI Tools Plugin

A comprehensive plugin providing skills and utilities for working with GitHub CLI (`gh`).

## Overview

This plugin provides expert guidance and patterns for using the GitHub CLI (`gh` command) to work with:
- Issues (listing, filtering, creating, managing)
- Pull Requests (listing, filtering, reviewing, merging)
- Workflows (monitoring, debugging, re-running)
- Labels (creating, managing, applying)
- Projects (listing, adding items)

## Skills

### gh-usage

Comprehensive guide for using the `gh` command line tool effectively. Includes extensive documentation on issue creation with templates, best practices, and patterns.

**When to use:**
- Need to list or filter GitHub issues or pull requests
- Want to check workflow run status or logs
- Need to work with labels on issues or PRs
- Want to interact with GitHub Projects
- Need to find failed workflows or PRs needing review
- Want to learn best practices for creating well-structured issues

**Example invocations:**
- "Show me all open PRs that need review"
- "List failed workflow runs for the current branch"
- "How do I filter issues by label?"
- "Get the status of workflows attached to this PR"
- "List all open issues assigned to me"
- "What's the best way to create a bug report?"
- "Show me how to create a feature request with gh"

**Issue Creation Coverage:**
- 5+ detailed issue templates (bug reports, feature requests, tasks, documentation)
- 4+ common patterns (from error logs, failed workflows, bulk creation, PR linking)
- Best practices and tips for writing effective issues
- Interactive vs command-line creation methods
- Using issue templates from repositories

## Agents

### issue-drafter

Specialized agent that helps you draft well-structured, comprehensive GitHub issues following best practices.

**When to use:**
- Want to create a new GitHub issue with proper structure
- Need help writing a bug report or feature request
- Want guidance on what information to include
- Need to gather context from the codebase for an issue

**What it does:**
1. **Understands your need**: Asks clarifying questions about the issue type and requirements
2. **Gathers context**: Optionally searches codebase for relevant errors, files, or related issues
3. **Drafts the issue**: Creates properly structured content using appropriate templates:
   - Bug reports with reproduction steps, environment, logs
   - Feature requests with user stories, benefits, implementation ideas
   - Tasks/chores with checklists and acceptance criteria
   - Documentation requests with scope and audience
4. **Refines with you**: Iterates based on your feedback
5. **Generates command**: Provides ready-to-use `gh issue create` command

**Example invocations:**
- "Help me create a bug report for the login issue"
- "Draft a feature request for dark mode"
- "I need to create an issue about the failing tests"
- "Create an issue from this error log"

**Example workflow:**
```
You: "Help me create a bug report for the login crash"

Agent: "I'll help you create a well-structured bug report.
        Let me ask a few questions:
        - What exactly happens when it crashes?
        - Can you reproduce it consistently?
        - Would you like me to check the codebase for related errors?"

[After gathering information]

Agent: "Here's the drafted issue:

**Title**: Login page crashes on form submission

**Body**: [Well-structured bug report with all sections]

**Command**:
gh issue create --title "..." --body-file /tmp/issue.md --label bug,high-priority

Would you like me to create this issue now?"
```

## Installation

This plugin is included in the `vdemeester-claude-code-plugins` bundle.

To use it separately, copy the `gh-tools` directory to your `.claude/plugins/` directory.

## Usage

Simply ask Claude Code to help you with GitHub CLI operations, and the `gh-usage` skill will be invoked automatically when appropriate:

```
"List all open PRs with the bug label"
"Show me failed workflow runs for the main branch"
"How do I add labels to an issue?"
```

Or explicitly invoke the skill:

```
Can you help me understand how to use gh to filter pull requests?
```

## Features

- **Issues**: Comprehensive coverage of listing, filtering, creating, and managing issues
  - 5+ detailed issue templates with best practices
  - Interactive issue creation assistance via `issue-drafter` agent
  - Patterns for creating issues from logs, failed workflows, and more
- **Pull Requests**: Complete PR workflow including creation, review, status checks, and merging
- **Workflows**: Monitor, debug, and manage GitHub Actions workflows and runs
- **Labels**: Create and manage repository labels
- **Projects**: Work with GitHub Projects V2
- **Advanced Patterns**: Common workflows like finding failed runs, monitoring PRs, exporting data
- **JSON/JQ Integration**: Examples using `--json` and `--jq` for scripting and automation
- **Specialized Agents**: Issue drafting agent for creating well-structured, comprehensive issues

## Requirements

- [GitHub CLI](https://cli.github.com/) (`gh`) must be installed
- Authenticated with GitHub: `gh auth login`

## Examples

### Create a Well-Structured Bug Report
```bash
gh issue create --title "Login fails with valid credentials" --body "$(cat <<'EOF'
## Bug Description
Users cannot log in even with correct username/password.

## Steps to Reproduce
1. Navigate to /login
2. Enter valid credentials
3. Click "Sign In"

## Expected Behavior
User should be logged in successfully.

## Actual Behavior
Login fails with "Invalid credentials" error.

## Environment
- Browser: Chrome 120
- OS: macOS 14.1

## Error Logs
TypeError: Cannot read property 'token' of undefined
EOF
)" --label bug --assignee @me
```

### Create Feature Request with Context
```bash
gh issue create --title "Add dark mode support" --body-file feature-request.md --label enhancement,ux --milestone "Q1 2024"
```

### List Failed Workflow Runs
```bash
gh run list --status failure --limit 20
```

### Get PR Status with All Checks
```bash
gh pr checks 456 --watch
```

### Find PRs Ready to Merge
```bash
gh pr list --search "is:open review:approved status:success draft:false"
```

### Create Issue from Error Log
```bash
ERROR_LOG=$(grep "ERROR" app.log | tail -20)
gh issue create \
  --title "Application errors in production" \
  --body "## Error Logs
\`\`\`
$ERROR_LOG
\`\`\`" \
  --label bug,production
```

### Export Issues to JSON
```bash
gh issue list --limit 1000 --json number,title,labels,state > issues.json
```

## Contributing

This plugin is part of the `vdemeester-claude-code-plugins` repository. Contributions welcome!

## License

See the main repository LICENSE file.
