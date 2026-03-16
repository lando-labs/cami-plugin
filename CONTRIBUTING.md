# Contributing to CAMI

Thank you for your interest in contributing to CAMI! This document provides guidelines and information for contributors.

## Getting Started

### Prerequisites

- [Claude Code CLI](https://claude.ai/code) installed and authenticated
- Git
- A GitHub account

### Local Development

1. Fork and clone the repository:
   ```bash
   git clone https://github.com/YOUR_USERNAME/cami-plugin.git
   cd cami-plugin
   ```

2. Run Claude Code with the plugin:
   ```bash
   claude --plugin-dir .
   ```

3. Test your changes by interacting with CAMI:
   ```
   You: "Hey CAMI"
   ```

## How to Contribute

### Reporting Bugs

Before submitting a bug report:
1. Check existing [issues](https://github.com/lando-labs/cami-plugin/issues) to avoid duplicates
2. Collect relevant information (Claude Code version, error messages, steps to reproduce)

When submitting a bug report:
- Use a clear, descriptive title
- Describe the exact steps to reproduce the problem
- Include your environment details (OS, Claude Code version)
- Describe the expected vs actual behavior

### Suggesting Features

Feature requests are welcome! Please:
1. Check existing [discussions](https://github.com/lando-labs/cami-plugin/discussions) first
2. Describe the problem your feature would solve
3. Explain your proposed solution
4. Consider alternatives you've thought about

### Pull Requests

1. **Create a branch** from `main`:
   ```bash
   git checkout -b feature/your-feature-name
   ```

2. **Make your changes**:
   - Follow the existing code style
   - Update documentation if needed
   - Keep changes focused and atomic

3. **Test your changes**:
   - Run through the [QUICKSTART.md](QUICKSTART.md) verification checklist
   - Test affected skills manually

4. **Commit your changes**:
   ```bash
   git commit -m "[scope] Brief description of change"
   ```

   Commit prefixes:
   - `[feat]` - New features
   - `[fix]` - Bug fixes
   - `[docs]` - Documentation changes
   - `[refactor]` - Code refactoring
   - `[test]` - Test additions or changes

5. **Push and create a PR**:
   ```bash
   git push origin feature/your-feature-name
   ```

### Code Style

- Skills use Markdown with YAML frontmatter
- Keep skill files focused and single-purpose
- Follow the scout persona voice guidelines in `reference/voice/scout-persona.md`
- Use clear, descriptive names for agents and skills

### Documentation

- Update relevant docs when changing functionality
- Keep examples practical and tested
- Maintain consistency with existing documentation style

## Project Structure

```
cami-plugin/
├── skills/                 # CAMI skills (entry points)
│   ├── cami/              # Main entry point
│   ├── create-agent/      # Agent creation
│   ├── create-project/    # Project setup
│   ├── manage-capabilities/  # Deploy/scan agents
│   └── manage-sources/    # Source management
├── agents/                 # Built-in agents
├── reference/             # Documentation and patterns
│   ├── voice/            # Persona and communication
│   ├── patterns/         # Common patterns
│   └── ux/               # UX specifications
└── .claude-plugin/        # Plugin metadata
```

## Questions?

- Open a [discussion](https://github.com/lando-labs/cami-plugin/discussions) for questions
- Check [issues](https://github.com/lando-labs/cami-plugin/issues) for known problems
- Read the [README](README.md) for usage information

## License

By contributing to CAMI, you agree that your contributions will be licensed under the MIT License.
