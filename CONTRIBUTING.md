# Contributing to Go Quality Gate Action

Thank you for your interest in contributing to the Go Quality Gate Action! This document provides guidelines and information for contributors.

## 🚀 Getting Started

### Prerequisites

- Git
- GitHub account
- Basic understanding of GitHub Actions
- Familiarity with YAML
- Shell scripting knowledge (bash)

### Development Setup

1. **Fork the repository**

   ```bash
   # Fork on GitHub, then clone your fork
   git clone https://github.com/YOUR_USERNAME/go-quality-gate-action.git
   cd go-quality-gate-action
   ```

2. **Create a development branch**

   ```bash
   git checkout -b feature/your-feature-name
   ```

3. **Test your changes**
   ```bash
   # Create a test workflow
   mkdir -p .github/workflows
   cp examples/basic-usage.yml .github/workflows/test-local.yml
   ```

## 📋 How to Contribute

### Types of Contributions

- 🐛 **Bug Reports**: Report issues or unexpected behavior
- ✨ **Feature Requests**: Suggest new functionality
- 📚 **Documentation**: Improve or add documentation
- 🔧 **Code Changes**: Fix bugs or implement features
- 🧪 **Tests**: Add or improve test coverage
- 📝 **Examples**: Create usage examples

### Reporting Issues

When reporting bugs or issues:

1. **Search existing issues** to avoid duplicates
2. **Use issue templates** when available
3. **Include detailed information**:
   - Steps to reproduce
   - Expected vs actual behavior
   - Environment details (OS, GitHub Actions runner)
   - Workflow configuration
   - Error messages and logs

**Bug Report Template:**

````markdown
## Bug Description

Brief description of the issue

## Steps to Reproduce

1. Step one
2. Step two
3. Step three

## Expected Behavior

What should happen

## Actual Behavior

What actually happens

## Environment

- OS: [ubuntu-latest, windows-latest, macos-latest]
- Action Version: [v1.0.0]
- Go Quality Gate Version: [latest]

## Configuration

```yaml
# Your action configuration
```
````

## Logs

```
# Relevant error logs
```

```

### Suggesting Features

For feature requests:

1. **Check existing feature requests** first
2. **Describe the use case** clearly
3. **Provide examples** of how it would work
4. **Consider backward compatibility**

## 🔧 Development Guidelines

### Code Style

- Use clear, descriptive variable names
- Add comments for complex logic
- Follow existing shell scripting patterns
- Use proper error handling

### Action Development

The action consists of several key files:

```

action.yml # Main action definition
README.md # Documentation
examples/ # Usage examples
docs/ # Detailed documentation
.github/workflows/ # CI/CD workflows

````

### Key Components

1. **action.yml**: Defines inputs, outputs, and steps
2. **Shell Scripts**: Embedded in the composite action
3. **Documentation**: README and docs/ folder
4. **Examples**: Real-world usage examples
5. **Tests**: Automated testing workflows

### Testing Your Changes

1. **Local Testing**:
   ```bash
   # Create a test repository
   mkdir test-project
   cd test-project
   git init

   # Create test configuration
   cat > quality.yml << 'EOF'
   tools:
     - name: "Test Tool"
       check_command: "echo 'test'"
       install_command: "echo 'install'"
   hooks:
     test:
       pre-commit:
         - name: "Test Check"
           command: "echo 'Testing...'"
   EOF

   # Test the action locally (requires act or similar)
   act -j test-action
````

2. **GitHub Testing**:
   - Push to your fork
   - Create a test workflow
   - Verify it works on all platforms

### Pull Request Process

1. **Create Feature Branch**

   ```bash
   git checkout -b feature/descriptive-name
   ```

2. **Make Changes**

   - Write clear commit messages
   - Keep changes focused and atomic
   - Add tests if applicable

3. **Update Documentation**

   - Update README.md if needed
   - Add examples for new features
   - Update configuration docs

4. **Test Thoroughly**

   - Test on multiple platforms
   - Verify existing functionality still works
   - Add test cases for new features

5. **Submit Pull Request**
   - Use descriptive title and description
   - Reference related issues
   - Include testing information

### Pull Request Template

```markdown
## Description

Brief description of changes

## Type of Change

- [ ] Bug fix
- [ ] New feature
- [ ] Documentation update
- [ ] Breaking change

## Testing

- [ ] Tested on Ubuntu
- [ ] Tested on Windows
- [ ] Tested on macOS
- [ ] Added test cases

## Checklist

- [ ] Code follows project style
- [ ] Documentation updated
- [ ] Examples added/updated
- [ ] Tests pass
```

## 📚 Documentation Guidelines

### Writing Documentation

- Use clear, concise language
- Provide practical examples
- Include troubleshooting information
- Keep examples up-to-date

### Documentation Structure

```
README.md              # Main documentation
docs/
  configuration.md     # Configuration guide
  troubleshooting.md   # Troubleshooting guide
examples/             # Usage examples
  basic-usage.yml
  multi-language.yml
  auto-fix.yml
```

### Example Standards

All examples should:

- Be complete and runnable
- Include relevant comments
- Cover common use cases
- Follow best practices

## 🧪 Testing Guidelines

### Test Categories

1. **Basic Functionality**: Core action features
2. **Platform Compatibility**: Linux, Windows, macOS
3. **Error Handling**: Various failure scenarios
4. **Integration**: With different tools and configurations

### Creating Tests

Add tests in `.github/workflows/test.yml`:

```yaml
- name: Test Feature X
  uses: ./
  with:
    # Test parameters

- name: Verify Feature X
  run: |
    # Verification logic
```

## 🏷️ Release Process

### Version Management

- Follow [Semantic Versioning](https://semver.org/)
- Update version in appropriate files
- Tag releases properly

### Release Checklist

1. **Update Documentation**

   - README.md changes
   - CHANGELOG.md entry
   - Version references

2. **Test Thoroughly**

   - All platforms
   - Various configurations
   - Backward compatibility

3. **Create Release**
   - Tag with proper version
   - Create GitHub release
   - Update marketplace listing

## 🎯 Best Practices

### Action Development

1. **Fail Fast**: Detect errors early
2. **Clear Output**: Provide helpful messages
3. **Backward Compatibility**: Don't break existing users
4. **Performance**: Keep action execution fast
5. **Security**: Handle secrets and tokens safely

### Shell Scripting

1. **Error Handling**: Use `set -e` and proper error checks
2. **Quoting**: Properly quote variables
3. **Platform Support**: Consider cross-platform compatibility
4. **Logging**: Provide clear status messages

### Examples

```bash
# Good
set -e
if [ ! -f "$CONFIG_FILE" ]; then
  echo "::error::Configuration file not found: $CONFIG_FILE"
  exit 1
fi

# Bad
if [ ! -f $CONFIG_FILE ]; then
  echo "Config not found"
fi
```

## 🤝 Community Guidelines

### Code of Conduct

- Be respectful and inclusive
- Provide constructive feedback
- Help others learn and grow
- Focus on what's best for the community

### Communication

- Use GitHub issues for bug reports and feature requests
- Be patient with responses
- Provide clear and helpful information
- Ask questions if you need clarification

## 📞 Getting Help

### Resources

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Go Quality Gate Repository](https://github.com/dmux/go-quality-gate)
- [Shell Scripting Guide](https://www.shellscript.sh/)

### Contact

- **Issues**: GitHub Issues for bugs and features
- **Discussions**: GitHub Discussions for general questions
- **Security**: Private security reports via GitHub Security

## 📝 License

By contributing, you agree that your contributions will be licensed under the MIT License.

Thank you for contributing to Go Quality Gate Action! 🚀
