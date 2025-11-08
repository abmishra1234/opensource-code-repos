# opensource-code-repos

A repository for maintaining custom versions of open source projects, tracking modifications, and managing personal forks.

## Purpose

This repository serves as a centralized location to:
- Maintain customized versions of open source projects
- Track and document modifications made to upstream projects
- Manage personal forks with clear version history
- Facilitate easy syncing with upstream changes

## Structure

Projects should be organized with the following structure:

```
opensource-code-repos/
├── projects/
│   ├── project-name-1/
│   │   ├── src/              # Forked source code
│   │   ├── docs/             # Custom documentation
│   │   ├── UPSTREAM.md       # Upstream repository information
│   │   └── MODIFICATIONS.md  # Log of custom changes
│   └── project-name-2/
│       └── ...
├── docs/
│   └── workflows/            # Common workflows and guides
└── README.md
```

## Getting Started

### Adding a New Project

1. **Fork or Clone the Upstream Project**
   ```bash
   cd projects/
   git clone <upstream-repo-url> <project-name>
   cd <project-name>
   ```

2. **Track the Upstream Repository**
   ```bash
   git remote add upstream <upstream-repo-url>
   git fetch upstream
   ```

3. **Create Documentation**
   - Create an `UPSTREAM.md` file documenting the original repository
   - Create a `MODIFICATIONS.md` file to log your custom changes

### Syncing with Upstream

To keep your custom version up-to-date with upstream changes:

```bash
cd projects/<project-name>
git fetch upstream
git merge upstream/main  # or the appropriate branch
# Resolve any conflicts
git push origin main
```

### Making Custom Modifications

1. Create a feature branch for your modifications:
   ```bash
   git checkout -b custom/<feature-name>
   ```

2. Make your changes and commit with clear messages

3. Document changes in `MODIFICATIONS.md`

4. Merge back to your main branch:
   ```bash
   git checkout main
   git merge custom/<feature-name>
   ```

## Best Practices

### Version Management
- Keep your modifications in clearly named branches (e.g., `custom/`, `patch/`)
- Tag releases of your custom versions
- Maintain a changelog for significant modifications

### Documentation
- Always document why modifications were made
- Keep track of upstream version you're based on
- Note any breaking changes from upstream

### Syncing Strategy
- Regularly sync with upstream to avoid large conflicts
- Test thoroughly after merging upstream changes
- Keep your modifications minimal and well-documented

### Example UPSTREAM.md Template

```markdown
# Upstream Information

- **Repository**: <upstream-repo-url>
- **Original Author**: <author-name>
- **License**: <license-type>
- **Version Based On**: <commit-hash or tag>
- **Last Synced**: <date>
- **Purpose**: <why you're maintaining this fork>
```

### Example MODIFICATIONS.md Template

```markdown
# Custom Modifications

## Version 1.0.0 - YYYY-MM-DD
### Added
- Feature X for purpose Y

### Changed
- Modified function Z to handle edge case

### Removed
- Deprecated method A

## Rationale
<Explain why these modifications were necessary>
```

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on contributing to this repository.

## License

This repository structure is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

Note: Individual projects maintained within this repository are subject to their original licenses.