# Claude Code Plugins

A collection of plugins for [Claude Code](https://claude.com/claude-code) to enhance your development workflow.

## Plugins

### Git Commit

Create thoughtful commit messages that focus on **why** changes were made, not just what changed.

**What makes it different**:

- Systematic file-by-file review to ensure commit coherence
- Commit messages that explain **impact and reasoning** (the "why")
- Conventional commits with proper Claude Code attribution
- Parallel git operations for speed and efficiency
- Smart push offers (only when NOT on main/master)

**Usage**:

```
/git-commit:commit
```

The plugin enhances Claude Code's native git workflow with structured review, better messaging, and safety gates. Perfect for maintaining clean, meaningful commit history.

### Weekly Review

Generate comprehensive weekly summaries of your work, automatically categorized by work type. **Prioritizes GitHub PRs** when available and falls back to git commits.

**What it analyzes**:

- Merged GitHub pull requests (preferred) or git commits from past 7-10 days
- PR labels, titles, and descriptions for intelligent categorization
- Automatic categorization into tech debt, bug fixes, and new features
- Statistics on PRs merged, commits made, files changed, and lines modified

**Usage**:

```
/weekly-review:weekly-review        # Review past 7 days
/weekly-review:weekly-review 10     # Review past 10 days
```

The plugin generates a structured report with:

- Summary of accomplishments (2-5 bullet points)
- Work breakdown by category with percentages
- Detailed analysis of tech debt, bug fixes, and new functionality
- Links to merged PRs (GitHub mode) or commit timeline (git mode)

**Features**:

- Automatically detects GitHub CLI availability
- Uses PR labels and descriptions for better categorization
- Read-only operation - never modifies your code or git history


## Installation

Install this plugin collection from the shell:

```
claude plugin marketplace add vdemeester/claude-code-plugins
```

To install specific plugins:

```
claude plugin install git-commit
claude plugin install weekly-review
```

## Plugin Structure

Each plugin is located in the `plugins/` directory and includes:

- `.claude-plugin/plugin.json` - Plugin metadata
- `commands/` - Slash commands for user interaction
- `agents/` - Specialized agents for specific tasks

## Plugin Development

Want to create your own Claude Code plugins? Check out [CLAUDE.md](CLAUDE.md) for a comprehensive guide covering:

- Plugin design principles (safety, transparency, context-awareness)
- Command design patterns (simple, multi-phase, agent-based)
- Multi-phase workflow design with user confirmation
- Agent design and coordination
- User experience best practices
- Safety patterns and error handling
- Reporting and transparency strategies
- Common patterns and anti-patterns to avoid

The guide is based on real-world experience designing the plugins in this repository.

## Contributing

Plugins are defined in `.claude-plugin/marketplace.json`. To add a new plugin, create a directory in `plugins/` following the existing structure.

## Author

Vincent Demeester - <vincent@sbr.pm>

## License

[Apache-2.0 License](LICENSE)
