# Contributing Guide

This guide explains how to add and maintain open source projects in this repository.

## Adding a New Project

### Prerequisites
- Ensure you have permission to fork/use the open source project
- Verify the project's license is compatible with your needs
- Understand the project's contribution guidelines if you plan to contribute back

### Steps

1. **Create Project Directory**
   ```bash
   mkdir -p projects/<project-name>
   cd projects/<project-name>
   ```

2. **Initialize or Clone**
   
   Option A - Clone existing repository:
   ```bash
   git clone <upstream-repo-url> .
   ```
   
   Option B - Copy source files manually:
   ```bash
   # Copy files into the directory
   git init
   git add .
   git commit -m "Initial import from <project-name> v<version>"
   ```

3. **Set Up Upstream Tracking**
   ```bash
   git remote add upstream <upstream-repo-url>
   git fetch upstream
   ```

4. **Create Required Documentation**
   
   Create `UPSTREAM.md`:
   ```markdown
   # Upstream Information
   
   - **Repository**: <upstream-repo-url>
   - **Original Author**: <author-name>
   - **License**: <license-type>
   - **Version Based On**: <commit-hash or tag>
   - **Last Synced**: <date>
   - **Purpose**: <why you're maintaining this fork>
   - **Homepage**: <project-homepage-url>
   - **Documentation**: <project-docs-url>
   ```
   
   Create `MODIFICATIONS.md`:
   ```markdown
   # Custom Modifications
   
   ## Initial Version - <date>
   ### Notes
   - Started tracking custom version based on <upstream-version>
   - Initial state: No modifications from upstream
   ```

5. **Update Main README**
   Add your project to the main repository README.md with:
   - Project name and brief description
   - Link to project directory
   - Purpose of maintaining this fork

## Making Modifications

### Workflow

1. **Create a Feature Branch**
   ```bash
   git checkout -b custom/<descriptive-name>
   ```

2. **Make Your Changes**
   - Keep changes focused and minimal
   - Follow the original project's coding standards
   - Add comments explaining why changes were made

3. **Document Your Changes**
   Update `MODIFICATIONS.md`:
   ```markdown
   ## Version/Date
   ### Added/Changed/Removed
   - Specific change description
   - Files affected: path/to/file.ext
   - Reason: Why this change was necessary
   ```

4. **Commit with Clear Messages**
   ```bash
   git add <changed-files>
   git commit -m "type: brief description
   
   Detailed explanation of what and why.
   Related to: <issue/requirement>
   "
   ```

5. **Merge to Main**
   ```bash
   git checkout main
   git merge custom/<descriptive-name>
   ```

### Commit Message Format

Use conventional commit format:
- `feat:` New feature
- `fix:` Bug fix
- `docs:` Documentation changes
- `refactor:` Code refactoring
- `test:` Adding or updating tests
- `chore:` Maintenance tasks
- `custom:` Custom modifications specific to your fork

## Syncing with Upstream

### Regular Sync Process

1. **Fetch Upstream Changes**
   ```bash
   cd projects/<project-name>
   git fetch upstream
   ```

2. **Check for Updates**
   ```bash
   git log HEAD..upstream/main --oneline
   ```

3. **Merge Upstream Changes**
   ```bash
   git checkout main
   git merge upstream/main
   ```

4. **Resolve Conflicts**
   - Carefully review conflicts
   - Preserve your custom modifications
   - Test thoroughly after resolution

5. **Update Documentation**
   Update `UPSTREAM.md`:
   ```markdown
   - **Last Synced**: <new-date>
   - **Version Based On**: <new-commit-hash>
   ```
   
   Update `MODIFICATIONS.md` if conflicts required changes:
   ```markdown
   ## Sync - <date>
   ### Updated
   - Synced with upstream version <version>
   - Resolved conflicts in <files>
   - Verified custom modifications still work
   ```

### Handling Conflicts

When conflicts arise:
1. Understand what changed in upstream
2. Understand your custom modifications
3. Decide how to integrate both changes
4. Document the resolution strategy
5. Test the merged result

## Best Practices

### Code Quality
- Maintain the same code quality standards as the upstream project
- Run tests before and after modifications
- Use linters and formatters consistent with the project

### Documentation
- Always explain WHY you made a change, not just WHAT changed
- Keep documentation up-to-date with code changes
- Link to relevant issues or discussions

### Testing
- Run existing test suites after modifications
- Add tests for new functionality
- Verify your changes don't break existing features

### Version Control
- Commit often with clear messages
- Use branches for experimental changes
- Tag stable versions of your fork

### Communication
- If beneficial to upstream, consider contributing back
- Keep track of upstream discussions about areas you've modified
- Note any upstream changes that might affect your modifications

## Removing a Project

If you no longer need to maintain a custom version:

1. **Archive or Remove Directory**
   ```bash
   git rm -r projects/<project-name>
   git commit -m "chore: remove <project-name> - no longer maintained"
   ```

2. **Update Main README**
   Remove the project from the main repository README.md

3. **Document Reason**
   If relevant, add a note in a `ARCHIVED.md` file

## Questions or Issues?

- Review the main [README.md](README.md) for general information
- Check existing project documentation for examples
- Consult the upstream project's documentation and community resources
