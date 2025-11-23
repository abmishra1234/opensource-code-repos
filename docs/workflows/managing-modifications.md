# Managing Modifications

This guide covers best practices for making, tracking, and maintaining custom modifications to open source projects.

## Philosophy

When maintaining your own version of open source code:
- **Minimize changes**: Only modify what's necessary
- **Document thoroughly**: Future you will thank present you
- **Stay organized**: Use clear naming and structure
- **Test rigorously**: Your modifications should not break existing functionality

## Making Modifications

### Step 1: Plan Your Change

Before coding, answer these questions:
- What problem are you solving?
- Can this be solved without modifying the source?
- Could this be contributed back to upstream?
- What's the impact on future upstream merges?

### Step 2: Create a Feature Branch

```bash
cd projects/<project-name>

# Create descriptive branch
git checkout -b custom/<feature-name>

# Examples:
# custom/add-authentication
# custom/fix-performance-issue
# custom/integrate-logging
```

### Step 3: Make Your Changes

Guidelines:
- Follow the project's existing code style
- Keep changes localized and focused
- Add comments explaining WHY, not just WHAT
- Consider configuration over code changes when possible

Example of well-commented custom modification:

```python
# CUSTOM: Added caching to improve performance for our use case
# where the same data is requested frequently.
# Original implementation made API call on every request.
# This cache expires after 5 minutes.
# Related to: internal-ticket-123

from functools import lru_cache
import time

@lru_cache(maxsize=100)
def fetch_data(key):
    # Original implementation
    return api_call(key)
```

### Step 4: Test Your Changes

```bash
# Run existing test suite
npm test  # or pytest, cargo test, etc.

# Add tests for your modifications if needed
# (Keep tests in a separate custom test file if possible)

# Manual testing
# Document test steps in MODIFICATIONS.md
```

### Step 5: Document Your Changes

Update `MODIFICATIONS.md`:

```markdown
## Version 1.1.0 - 2025-11-08

### Added
- **Caching layer for data fetching**
  - Files: `src/data/fetcher.py`
  - Implementation: LRU cache with 5-minute expiration
  - Reason: Reduces API calls by 80% for our access patterns
  - Testing: Verified cache hit rate in production simulation
  - Impact: No breaking changes, backward compatible

### Technical Details
- Cache size: 100 entries (configurable via `CACHE_SIZE` env var)
- Expiration: 300 seconds (configurable via `CACHE_EXPIRY` env var)
- Memory impact: ~5MB for full cache
- Performance: Reduced average response time from 200ms to 20ms
```

### Step 6: Commit Your Changes

```bash
git add <modified-files>
git commit -m "custom: add caching layer to data fetcher

Implements LRU caching with configurable size and expiration.
Reduces API calls significantly for repeated requests.

Files modified:
- src/data/fetcher.py

Related to: internal-ticket-123
"
```

### Step 7: Merge to Main

```bash
# Review your changes
git log main..HEAD

# Switch to main and merge
git checkout main
git merge custom/<feature-name>

# Push to your repository
git push origin main

# Optionally keep the feature branch for reference
# or delete it
git branch -d custom/<feature-name>
```

## Types of Modifications

### Configuration Changes

**Preferred approach** - Least intrusive:

```python
# Instead of hardcoding:
TIMEOUT = 30

# Use configuration:
import os
TIMEOUT = int(os.getenv('TIMEOUT', '30'))
```

### Feature Additions

When adding new features:
- Keep them modular
- Use interfaces/hooks if available
- Consider plugin architecture
- Document dependencies

### Bug Fixes

For bugs not yet fixed upstream:
- Document the bug clearly
- Reference upstream issue if exists
- Mark with `BUGFIX:` comment
- Consider contributing to upstream

```python
# BUGFIX: Handle null values in API response
# Upstream issue: https://github.com/project/repo/issues/123
# This fix will be removed when upstream merges #456
if response and response.data is not None:
    process_data(response.data)
```

### Performance Optimizations

Document performance improvements:
- Before and after metrics
- Trade-offs (memory vs speed, etc.)
- Testing methodology
- Conditions when optimization helps

### Integration Customizations

For integrating with other systems:
- Use adapter patterns
- Keep integration code separate
- Document external dependencies
- Consider environment-specific configs

## Marking Custom Code

### Inline Comments

Use clear markers for custom code:

```python
# CUSTOM START: Authentication integration
def check_auth(user):
    # Our custom authentication logic
    return our_auth_service.verify(user)
# CUSTOM END

# Original code continues...
```

### Preprocessor Directives (C/C++)

```c
#ifdef CUSTOM_BUILD
    // Custom implementation
#else
    // Original implementation
#endif
```

### Feature Flags

```python
ENABLE_CUSTOM_FEATURE = os.getenv('ENABLE_CUSTOM_FEATURE', 'false') == 'true'

if ENABLE_CUSTOM_FEATURE:
    # Custom behavior
else:
    # Original behavior
```

## Tracking Modifications

### Keep a Modification Registry

In `MODIFICATIONS.md`, maintain a registry:

```markdown
## Quick Reference - All Modifications

| File | Type | Date | Description |
|------|------|------|-------------|
| `src/auth.py` | Feature | 2025-11-01 | Custom auth integration |
| `src/cache.py` | Performance | 2025-11-08 | Added caching layer |
| `config/defaults.py` | Config | 2025-11-05 | Custom default values |
```

