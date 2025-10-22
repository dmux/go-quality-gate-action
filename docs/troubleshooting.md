# Troubleshooting Guide

This guide helps you resolve common issues when using the Go Quality Gate Action.

## Common Issues

### 1. Action Fails to Download Binary

**Error:**

```
Error: Failed to download go-quality-gate binary
```

**Solutions:**

1. **Check Release Availability:**
   - Verify the specified version exists in [releases](https://github.com/dmux/go-quality-gate/releases)
   - Use `latest` instead of a specific version
2. **Network Issues:**

   ```yaml
   - name: Run Quality Gate
     uses: dmux/go-quality-gate-action@v1
     with:
       version: "latest" # Try latest instead of specific version
   ```

3. **Platform Issues:**
   - Ensure your runner OS is supported (Linux, macOS, Windows)
   - Check if ARM64 binaries are available for your platform

### 2. Configuration File Not Found

**Error:**

```
Configuration file 'quality.yml' not found
```

**Solutions:**

1. **Create Configuration File:**

   ```bash
   # In your repository root
   touch quality.yml
   ```

2. **Use Custom Path:**

   ```yaml
   - uses: dmux/go-quality-gate-action@v1
     with:
       config-file: ".github/quality.yml"
   ```

3. **Let Action Create Default:**
   ```yaml
   - uses: dmux/go-quality-gate-action@v1
   # Action will create default configuration automatically
   ```

### 3. Tools Not Installed

**Error:**

```
Tool 'prettier' not found
```

**Solutions:**

1. **Enable Auto-Installation:**

   ```yaml
   - uses: dmux/go-quality-gate-action@v1
     with:
       install-dependencies: true
   ```

2. **Install Tools Manually:**

   ```yaml
   steps:
     - uses: actions/checkout@v4

     - name: Install Tools
       run: |
         npm install -g prettier eslint
         pip install ruff black mypy
         go install github.com/golangci/golangci-lint/cmd/golangci-lint@latest

     - uses: dmux/go-quality-gate-action@v1
   ```

3. **Update Tool Check Commands:**
   ```yaml
   tools:
     - name: "Prettier"
       check_command: "npx prettier --version 2>/dev/null || npm list -g prettier"
       install_command: "npm install -g prettier"
   ```

### 4. Permission Denied Errors

**Error:**

```
Permission denied: /usr/local/bin/quality-gate
```

**Solutions:**

1. **Use Different Installation Path:**
   ```yaml
   # The action handles this automatically, but if you're customizing:
   - name: Install to User Directory
     run: |
       mkdir -p ~/.local/bin
       curl -L -o ~/.local/bin/quality-gate "$DOWNLOAD_URL"
       chmod +x ~/.local/bin/quality-gate
       echo "$HOME/.local/bin" >> $GITHUB_PATH
   ```

### 5. Quality Checks Fail

**Error:**

```
Quality gate failed with exit code 1
```

**Solutions:**

1. **Enable Auto-Fix:**

   ```yaml
   - uses: dmux/go-quality-gate-action@v1
     with:
       auto-fix: true
       fail-on-error: false # Don't fail if auto-fix resolves issues
   ```

2. **Debug Output:**

   ```yaml
   - uses: dmux/go-quality-gate-action@v1
     with:
       json-output: true
     env:
       ACTIONS_STEP_DEBUG: true
   ```

3. **Process Results:**

   ```yaml
   - name: Run Quality Gate
     id: qg
     uses: dmux/go-quality-gate-action@v1
     with:
       fail-on-error: false

   - name: Check Results
     run: |
       echo "Result: ${{ steps.qg.outputs.result }}"
       echo "Output: ${{ steps.qg.outputs.output }}"
   ```

### 6. Windows-Specific Issues

**Error:**

```
Command not found on Windows
```

**Solutions:**

1. **Use Cross-Platform Commands:**

   ```yaml
   hooks:
     cross-platform:
       pre-commit:
         - name: "🔍 Cross-Platform Check"
           command: |
             if [ "$RUNNER_OS" = "Windows" ]; then
               echo "Windows command"
             else
               echo "Unix command"
             fi
   ```

2. **PowerShell Commands:**
   ```yaml
   - name: Windows Setup
     if: runner.os == 'Windows'
     shell: powershell
     run: |
       # PowerShell-specific commands
   ```

### 7. Large Repository Performance

**Issue:** Action takes too long to complete

**Solutions:**

1. **Optimize Commands for Changed Files:**

   ```yaml
   hooks:
     optimized:
       pre-commit:
         - name: "🔍 Lint Changed Files"
           command: |
             git diff --cached --name-only --diff-filter=ACM | \
             grep '\\.js$' | \
             xargs -r npx eslint
   ```

2. **Parallel Execution:**

   ```yaml
   hooks:
     parallel:
       pre-commit:
         - name: "🎨 Format Frontend"
           command: "npx prettier --check 'frontend/**/*.{js,ts}'" &
         - name: "🎨 Format Backend"
           command: "gofmt -l ./backend" &
         - name: "Wait for All"
           command: "wait"
   ```

3. **Selective Hook Execution:**
   ```yaml
   - uses: dmux/go-quality-gate-action@v1
     with:
       hook: "pre-commit" # Only run specific hook
   ```

### 8. JSON Output Issues

**Error:**

```
Invalid JSON output
```

**Solutions:**

1. **Validate JSON Structure:**

   ```yaml
   - name: Validate JSON
     run: |
       echo '${{ steps.qg.outputs.output }}' | jq '.'
   ```

2. **Handle Empty Output:**
   ```yaml
   - name: Process Results
     run: |
       OUTPUT='${{ steps.qg.outputs.output }}'
       if [ -n "$OUTPUT" ] && [ "$OUTPUT" != "null" ]; then
         echo "$OUTPUT" | jq '.results[]'
       else
         echo "No JSON output available"
       fi
   ```

### 9. Git Hooks Conflicts

**Issue:** Conflicts with existing git hooks

**Solutions:**

1. **Use Action Only (Disable Git Hooks):**

   ```yaml
   # Don't run quality-gate --install, just use in CI
   - uses: dmux/go-quality-gate-action@v1
   ```

2. **Backup Existing Hooks:**
   ```bash
   # Before installing
   cp .git/hooks/pre-commit .git/hooks/pre-commit.backup
   ```

### 10. Environment Variables

**Issue:** Tools need specific environment setup

**Solutions:**

1. **Set Environment Variables:**

   ```yaml
   - uses: dmux/go-quality-gate-action@v1
     env:
       NODE_ENV: production
       PYTHONPATH: ./src
       GOPATH: /go
   ```

2. **Load Environment from File:**

   ```yaml
   - name: Load Environment
     run: |
       if [ -f .env ]; then
         export $(cat .env | xargs)
       fi

   - uses: dmux/go-quality-gate-action@v1
   ```

## Debug Strategies

### 1. Enable Verbose Logging

```yaml
- uses: dmux/go-quality-gate-action@v1
  env:
    ACTIONS_STEP_DEBUG: true
    ACTIONS_RUNNER_DEBUG: true
```

### 2. Test Configuration Locally

```bash
# Install quality-gate locally
curl -L -o quality-gate "https://github.com/dmux/go-quality-gate/releases/latest/download/quality-gate-$(uname -s | tr '[:upper:]' '[:lower:]')-$(uname -m)"
chmod +x quality-gate

# Test configuration
./quality-gate --init
./quality-gate pre-commit --output=json
```

### 3. Minimal Test Configuration

Create a minimal `quality.yml` for testing:

```yaml
tools:
  - name: "Echo Test"
    check_command: "echo 'test'"
    install_command: "echo 'no install needed'"

hooks:
  test:
    pre-commit:
      - name: "🧪 Basic Test"
        command: "echo 'Quality gate working!'"
```

### 4. Step-by-Step Debugging

```yaml
- name: Debug - Check Environment
  run: |
    echo "Runner OS: $RUNNER_OS"
    echo "Architecture: $(uname -m)"
    echo "Shell: $SHELL"
    echo "Path: $PATH"

- name: Debug - Check Files
  run: |
    ls -la
    find . -name "quality.yml" -o -name "*.yml" | head -10

- name: Debug - Test Commands
  run: |
    # Test individual commands from your configuration
    echo "Testing tool installations..."
    which node || echo "Node not found"
    which python || which python3 || echo "Python not found"
    which go || echo "Go not found"
```

## Getting Help

### 1. Enable Debug Output

Always include debug output when reporting issues:

```yaml
- uses: dmux/go-quality-gate-action@v1
  env:
    ACTIONS_STEP_DEBUG: true
```

### 2. Include Configuration

When reporting issues, include:

- Your `action.yml` workflow
- Your `quality.yml` configuration
- Error messages and logs
- Runner OS and versions

### 3. Test Locally First

Before reporting issues:

1. Test with a minimal configuration
2. Verify tools are available
3. Check configuration syntax

### 4. Common Information to Include

When asking for help, provide:

```yaml
# Your workflow file
name: Debug Quality Gate
on: [workflow_dispatch]
jobs:
  debug:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Debug Info
        run: |
          echo "Runner: ${{ runner.os }}"
          echo "Event: ${{ github.event_name }}"
          echo "Repository: ${{ github.repository }}"
          cat quality.yml

      - uses: dmux/go-quality-gate-action@v1
        env:
          ACTIONS_STEP_DEBUG: true
```

## Additional Resources

- [Main Documentation](../README.md)
- [Configuration Guide](configuration.md)
- [GitHub Issues](https://github.com/dmux/go-quality-gate-action/issues)
- [Go Quality Gate Repository](https://github.com/dmux/go-quality-gate)
