# Configuration Guide

This guide explains how to configure the Go Quality Gate Action for different project types and use cases.

## Basic Configuration Structure

The action uses a YAML configuration file (default: `quality.yml`) with the following structure:

```yaml
tools:
  - name: "Tool Name"
    check_command: "command to check if tool is installed"
    install_command: "command to install the tool"

hooks:
  hook_group:
    hook_type:
      - name: "Check Name"
        command: "command to run"
        fix_command: "optional auto-fix command"
        output_rules:
          show_on: "when to show output (always/failure/success)"
          on_failure_message: "custom failure message"
```

## Configuration Options

### Tools Section

Define the tools required for your quality checks:

```yaml
tools:
  - name: "Human-readable tool name"
    check_command: "Command that exits with 0 if tool is installed"
    install_command: "Command to install the tool automatically"
```

**Example:**

```yaml
tools:
  - name: "Prettier"
    check_command: "npx prettier --version"
    install_command: "npm install --global prettier"
```

### Hooks Section

Define quality checks organized by groups and hook types:

```yaml
hooks:
  group_name:
    hook_type: # pre-commit or pre-push
      - name: "Display name with emoji"
        command: "command to execute"
        fix_command: "optional command to auto-fix issues"
        output_rules:
          show_on: "always|failure|success"
          on_failure_message: "Custom message on failure"
```

### Output Rules

Control when and how output is displayed:

- `show_on: always` - Always show output
- `show_on: failure` - Only show output when command fails
- `show_on: success` - Only show output when command succeeds
- `on_failure_message` - Custom message to display on failure

## Language-Specific Configurations

### Go Projects

```yaml
tools:
  - name: "Go Formatter"
    check_command: "go version"
    install_command: "echo 'Go tools included with Go'"
  - name: "golangci-lint"
    check_command: "golangci-lint version"
    install_command: "go install github.com/golangci/golangci-lint/cmd/golangci-lint@latest"

hooks:
  golang:
    pre-commit:
      - name: "🎨 Go Format"
        command: "gofmt -l ."
        fix_command: "gofmt -w ."
      - name: "🔍 Go Lint"
        command: "golangci-lint run"
      - name: "🧪 Go Tests"
        command: "go test ./..."
    pre-push:
      - name: "🏗️ Go Build"
        command: "go build ./..."
```

### Python Projects

```yaml
tools:
  - name: "Ruff"
    check_command: "ruff --version"
    install_command: "pip install ruff"
  - name: "MyPy"
    check_command: "mypy --version"
    install_command: "pip install mypy"

hooks:
  python:
    pre-commit:
      - name: "🎨 Python Format"
        command: "ruff format --check ."
        fix_command: "ruff format ."
      - name: "🔍 Python Lint"
        command: "ruff check ."
        fix_command: "ruff check . --fix"
      - name: "🏷️ Type Check"
        command: "mypy ."
      - name: "🧪 Python Tests"
        command: "python -m pytest"
```

### JavaScript/TypeScript Projects

```yaml
tools:
  - name: "Prettier"
    check_command: "npx prettier --version"
    install_command: "npm install --global prettier"
  - name: "ESLint"
    check_command: "npx eslint --version"
    install_command: "npm install --global eslint"

hooks:
  javascript:
    pre-commit:
      - name: "🎨 Format Check"
        command: "npx prettier --check '**/*.{js,jsx,ts,tsx}'"
        fix_command: "npx prettier --write '**/*.{js,jsx,ts,tsx}'"
      - name: "🔍 Lint Check"
        command: "npx eslint '**/*.{js,jsx,ts,tsx}'"
        fix_command: "npx eslint '**/*.{js,jsx,ts,tsx}' --fix"
      - name: "🧪 Tests"
        command: "npm test"
```

### Rust Projects

```yaml
tools:
  - name: "Rustfmt"
    check_command: "rustfmt --version"
    install_command: "rustup component add rustfmt"
  - name: "Clippy"
    check_command: "cargo clippy --version"
    install_command: "rustup component add clippy"

hooks:
  rust:
    pre-commit:
      - name: "🎨 Rust Format"
        command: "cargo fmt -- --check"
        fix_command: "cargo fmt"
      - name: "🔍 Clippy Lint"
        command: "cargo clippy -- -D warnings"
      - name: "🧪 Rust Tests"
        command: "cargo test"
```

## Security Configuration

Always include security checks regardless of language:

```yaml
tools:
  - name: "Gitleaks"
    check_command: "gitleaks version"
    install_command: "go install github.com/zricethezav/gitleaks/v8@latest"

hooks:
  security:
    pre-commit:
      - name: "🔒 Secret Scanning"
        command: "gitleaks detect --no-git --source . --verbose"
        output_rules:
          on_failure_message: "⚠️ Potential secrets detected! Please review before committing."
```

