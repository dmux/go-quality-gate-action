# Changelog

All notable changes to the Go Quality Gate Action will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

- Initial release of Go Quality Gate Action
- Complete GitHub Action implementation
- Support for multiple platforms (Linux, macOS, Windows)
- Automatic binary download and installation
- Configuration validation and default creation
- Auto-fix functionality
- JSON output support
- Comprehensive documentation and examples

### Features

- **Multi-platform Support**: Works on Ubuntu, Windows, and macOS runners
- **Automatic Setup**: Downloads and installs go-quality-gate binary automatically
- **Flexible Configuration**: Supports custom configuration files and paths
- **Auto-fix**: Automatically applies fixes when possible
- **Rich Output**: Human-readable and JSON output formats
- **Tool Management**: Automatically installs missing quality tools
- **Error Handling**: Comprehensive error handling and reporting

## [1.0.0] - Initial Release

### Added

- Core action functionality
- Input parameters:
  - `version`: Version of go-quality-gate to use
  - `config-file`: Path to quality.yml configuration
  - `hook`: Hook to execute (pre-commit, pre-push)
  - `auto-fix`: Enable automatic fixes
  - `json-output`: JSON format output
  - `fail-on-error`: Fail action on quality issues
  - `install-dependencies`: Auto-install missing tools
  - `working-directory`: Working directory for execution

### Outputs

- `result`: Quality gate execution result
- `output`: Detailed output (JSON or text)
- `duration`: Execution time in milliseconds

### Documentation

- Comprehensive README with usage examples
- Configuration guide
- Troubleshooting documentation
- Contributing guidelines
- Multiple workflow examples

### Examples

- Basic usage example
- Multi-language project configuration
- Auto-fix workflow
- Matrix testing setup
- Advanced result processing

### Infrastructure

- GitHub Actions workflows for testing
- Release automation
- Cross-platform testing
- Example configurations

---

## Template for Future Releases

### [X.Y.Z] - YYYY-MM-DD

#### Added

- New features

#### Changed

- Changes in existing functionality

#### Deprecated

- Soon-to-be removed features

#### Removed

- Removed features

#### Fixed

- Bug fixes

#### Security

- Security improvements
