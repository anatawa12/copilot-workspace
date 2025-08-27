# Copilot Workspace - Automated Git Rebase

This repository provides a GitHub Actions workflow that enables automated git rebase operations through a simple configuration file. This is particularly useful for GitHub Copilot to perform commit squashing and rebase operations.

## Features

- **Automated Rebase**: Trigger git rebase operations by committing a `.github/rebase-todo` file
- **Commit Squashing**: Combine multiple commits with custom commit messages
- **Reusable Workflow**: Use `workflow_call` to integrate into your existing workflows
- **Proper Attribution**: Maintains correct committer information based on who created the rebase-todo file
- **Automatic Cleanup**: Removes the rebase-todo file after successful completion

## Quick Start

1. Copy the `rebase.yml` workflow to your repository's `.github/workflows/` directory
2. Create a `.github/rebase-todo` file with your rebase instructions
3. Commit and push the file to trigger the automated rebase

## Rebase Todo File Format

Create a `.github/rebase-todo` file with this format:

```
# Base commit hash (the commit before the ones you want to modify)
a1b2c3d4e5f6789012345678901234567890abcd

# Operations (pick or squash with optional new message)
pick b2c3d4e5f6789012345678901234567890abcde
squash c3d4e5f6789012345678901234567890abcdef Fix authentication and add tests
s d4e5f6789012345678901234567890abcdef01 Refactor user service and update docs
```

### Supported Operations

- `pick` or `p`: Use the commit as-is
- `squash` or `s`: Combine with the previous commit, optionally with a new message

## Usage Examples

### Basic Commit Squashing

To squash the last 3 commits into one:

1. Get the base commit hash (4 commits ago):
   ```bash
   git log --oneline -4
   ```

2. Create `.github/rebase-todo`:
   ```
   <base-commit-hash>
   pick <first-commit-hash>
   squash <second-commit-hash> Combined feature implementation
   squash <third-commit-hash>
   ```

3. Commit and push the file

### With Workflow Call

Use the reusable workflow in your own workflows:

```yaml
name: My Custom Rebase

on:
  push:
    paths:
      - '.github/rebase-todo'

jobs:
  rebase:
    uses: ./.github/workflows/rebase.yml
    secrets: inherit
```

## Files in This Repository

- `/.github/workflows/rebase.yml` - Main reusable rebase workflow
- `/.github/workflows/example-rebase-usage.yml` - Example usage workflow
- `/REBASE_TODO_FORMAT.md` - Detailed format documentation
- `/README.md` - This file

## For GitHub Copilot Integration

This workflow is designed to work seamlessly with GitHub Copilot. When you need to squash commits:

1. Ask Copilot to create a rebase-todo file
2. Copilot can analyze your commit history and generate the appropriate format
3. The workflow automatically handles the rebase and cleanup

## Requirements

- Repository must have `contents: write` permissions for the workflow
- Use full 40-character SHA hashes for reliability
- Ensure you're working on a branch that can be force-pushed

## Important Notes

⚠️ **This workflow performs force pushes**. Only use on branches where force pushing is acceptable.

✅ **Automatic cleanup**: The rebase-todo file is removed after successful completion.

🔒 **Secure**: Uses the commit author of the rebase-todo file as the committer for rebased commits.