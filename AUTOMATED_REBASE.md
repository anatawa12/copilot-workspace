# Automated Git Rebase Workflow

This document describes the automated git rebase workflow that enables GitHub Copilot and users to perform commit squashing and rebase operations through a simple configuration file.

## Features

- **Automated Rebase**: Trigger git rebase operations by committing a configuration file
- **Commit Squashing**: Combine multiple commits with custom commit messages
- **Multi-line Message Support**: Full support for complex commit messages with Co-authored-by lines
- **Reusable Workflow**: Use `workflow_call` to integrate into your existing workflows
- **Injection Safe**: Uses YAML parsing to prevent script injection attacks
- **Proper Attribution**: Maintains correct committer information based on who created the config file
- **Automatic Cleanup**: Removes the config file after successful completion

## Configuration Formats

### YAML Format (Recommended)

The new YAML format provides better reliability, multi-line message support, and injection safety:

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

### Legacy Text Format

The original text format is still supported for backward compatibility:

**File:** `.github/rebase-todo`

```
# Base commit hash
a1b2c3d4e5f6789012345678901234567890abcd

# Operations
pick b2c3d4e5f6789012345678901234567890abcde
squash c3d4e5f6789012345678901234567890abcdef Fix authentication and add tests
```

**📖 [View Text Format Documentation](REBASE_TODO_FORMAT.md)**

## Quick Start

### Using YAML Format (Recommended)

1. Copy the `rebase-v2.yml` workflow to your repository's `.github/workflows/` directory
2. Create a `.github/rebase-config.yml` file with your rebase instructions
3. Commit and push the file to trigger the automated rebase

### Using Legacy Text Format

1. Copy the `rebase.yml` workflow to your repository's `.github/workflows/` directory
2. Create a `.github/rebase-todo` file with your rebase instructions
3. Commit and push the file to trigger the automated rebase

## Workflow Files

- **`rebase-v2.yml`** - New workflow supporting YAML configuration format
- **`rebase.yml`** - Legacy workflow supporting text format
- **`example-rebase-usage.yml`** - Example showing how to use the workflows

## Usage Examples

### Basic Commit Squashing (YAML)

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
      - '.github/rebase-config.yml'  # For YAML format
      # - '.github/rebase-todo'      # For text format

jobs:
  rebase:
    uses: ./.github/workflows/rebase-v2.yml  # For YAML format
    # uses: ./.github/workflows/rebase.yml   # For text format
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

🆕 **YAML format recommended**: Provides better parsing, multi-line support, and injection safety.

## Migration from Text to YAML Format

If you're using the legacy text format, consider migrating to YAML for better features:

**Text format:**
```
a1b2c3d4e5f6789012345678901234567890abcd
pick b2c3d4e5f6789012345678901234567890abcde
squash c3d4e5f6789012345678901234567890abcdef Fix auth
```

**YAML format:**
```yaml
base_commit: "a1b2c3d4e5f6789012345678901234567890abcd"
operations:
  - action: "pick"
    commit: "b2c3d4e5f6789012345678901234567890abcde"
  - action: "squash" 
    commit: "c3d4e5f6789012345678901234567890abcdef"
    message: |
      Fix authentication system
      
      - Resolve session handling issues
      - Add comprehensive test coverage
      - Update documentation
      
      Co-authored-by: Team Member <team@example.com>
```

## Files in This Repository

- `/.github/workflows/rebase-v2.yml` - Main reusable rebase workflow (YAML format)
- `/.github/workflows/rebase.yml` - Legacy rebase workflow (text format)
- `/.github/workflows/example-rebase-usage.yml` - Example usage workflow
- `/REBASE_CONFIG_YAML.md` - YAML format documentation
- `/REBASE_TODO_FORMAT.md` - Legacy text format documentation
- `/AUTOMATED_REBASE.md` - This file