## Multi-Language Projects

For projects with multiple languages, combine configurations:

```yaml
tools:
  # Security tools (for all projects)
  - name: "Gitleaks"
    check_command: "gitleaks version"
    install_command: "go install github.com/zricethezav/gitleaks/v8@latest"

  # Go tools
  - name: "golangci-lint"
    check_command: "golangci-lint version"
    install_command: "go install github.com/golangci/golangci-lint/cmd/golangci-lint@latest"

  # Python tools
  - name: "Ruff"
    check_command: "ruff --version"
    install_command: "pip install ruff"

  # JavaScript tools
  - name: "Prettier"
    check_command: "npx prettier --version"
    install_command: "npm install --global prettier"

hooks:
  # Security (runs for all files)
  security:
    pre-commit:
      - name: "🔒 Secret Scanning"
        command: "gitleaks detect --no-git --source . --verbose"

  # Backend (Go)
  backend:
    pre-commit:
      - name: "🎨 Go Format"
        command: "gofmt -l ./backend"
        fix_command: "gofmt -w ./backend"
      - name: "🔍 Go Lint"
        command: "golangci-lint run ./backend/..."

  # Scripts (Python)
  scripts:
    pre-commit:
      - name: "🎨 Python Format"
        command: "ruff format --check ./scripts"
        fix_command: "ruff format ./scripts"

  # Frontend (JavaScript/TypeScript)
  frontend:
    pre-commit:
      - name: "🎨 Frontend Format"
        command: "npx prettier --check './frontend/**/*.{js,jsx,ts,tsx}'"
        fix_command: "npx prettier --write './frontend/**/*.{js,jsx,ts,tsx}'"
```

## Advanced Configuration

### Conditional Checks

Use shell conditions in commands:

```yaml
hooks:
  conditional:
    pre-commit:
      - name: "🧪 Run Tests (if test files exist)"
        command: "[ -d './tests' ] && python -m pytest ./tests || echo 'No tests found'"
      - name: "🎨 Format Go (if Go files exist)"
        command: "find . -name '*.go' | grep -q . && gofmt -l . || echo 'No Go files found'"
```

### Custom Error Messages

Provide helpful error messages:

```yaml
hooks:
  helpful:
    pre-commit:
      - name: "🎨 Code Formatting"
        command: "prettier --check ."
        fix_command: "prettier --write ."
        output_rules:
          show_on: failure
          on_failure_message: |
            Code formatting issues detected!

            To fix automatically, run:
            ./quality-gate --fix pre-commit

            Or manually run:
            npx prettier --write .
```

### Performance Optimization

For large projects, optimize commands:

```yaml
hooks:
  optimized:
    pre-commit:
      - name: "🔍 Lint Changed Files Only"
        command: "git diff --cached --name-only --diff-filter=ACM | grep '\\.py$' | xargs -r ruff check"
      - name: "🧪 Test Affected Areas"
        command: "python -m pytest --lf --tb=short" # Only last failed tests
```

## Best Practices

1. **Start Simple**: Begin with basic formatting and linting
2. **Add Security**: Always include secret scanning
3. **Incremental Adoption**: Add checks gradually
4. **Fast Feedback**: Keep checks fast (<30 seconds)
5. **Clear Messages**: Provide helpful error messages
6. **Auto-fix When Possible**: Include fix commands for formatting issues
7. **Document Everything**: Comment your configuration files

## Example Complete Configuration

See `quality.example.yml` for a comprehensive example covering multiple languages and use cases.

## Troubleshooting

### Tools Not Found

Ensure tools are installed or enable auto-installation:

```yaml
# In your GitHub Action
- uses: dmux/go-quality-gate-action@v1
  with:
    install-dependencies: true
```

### Commands Failing

Check tool installation and paths:

```yaml
tools:
  - name: "Tool Check"
    check_command: "which your-tool && your-tool --version"
    install_command: "installation-command"
```

### Performance Issues

For slow commands, consider:

- Running only on changed files
- Using parallel execution where possible
- Optimizing tool configurations
- Splitting into separate hooks

### Platform Differences

For cross-platform compatibility:

```yaml
hooks:
  cross-platform:
    pre-commit:
      - name: "🔍 Cross-platform Check"
        command: |
          if [ "$RUNNER_OS" = "Windows" ]; then
            # Windows-specific command
            echo "Running on Windows"
          else
            # Unix-like systems
            echo "Running on Unix-like system"
          fi
```
