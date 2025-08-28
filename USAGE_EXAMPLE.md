# Example: How to Use the Automated Rebase Workflow in Your Repository

This document shows how to integrate the automated rebase workflow into your own repository.

## Basic Setup

### 1. Create a trigger workflow

Create `.github/workflows/automated-rebase.yml`:

```yaml
name: Automated Rebase

on:
  # Trigger when rebase config file is pushed
  push:
    paths:
      - '.github/rebase-config.yml'
  
  # Allow manual triggering for testing
  workflow_dispatch:
    inputs:
      target_branch:
        description: 'Branch to rebase (optional)'
        required: false
        type: string

permissions:
  contents: write

jobs:
  rebase:
    # Use the reusable rebase workflow from this repository
    uses: anatawa12/copilot-workspace/.github/workflows/rebase.yml@master
    with:
      branch: ${{ github.event.inputs.target_branch || '' }}
    secrets: inherit  # Required to pass REBASE_TOKEN if set
```

### 2. Optional: Configure custom token for event triggering

If you want the rebase to trigger other workflows (recommended):

1. Create a Personal Access Token with `Contents: Write` permission
2. Add it as a repository secret named `REBASE_TOKEN`
3. The workflow will automatically use it instead of `GITHUB_TOKEN`

## Usage Examples

### Example 1: Basic commit squashing

Create `.github/rebase-config.yml`:

```yaml
base_commit: "abc123def456789012345678901234567890abcd"
operations:
  - action: "pick"
    commit: "def456789012345678901234567890abcdef12"
  - action: "squash"
    commit: "789012345678901234567890abcdef123456"
    message: "Implement user authentication feature"
```

Commit and push this file to trigger the rebase.

### Example 2: Using with GitHub Copilot

Just ask GitHub Copilot:

> "Please squash my last 3 commits into one with a better commit message"

Copilot will analyze your git history and create the appropriate rebase configuration file.

## Token Behavior

- **With REBASE_TOKEN set**: Pushes will trigger other workflows (recommended)
- **Default (GITHUB_TOKEN only)**: Pushes won't trigger other workflows (GitHub security feature)

## Security Notes

- The workflow requires `contents: write` permission
- Only use on branches where force pushing is acceptable
- The rebase config file is automatically removed after completion
- Uses the commit author of the config file as the committer for rebased commits