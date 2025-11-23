# Syncing with Upstream

This guide provides detailed workflows for keeping your custom versions synchronized with upstream changes.

## Quick Reference

```bash
# Fetch upstream changes
git fetch upstream

# Preview what's new
git log HEAD..upstream/main --oneline

# Merge upstream changes
git merge upstream/main

# Push to your repository
git push origin main
```

## Detailed Workflow

### Step 1: Prepare Your Environment

Before syncing, ensure your working directory is clean:

```bash
cd projects/<project-name>
git status
```

If you have uncommitted changes:
```bash
# Option 1: Commit them
git add .
git commit -m "WIP: save current work"

# Option 2: Stash them
git stash save "Work in progress before sync"
```

### Step 2: Fetch Upstream Changes

```bash
# Fetch all upstream branches and tags
git fetch upstream

# Fetch a specific branch
git fetch upstream main
```

### Step 3: Review Incoming Changes

Before merging, understand what's coming:

```bash
# View commits you don't have
git log HEAD..upstream/main --oneline

# See detailed changes
git log HEAD..upstream/main --stat

# View specific file changes
git diff HEAD..upstream/main -- path/to/file
```

### Step 4: Merge Strategy

Choose the appropriate merge strategy:

#### Strategy A: Clean Merge (Recommended)

```bash
git checkout main
git merge upstream/main
```

#### Strategy B: Rebase (Use with caution)

```bash
git checkout main
git rebase upstream/main
# Note: Only if you haven't shared your branch yet
```

#### Strategy C: Cherry-pick Specific Commits

```bash
# Pick specific commits you want
git cherry-pick <commit-hash>
```

### Step 5: Handle Conflicts

If conflicts occur:

1. **Identify Conflicted Files**
   ```bash
   git status
   ```

2. **Understand the Conflict**
   ```bash
   git diff
   ```

3. **Resolve Each Conflict**
   - Open each file in your editor
   - Look for conflict markers: `<<<<<<<`, `=======`, `>>>>>>>`
   - Decide which changes to keep
   - Remove conflict markers

4. **Test Your Resolution**
   ```bash
   # Run project tests
   npm test  # or pytest, cargo test, etc.
   
   # Manual testing
   # Verify functionality works as expected
   ```

5. **Complete the Merge**
   ```bash
   git add <resolved-files>
   git commit
   # Git will provide a default merge commit message
   ```

### Step 6: Update Documentation

After successfully syncing:

1. **Update UPSTREAM.md**
   ```markdown
   - **Version Based On**: <new-commit-hash>
   - **Last Synced**: <today's date>
   ```

2. **Update MODIFICATIONS.md**
   ```markdown
   ## Sync - <today's date>
   ### Updated
   - Synced with upstream version <version>
   - Resolved conflicts in: <list files if any>
   ### Verified
   - Tested custom features
   - All modifications still functional
   ```

### Step 7: Push Changes

```bash
# Push to your repository
git push origin main

# If you have other branches that need updating
git checkout <custom-branch>
git merge main
git push origin <custom-branch>
```

## Conflict Resolution Examples

### Example 1: Both Modified Same Function

**Upstream changes:**
```python
def calculate(x, y):
    return x + y + 10  # Added bonus
```

**Your custom version:**
```python
def calculate(x, y):
    """Calculate with validation"""  # Added docstring
    if x < 0 or y < 0:
        raise ValueError("Negative values not allowed")
    return x + y
```

**Resolution - Combine both changes:**
```python
def calculate(x, y):
    """Calculate with validation"""
    if x < 0 or y < 0:
        raise ValueError("Negative values not allowed")
    return x + y + 10  # Added bonus
```

### Example 2: Upstream Refactored Your Modified Code

**Strategy:**
1. Understand the upstream refactoring
2. Re-apply your modifications to the new structure
3. Test thoroughly
4. Document the resolution

## Syncing Frequency Recommendations

### Active Development
- Sync weekly or bi-weekly
- Stay close to upstream to minimize conflicts

### Stable Maintenance
- Sync monthly or with each upstream release
- Review changelogs for breaking changes

### Critical Projects
- Sync immediately for security updates
- Test thoroughly in staging environment

## Automation Options

### Create a Sync Script

Create `scripts/sync-upstream.sh`:

```bash
#!/bin/bash
PROJECT=$1

if [ -z "$PROJECT" ]; then
    echo "Usage: ./sync-upstream.sh <project-name>"
    exit 1
fi

cd "projects/$PROJECT" || exit 1

echo "Fetching upstream changes..."
git fetch upstream

echo "Changes from upstream:"
git log HEAD..upstream/main --oneline

read -p "Proceed with merge? (y/n) " -n 1 -r
echo
if [[ $REPLY =~ ^[Yy]$ ]]; then
    git merge upstream/main
    echo "Don't forget to update UPSTREAM.md and MODIFICATIONS.md!"
fi
```

### Git Aliases

Add to your `~/.gitconfig`:

```ini
[alias]
    sync-upstream = "!f() { git fetch upstream && git merge upstream/main; }; f"
    upstream-log = "log HEAD..upstream/main --oneline"
```

## Troubleshooting

### Issue: Merge Conflicts Are Too Complex

**Solution:**
1. Abort the merge: `git merge --abort`
2. Create a new branch: `git checkout -b sync-attempt`
3. Try merging there first
4. If successful, merge to main

### Issue: Lost Custom Modifications After Merge

**Solution:**
```bash
# Find your modifications before the merge
git reflog
git checkout <commit-before-merge>

# Create a recovery branch
git checkout -b recovery

# Try merging again, more carefully
git merge upstream/main
```

### Issue: Upstream Removed a File You Modified

**Decision needed:**
1. Keep your modified version (restore the file)
2. Accept the removal (do nothing)
3. Integrate your changes elsewhere in the codebase

## Best Practices

1. **Regular Syncing**: Don't let your fork drift too far
2. **Branch Protection**: Sync in a branch first, merge to main after testing
3. **Document Everything**: Record why you resolved conflicts the way you did
4. **Test Thoroughly**: Run all tests after syncing
5. **Communication**: Note any breaking changes from upstream
6. **Backup**: Keep backups before major syncs

## Related Documentation

- [CONTRIBUTING.md](../../CONTRIBUTING.md) - General contribution guidelines
- [Managing Modifications](./managing-modifications.md) - How to make and track custom changes
