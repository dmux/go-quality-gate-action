# Go Quality Gate Action

[![GitHub release (latest by date)](https://img.shields.io/github/v/release/dmux/go-quality-gate-action)](https://github.com/dmux/go-quality-gate-action/releases)
[![GitHub](https://img.shields.io/github/license/dmux/go-quality-gate-action)](LICENSE)
[![GitHub Marketplace](https://img.shields.io/badge/Marketplace-Go%20Quality%20Gate-blue.svg?colorA=24292e&colorB=0366d6&style=flat&longCache=true&logo=data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAA4AAAAOCAYAAAAfSC3RAAAABHNCSVQICAgIfAhkiAAAAAlwSFlzAAAM6wAADOsB5dZE0gAAABl0RVh0U29mdHdhcmUAd3d3Lmlua3NjYXBlLm9yZ5vuPBoAAAERSURBVCiRhZG/SsMxFEZPfsVJ61jbxaF0cRQRcRJ9hlYn30IHN/+9iquDCOIsblIrOjqKgy5aKoJQj4n3EI/XP/C7yUGhV+iXf1SR/EgJuHlTUVMN7j4S1WGvU7elRs29xJeHP2IEd3jqv9sd4JeXH5MHmVQAAB0xMAEE3jJFRwLhWneUAIcH6rgvnby8lb3johdLWUGAD8FAQYv2z3fVa1KbB7QeBQRAj1qrDAHAgnmXAuvgPZNlqMwaR8PDVQXDOPWU2NgNkMxKEVaZkYj8lGVUpKmzNLOSYKhOUs2tjOb3ZJ3n8fdHnVa++SPV6GQOYy5ChaOEGhJSzMQKwLT5hHZ0oQAA//Lz7N2ZWdwAAAAASUVORK5CYII=)](https://github.com/marketplace/actions/go-quality-gate)

A comprehensive GitHub Action for **Go Quality Gate** - a language-agnostic code quality control tool built in Go with Git hooks support.

## ✨ Features

- 🏗️ **Zero Dependencies**: Single binary with no runtime dependencies
- 🔧 **Automatic Setup**: Downloads and installs go-quality-gate automatically
- 🌍 **Multi-language Support**: Works with any programming language
- 📊 **Rich Output**: Supports both human-readable and JSON output formats
- 🔒 **Built-in Security**: Includes secret scanning capabilities
- ⚡ **Fast Execution**: Native Go performance
- 🛠️ **Auto-fix**: Automatic code formatting and fixes when possible

## 🚀 Quick Start

### Basic Usage

```yaml
name: Quality Gate
on: [push, pull_request]

jobs:
  quality-gate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Run Quality Gate
        uses: dmux/go-quality-gate-action@v1
        with:
          hook: "pre-commit"
```

### Advanced Configuration

```yaml
name: Quality Gate
on: [push, pull_request]

jobs:
  quality-gate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Run Quality Gate with Auto-fix
        uses: dmux/go-quality-gate-action@v1
        with:
          version: "latest"
          config-file: ".github/quality.yml"
          hook: "pre-commit"
          auto-fix: true
          json-output: true
          fail-on-error: true
          install-dependencies: true
          working-directory: "."
```

## 📋 Inputs

| Input                  | Description                                 | Default       | Required |
| ---------------------- | ------------------------------------------- | ------------- | -------- |
| `version`              | Version of go-quality-gate to use           | `latest`      | No       |
| `config-file`          | Path to the quality.yml configuration file  | `quality.yml` | No       |
| `hook`                 | Hook to execute (pre-commit, pre-push)      | `pre-commit`  | No       |
| `auto-fix`             | Enable automatic fixes when possible        | `false`       | No       |
| `json-output`          | Output results in JSON format               | `true`        | No       |
| `fail-on-error`        | Fail the action when quality checks fail    | `true`        | No       |
| `install-dependencies` | Automatically install missing quality tools | `true`        | No       |
| `working-directory`    | Working directory to run the quality gate   | `.`           | No       |

## 📤 Outputs

| Output     | Description                                                |
| ---------- | ---------------------------------------------------------- |
| `result`   | Quality gate execution result (`success`/`failure`)        |
| `output`   | Quality gate output (JSON format when json-output is true) |
| `duration` | Total execution time in milliseconds                       |

## ⚙️ Configuration

Create a `quality.yml` file in your repository root:

```yaml
tools:
  - name: "Gitleaks"
    check_command: "gitleaks version"
    install_command: "go install github.com/zricethezav/gitleaks/v8@latest"
  - name: "Ruff (Python)"
    check_command: "ruff --version"
    install_command: "pip install ruff"
  - name: "Prettier (JavaScript/TypeScript)"
    check_command: "npx prettier --version"
    install_command: "npm install --global prettier"

hooks:
  security:
    pre-commit:
      - name: "🔒 Security Check"
        command: "gitleaks detect --no-git --source . --verbose"
        output_rules:
          on_failure_message: "Secret leak detected! Review code before committing."

  python-backend:
    pre-commit:
      - name: "🎨 Format Check (Ruff)"
        command: "ruff format ./backend --check"
        fix_command: "ruff format ./backend"
        output_rules:
          show_on: failure
          on_failure_message: "Code formatting issues found. Enable auto-fix to resolve."
      - name: "🧪 Lint Check (Ruff)"
        command: "ruff check ./backend"
        fix_command: "ruff check ./backend --fix"

  typescript-frontend:
    pre-commit:
      - name: "🎨 Format Check (Prettier)"
        command: "npx prettier --check 'frontend/**/*.{ts,tsx,js,jsx}'"
        fix_command: "npx prettier --write 'frontend/**/*.{ts,tsx,js,jsx}'"
      - name: "🔍 Lint Check (ESLint)"
        command: "npx eslint 'frontend/**/*.{ts,tsx,js,jsx}'"
        fix_command: "npx eslint 'frontend/**/*.{ts,tsx,js,jsx}' --fix"
```

## 📋 Complete Workflow Examples

### Multi-Language Project

```yaml
name: Quality Gate - Multi-Language
on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]

jobs:
  quality-gate:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4
        with:
          fetch-depth: 0 # Fetch full history for better analysis

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: "18"
          cache: "npm"

      - name: Setup Python
        uses: actions/setup-python@v4
        with:
          python-version: "3.11"
          cache: "pip"

      - name: Install Dependencies
        run: |
          npm ci
          pip install -r requirements.txt

      - name: Run Quality Gate
        uses: dmux/go-quality-gate-action@v1
        with:
          config-file: ".github/quality.yml"
          hook: "pre-commit"
          auto-fix: false
          json-output: true
          fail-on-error: true

      - name: Upload Results
        if: always()
        uses: actions/upload-artifact@v3
        with:
          name: quality-gate-results
          path: quality-gate-results.json
```

### Auto-fix Workflow

```yaml
name: Quality Gate - Auto Fix
on:
  push:
    branches: [feature/*, bugfix/*]

jobs:
  quality-gate-autofix:
    runs-on: ubuntu-latest
    permissions:
      contents: write # Required for auto-commit

    steps:
      - name: Checkout Code
        uses: actions/checkout@v4
        with:
          token: ${{ secrets.GITHUB_TOKEN }}

      - name: Run Quality Gate with Auto-fix
        uses: dmux/go-quality-gate-action@v1
        with:
          hook: "pre-commit"
          auto-fix: true
          fail-on-error: false # Don't fail, we want to commit fixes

      - name: Commit Auto-fixes
        if: steps.quality-gate.outputs.result == 'failure'
        run: |
          git config --local user.email "action@github.com"
          git config --local user.name "Quality Gate Action"

          if ! git diff --quiet; then
            git add .
            git commit -m "🤖 Auto-fix: Code formatting and linting fixes"
            git push
            echo "✅ Auto-fixes committed and pushed"
          else
            echo "ℹ️ No auto-fixes applied"
          fi
```

### Matrix Strategy for Multiple Environments

```yaml
name: Quality Gate - Matrix
on: [push, pull_request]

jobs:
  quality-gate:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
        include:
          - os: ubuntu-latest
            hook: pre-commit
          - os: windows-latest
            hook: pre-commit
          - os: macos-latest
            hook: pre-push

    steps:
      - uses: actions/checkout@v4

      - name: Run Quality Gate
        uses: dmux/go-quality-gate-action@v1
        with:
          hook: ${{ matrix.hook }}
          json-output: true
```

## 🎯 JSON Output Usage

When `json-output` is enabled, you can process the results programmatically:

```yaml
- name: Run Quality Gate
  id: qg
  uses: dmux/go-quality-gate-action@v1
  with:
    json-output: true
    fail-on-error: false

- name: Process Results
  run: |
    echo "Result: ${{ steps.qg.outputs.result }}"
    echo "Duration: ${{ steps.qg.outputs.duration }}ms"

    # Parse JSON output
    echo '${{ steps.qg.outputs.output }}' | jq '.results[] | select(.success == false) | .hook.Name'
```

Sample JSON output:

```json
{
  "status": "failure",
  "results": [
    {
      "hook": {
        "Name": "🔒 Security Check",
        "Command": "gitleaks detect --no-git --source . --verbose"
      },
      "success": true,
      "output": "",
      "duration_ms": 150,
      "duration": "150ms"
    },
    {
      "hook": {
        "Name": "🎨 Format Check (Prettier)",
        "Command": "npx prettier --check 'src/**/*.{ts,tsx}'"
      },
      "success": false,
      "output": "Code style issues found in 3 files",
      "duration_ms": 890,
      "duration": "890ms"
    }
  ]
}
```

## 🔧 Troubleshooting

### Common Issues

**Issue**: Configuration file not found

```yaml
# Solution: Specify correct path or let action create default
- uses: dmux/go-quality-gate-action@v1
  with:
    config-file: ".github/workflows/quality.yml" # Custom path
```

**Issue**: Tools not installed

```yaml
# Solution: Enable automatic installation
- uses: dmux/go-quality-gate-action@v1
  with:
    install-dependencies: true
```

**Issue**: Action fails on formatting issues

```yaml
# Solution: Enable auto-fix or set fail-on-error to false
- uses: dmux/go-quality-gate-action@v1
  with:
    auto-fix: true
    fail-on-error: false
```

### Debug Mode

Enable step debug output:

```yaml
- name: Run Quality Gate
  uses: dmux/go-quality-gate-action@v1
  with:
    hook: "pre-commit"
  env:
    ACTIONS_STEP_DEBUG: true
```

## 🛠️ Supported Platforms

- ✅ Linux (x64, ARM64)
- ✅ macOS (Intel, Apple Silicon)
- ✅ Windows (x64)

## 📚 Related Projects

- [go-quality-gate](https://github.com/dmux/go-quality-gate) - The main CLI tool
- [Quality Gate Documentation](https://dmux.github.io/go-quality-gate/) - Complete documentation

## 🤝 Contributing

1. Fork the repository
2. Create your feature branch: `git checkout -b feature/amazing-feature`
3. Commit your changes: `git commit -m 'Add amazing feature'`
4. Push to the branch: `git push origin feature/amazing-feature`
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🔗 Links

- [GitHub Marketplace](https://github.com/marketplace/actions/go-quality-gate)
- [GitHub Repository](https://github.com/dmux/go-quality-gate-action)
- [Documentation](https://dmux.github.io/go-quality-gate/)
- [Issues](https://github.com/dmux/go-quality-gate-action/issues)
