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

Comprehensive guide for using the `gh` command line tool effectively.

**When to use:**
- Need to list or filter GitHub issues or pull requests
- Want to check workflow run status or logs
- Need to work with labels on issues or PRs
- Want to interact with GitHub Projects
- Need to find failed workflows or PRs needing review

**Example invocations:**
- "Show me all open PRs that need review"
- "List failed workflow runs for the current branch"
- "How do I filter issues by label?"
- "Get the status of workflows attached to this PR"
- "List all open issues assigned to me"

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
- **Pull Requests**: Complete PR workflow including creation, review, status checks, and merging
- **Workflows**: Monitor, debug, and manage GitHub Actions workflows and runs
- **Labels**: Create and manage repository labels
- **Projects**: Work with GitHub Projects V2
- **Advanced Patterns**: Common workflows like finding failed runs, monitoring PRs, exporting data
- **JSON/JQ Integration**: Examples using `--json` and `--jq` for scripting and automation

## Requirements

- [GitHub CLI](https://cli.github.com/) (`gh`) must be installed
- Authenticated with GitHub: `gh auth login`

## Examples

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

### Export Issues to JSON
```bash
gh issue list --limit 1000 --json number,title,labels,state > issues.json
```

## Contributing

This plugin is part of the `vdemeester-claude-code-plugins` repository. Contributions welcome!

## License

See the main repository LICENSE file.
