# Automated Git Rebase Workflow

This document describes the automated git rebase workflow that enables GitHub Copilot and users to perform commit squashing and rebase operations through a simple configuration file.

## Key Features

- **Automated commit squashing and rebase operations**: Trigger git rebase operations by committing a YAML configuration file
- **GitHub Copilot integration**: Designed to work seamlessly with GitHub Copilot for intelligent commit management
- **YAML configuration format with multi-line message support**: Full support for complex commit messages with Co-authored-by lines
- **Injection-safe parsing and execution**: Uses YAML parsing and environment variables to prevent script injection attacks
- **Automatic cleanup and proper attribution**: Maintains correct committer information and removes config files after completion
- **Reusable Workflow**: Use `workflow_call` to integrate into your existing workflows

## Configuration Format

### YAML Format

The workflow uses a YAML configuration format that provides reliability, multi-line message support, and injection safety:

**File:** `.github/rebase-config.yml`

```yaml
base_commit: "a1b2c3d4e5f6789012345678901234567890abcd"
operations:
  - action: "pick"
    commit: "b2c3d4e5f6789012345678901234567890abcde"
  - action: "squash"
    commit: "c3d4e5f6789012345678901234567890abcdef"
    message: |
      Fix authentication and add comprehensive tests
      
      - Add unit tests for login flow
      - Fix password validation bug
      - Add integration tests
      
      Co-authored-by: Jane Smith <jane@example.com>
```

**📖 [View YAML Format Documentation](REBASE_CONFIG_YAML.md)**

## Quick Start

1. Copy the `rebase-v2.yml` workflow to your repository's `.github/workflows/` directory
2. Create a `.github/rebase-config.yml` file with your rebase instructions
3. Commit and push the file to trigger the automated rebase

## Workflow Files

- **`rebase-v2.yml`** - Main reusable rebase workflow supporting YAML configuration
- **`example-rebase-usage.yml`** - Example showing how to use the workflow

## Usage Examples

### Basic Commit Squashing

To squash the last 3 commits into one:

1. Get the base commit hash (4 commits ago):
   ```bash
   git log --oneline -4
   ```

2. Create `.github/rebase-config.yml`:
   ```yaml
   base_commit: "<base-commit-hash>"
   operations:
     - action: "pick"
       commit: "<first-commit-hash>"
     - action: "squash"
       commit: "<second-commit-hash>"
       message: "Combined feature implementation"
     - action: "squash"
       commit: "<third-commit-hash>"
   ```

3. Commit and push the file

### With Workflow Call

Use the reusable workflow in your own workflows:

```yaml
name: My Custom Rebase

on:
  push:
    paths:
      - '.github/rebase-config.yml'

jobs:
  rebase:
    uses: ./.github/workflows/rebase-v2.yml
    secrets: inherit
```

## For GitHub Copilot Integration

This workflow is designed to work seamlessly with GitHub Copilot. When you need to squash commits:

1. Ask Copilot to create a rebase configuration file
2. Copilot can analyze your commit history and generate the appropriate format
3. The workflow automatically handles the rebase and cleanup

## Requirements

- Repository must have `contents: write` permissions for the workflow
- Use full 40-character SHA hashes for reliability
- Ensure you're working on a branch that can be force-pushed

## Important Notes

⚠️ **This workflow performs force pushes**. Only use on branches where force pushing is acceptable.

✅ **Automatic cleanup**: The config file is removed after successful completion.

🔒 **Secure**: Uses the commit author of the config file as the committer for rebased commits.

🆕 **Modern YAML format**: Provides reliable parsing, multi-line support, and injection safety.

## Files in This Repository

- **[AUTOMATED_REBASE.md](AUTOMATED_REBASE.md)** - Complete documentation for the automated rebase workflow
- **[REBASE_CONFIG_YAML.md](REBASE_CONFIG_YAML.md)** - YAML configuration format specification
- **[copilot-instructions.md](copilot-instructions.md)** - GitHub Copilot integration guide
- **`.github/workflows/rebase-v2.yml`** - Main reusable rebase workflow
- **`.github/workflows/example-rebase-usage.yml`** - Example usage workflow