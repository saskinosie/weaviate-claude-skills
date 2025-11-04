# Contributing to Weaviate Claude Skills

Thank you for your interest in contributing! This project welcomes contributions of all kinds.

## How to Contribute

### Reporting Issues

- Check if the issue already exists in [GitHub Issues](https://github.com/saskinosie/weaviate-claude-skills/issues)
- Provide clear steps to reproduce the problem
- Include your environment details (OS, Python version, Weaviate version)
- Share error messages and logs when applicable

### Suggesting Enhancements

- Open an issue describing your enhancement
- Explain the use case and why it would be valuable
- Provide examples of how it would work

### Contributing Code

1. **Fork the repository**
   ```bash
   git clone https://github.com/saskinosie/weaviate-claude-skills.git
   cd weaviate-claude-skills
   ```

2. **Create a feature branch**
   ```bash
   git checkout -b feature/your-feature-name
   ```

3. **Make your changes**
   - Follow the existing code style and structure
   - Test your changes with Claude.ai or Claude Desktop
   - Update documentation as needed

4. **Commit your changes**
   ```bash
   git add .
   git commit -m "Add: description of your changes"
   ```

5. **Push and create a Pull Request**
   ```bash
   git push origin feature/your-feature-name
   ```
   Then open a PR on GitHub with a clear description of your changes.

## Adding a New Skill

To add a new skill to the collection:

1. **Create a new folder** with a descriptive name:
   ```
   skill-name/
   ├── SKILL.md         # The skill content (required)
   └── examples/        # Optional: example files
   ```

2. **Write the SKILL.md** with YAML frontmatter:
   ```markdown
   ---
   skill_name: "Your Skill Name"
   version: "1.0"
   description: "Brief description of what this skill does"
   dependencies:
     - weaviate-connection  # List any prerequisite skills
   tags:
     - weaviate
     - your-tags
   ---

   # Skill Name

   ## Purpose
   Explain what this skill does and when to use it.

   ## Usage
   Provide clear examples and code snippets.

   ## Examples
   Show real-world usage scenarios.
   ```

3. **Update the main README.md**
   - Add your skill to the "Available Skills" section
   - Include it in the workflow diagrams if applicable
   - Update the project structure section

4. **Test your skill**
   - Load it in Claude.ai or Claude Desktop
   - Verify all examples work
   - Check that dependencies load correctly

## Skill Writing Guidelines

### Structure
- Start with clear prerequisites and dependencies
- Include practical, copy-paste examples
- Add error handling guidance
- Provide troubleshooting tips

### Code Examples
- Use real, working code (not pseudocode)
- Include imports and setup
- Show error handling
- Add comments explaining key steps

### Documentation
- Write for beginners and experts
- Explain WHY, not just WHAT
- Include common pitfalls
- Link to official Weaviate docs when relevant

### Testing
- Test with both Claude.ai web and Claude Desktop
- Verify skill loading and dependency resolution
- Check all code examples execute successfully
- Test with local and cloud Weaviate instances

## Improving Existing Skills

- Fix typos, improve clarity, update outdated information
- Add missing examples or use cases
- Improve error messages and troubleshooting
- Update to newer Weaviate client versions

## Development Setup

### Prerequisites
```bash
# Install Python dependencies
pip install -r requirements.txt

# Start local Weaviate (for testing)
cd weaviate-local-setup
docker-compose up -d
```

### Using Dev Container (Optional)
This repository includes a `.devcontainer` configuration for VS Code:

1. Install [VS Code](https://code.visualstudio.com/) and the [Dev Containers extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)
2. Open the repository in VS Code
3. Click "Reopen in Container" when prompted
4. Everything will be set up automatically (Python, Docker, dependencies)

### Testing Your Changes

1. **Test in Claude.ai**
   - Upload the modified SKILL.md file
   - Try the examples in the documentation
   - Verify dependencies load correctly

2. **Test in Claude Desktop**
   - Reference the local skills folder
   - Test skill loading and execution
   - Verify file paths work correctly

3. **Test with Local Weaviate**
   ```bash
   cd weaviate-local-setup
   docker-compose up -d
   # Wait for startup
   curl http://localhost:8080/v1/.well-known/ready
   ```

## Code of Conduct

- Be respectful and inclusive
- Welcome newcomers and beginners
- Focus on constructive feedback
- Help others learn and grow

## Questions?

- Open a [GitHub Issue](https://github.com/saskinosie/weaviate-claude-skills/issues)
- Check the [Weaviate Documentation](https://weaviate.io/developers/weaviate)
- Review existing skills for examples

## License

By contributing, you agree that your contributions will be licensed under the MIT License.

---

Thank you for making Weaviate Claude Skills better!
