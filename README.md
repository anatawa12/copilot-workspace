# Copilot Workspace

This repository contains various tools and workflows for enhancing development workflows, particularly designed to work with GitHub Copilot.

## Contents

### Automated Git Rebase Workflow

A GitHub Actions workflow that enables automated git rebase operations through a simple YAML configuration file. This is particularly useful for GitHub Copilot to perform commit squashing and rebase operations.

**📖 [View Full Documentation](AUTOMATED_REBASE.md)**

**Key Features:**
- Automated commit squashing and rebase operations
- GitHub Copilot integration
- YAML configuration format with multi-line message support
- Injection-safe parsing and execution
- Automatic cleanup and proper attribution

### Files

- **[AUTOMATED_REBASE.md](AUTOMATED_REBASE.md)** - Complete documentation for the automated rebase workflow
- **[REBASE_CONFIG_YAML.md](REBASE_CONFIG_YAML.md)** - YAML configuration format specification
- **[REBASE_TODO_FORMAT.md](REBASE_TODO_FORMAT.md)** - Legacy text format documentation
- **[copilot-instructions.md](copilot-instructions.md)** - GitHub Copilot integration guide
- **`.github/workflows/rebase-v2.yml`** - Main reusable rebase workflow (YAML format)
- **`.github/workflows/rebase.yml`** - Legacy rebase workflow (text format)
- **`.github/workflows/example-rebase-usage.yml`** - Example usage workflow