### Use Git Tags

Tag stable versions of your custom fork:

```bash
# Tag a release
git tag -a v1.0.0-custom -m "Custom version 1.0.0 based on upstream v1.0.0"
git push origin v1.0.0-custom
```

### Maintain Changelogs

Keep `CHANGELOG.md` for version history:

```markdown
# Changelog

## [1.1.0-custom] - 2025-11-08
### Added
- Caching layer for improved performance

### Based On
- Upstream version: v1.0.0

## [1.0.0-custom] - 2025-11-01
### Added
- Initial custom version
- Custom authentication integration

### Based On
- Upstream version: v1.0.0
```

## Reviewing Modifications

### Regular Audits

Periodically review your modifications:
- Are they still necessary?
- Can any be removed?
- Should any be contributed upstream?
- Do they still work with current upstream?

### Checklist for Review

```markdown
- [ ] All modifications documented in MODIFICATIONS.md
- [ ] Code comments explain WHY, not just WHAT
- [ ] Tests pass with modifications
- [ ] No security vulnerabilities introduced
- [ ] Performance impact assessed
- [ ] Compatible with latest upstream version
- [ ] No unnecessary changes
```

## Minimizing Future Conflicts

### Strategy 1: Extend, Don't Modify

When possible, extend rather than modify:

```python
# Instead of modifying original class:
class OriginalClass:
    def method(self):
        pass

# Extend it:
class CustomClass(OriginalClass):
    def method(self):
        # Add custom behavior
        result = super().method()
        # Post-process
        return result
```

### Strategy 2: Use Hooks and Callbacks

If the project provides hooks:

```javascript
// Use provided hooks instead of modifying core
original.on('event', (data) => {
    // Your custom handling
});
```

### Strategy 3: Configuration Files

Prefer configuration over code changes:

```yaml
# config/custom.yml
features:
  custom_auth: true
  caching: true
  
performance:
  cache_size: 100
  timeout: 30
```

### Strategy 4: Separate Custom Modules

Keep custom code in separate files:

```
project/
├── src/
│   ├── core/          # Original files
│   └── custom/        # Your additions
│       ├── __init__.py
│       ├── auth.py
│       └── cache.py
```

## Contributing Back to Upstream

### When to Contribute

Consider contributing if:
- Bug fix that benefits everyone
- Feature that's generally useful
- Performance improvement without trade-offs
- Documentation improvements

### How to Contribute

1. **Check Upstream Guidelines**
   - Read CONTRIBUTING.md
   - Follow their code style
   - Understand their process

2. **Prepare Your Contribution**
   - Extract relevant changes
   - Remove organization-specific code
   - Add tests
   - Update documentation

3. **Submit Pull Request**
   - Clear description
   - Reference any related issues
   - Be responsive to feedback

4. **Track Your PR**
   - Update your MODIFICATIONS.md with PR link
   - If accepted, plan to remove your custom version
   - If rejected, document why in MODIFICATIONS.md

## Cleaning Up Old Modifications

### When to Remove

Remove modifications when:
- They're merged into upstream
- No longer needed
- Better solution available
- Causing too many conflicts

### How to Remove

```bash
# Create cleanup branch
git checkout -b cleanup/<modification-name>

# Remove the modification
# Edit files to remove custom code

# Test
npm test

# Commit
git commit -m "cleanup: remove <modification-name>

This modification is no longer needed because:
- [reason for removal]

Updated files:
- [list of files]
"

# Merge to main
git checkout main
git merge cleanup/<modification-name>
```

### Document Removal

Update `MODIFICATIONS.md`:

```markdown
## Removed Modifications

### Caching Layer - Removed 2025-12-01
- **Originally Added**: 2025-11-08
- **Reason for Removal**: Upstream implemented native caching in v2.0.0
- **Migration**: Updated to use upstream caching API
```

## Tools and Automation

### Diff Scripts

Create helpful scripts:

```bash
#!/bin/bash
# show-custom-changes.sh
# Shows all commits with "custom:" prefix

git log --oneline --grep="custom:" --all
```

### Modification Report

Generate reports of current modifications:

```bash
#!/bin/bash
# generate-mod-report.sh
# Lists all files with CUSTOM markers

grep -rn "CUSTOM" src/ --include="*.py" --include="*.js" --include="*.go"
```

## Best Practices Summary

1. ✅ **Minimize Changes**: Only modify what's absolutely necessary
2. ✅ **Document Everything**: Explain WHY, not just WHAT
3. ✅ **Use Feature Flags**: Make modifications toggleable
4. ✅ **Test Thoroughly**: Don't break existing functionality
5. ✅ **Stay Organized**: Use clear naming and structure
6. ✅ **Regular Reviews**: Audit modifications periodically
7. ✅ **Consider Upstream**: Can changes be contributed back?
8. ✅ **Plan for Merges**: Minimize future merge conflicts

## Related Documentation

- [Syncing with Upstream](./syncing-upstream.md) - How to sync with upstream
- [CONTRIBUTING.md](../../CONTRIBUTING.md) - General contribution guidelines
- [README.md](../../README.md) - Repository overview
