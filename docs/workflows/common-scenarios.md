# Common Scenarios

This guide covers common scenarios you'll encounter when maintaining custom versions of open source projects.

## Scenario 1: Starting Fresh with a New Project

**Situation**: You want to start maintaining a custom version of an open source project.

**Steps**:

1. **Research the Project**
   - Review the project's license
   - Check how active development is
   - Look for existing forks and their reasons
   - Assess how frequently it's updated

2. **Set Up Locally**
   ```bash
   cd projects/
   git clone https://github.com/original/project.git project-name
   cd project-name
   git remote rename origin upstream
   git remote add origin https://github.com/yourusername/opensource-code-repos.git
   ```

3. **Create Documentation**
   ```bash
   cp ../../docs/templates/UPSTREAM.md .
   cp ../../docs/templates/MODIFICATIONS.md .
   # Edit both files with project details
   ```

4. **Initial Commit**
   ```bash
   git add UPSTREAM.md MODIFICATIONS.md
   git commit -m "docs: add tracking documentation for custom version"
   git push origin main
   ```

## Scenario 2: Urgent Bug Fix Needed

**Situation**: You discover a critical bug that needs immediate fixing, and upstream hasn't addressed it yet.

**Steps**:

1. **Check Upstream**
   ```bash
   # Search for related issues
   # Check if a fix is in progress
   ```

2. **Quick Fix Branch**
   ```bash
   git checkout -b hotfix/critical-bug-name
   ```

3. **Implement Minimal Fix**
   - Fix only what's necessary
   - Add clear comments
   - Mark with `BUGFIX:` or `HOTFIX:`

4. **Test Thoroughly**
   ```bash
   # Run test suite
   npm test
   # Manual verification
   ```

5. **Document and Deploy**
   ```bash
   # Update MODIFICATIONS.md
   git commit -m "hotfix: fix critical bug in component X
   
   This fixes [brief description].
   Upstream issue: [link if exists]
   Will monitor upstream for official fix."
   
   git checkout main
   git merge hotfix/critical-bug-name
   git push origin main
   ```

6. **Consider Contributing**
   - If the fix is clean and general, submit a PR to upstream
   - Track the upstream PR in your MODIFICATIONS.md

## Scenario 3: Upstream Made Breaking Changes

**Situation**: You sync with upstream and they've made breaking changes to an area you've modified.

**Steps**:

1. **Don't Panic**
   ```bash
   # First, see what broke
   git merge upstream/main
   # If conflicts, review them carefully
   ```

2. **Understand Upstream Changes**
   ```bash
   # Read upstream changelog/release notes
   git log upstream/main --grep="breaking"
   # Check upstream documentation
   ```

3. **Assess Impact**
   - What broke in your custom code?
   - Can you adapt to new API?
   - Is the old behavior still available?

4. **Choose Strategy**
   
   **Option A - Adapt to Changes** (Recommended):
   ```bash
   # Update your custom code to work with new upstream
   # This keeps you aligned with upstream direction
   ```
   
   **Option B - Stay on Old Version** (Short term):
   ```bash
   git merge --abort
   # Document in UPSTREAM.md that you're pinned to older version
   # Plan migration path to newer version
   ```
   
   **Option C - Abstract the Changes**:
   ```python
   # Create compatibility layer
   def our_function():
       if NEW_UPSTREAM_API:
           return new_upstream_method()
       else:
           return old_upstream_method()
   ```

5. **Document Decision**
   ```markdown
   ## Sync - 2025-11-08
   ### Breaking Changes
   - Upstream changed X to Y in version 2.0
   - Decision: [Adapted/Pinned/Abstracted]
   - Impact: [description]
   - Migration plan: [if staying on old version]
   ```

## Scenario 4: Multiple Custom Features Conflict

**Situation**: You have several custom features that interact in unexpected ways.

**Steps**:

1. **Identify the Interaction**
   ```bash
   # Review recent changes
   git log --oneline --grep="custom:" -10
   ```

2. **Isolate the Issue**
   - Test each feature independently
   - Find which combination causes the problem

3. **Refactor for Compatibility**
   ```bash
   git checkout -b refactor/fix-feature-interaction
   # Make necessary changes
   ```

4. **Add Integration Tests**
   ```python
   # test_custom_features_integration.py
   def test_feature_a_and_b_together():
       # Ensure features work together
       pass
   ```

5. **Document the Interaction**
   ```markdown
   ## Known Interactions
   - Feature A + Feature B: [description of interaction]
   - Testing: [how to verify they work together]
   ```

## Scenario 5: Want to Contribute Back to Upstream

**Situation**: Your custom modification would benefit the broader community.

**Steps**:

1. **Prepare the Contribution**
   ```bash
   # Create a clean branch from upstream
   git fetch upstream
   git checkout -b contribution/feature-name upstream/main
   ```

2. **Extract Your Changes**
   - Remove organization-specific code
   - Make it configurable if needed
   - Ensure it follows upstream style

3. **Add Tests and Documentation**
   - Follow upstream testing conventions
   - Update relevant documentation
   - Add examples if appropriate

4. **Review Upstream Guidelines**
   - Check CONTRIBUTING.md
   - Look at recent merged PRs
   - Understand their review process

5. **Submit Pull Request**
   ```bash
   git push origin contribution/feature-name
   # Create PR on upstream repository
   ```

6. **Track the PR**
   Update MODIFICATIONS.md:
   ```markdown
   ### Feature X
   - Status: Submitted to upstream
   - PR: https://github.com/original/project/pull/123
   - Date: 2025-11-08
   - Plan: If accepted, will remove custom version
   ```

## Scenario 6: Upstream Project Becomes Inactive

**Situation**: The upstream project hasn't been updated in months/years, but you still need it.

**Steps**:

1. **Assess the Situation**
   - Check if project is officially abandoned
   - Look for community forks
   - Evaluate alternative projects

2. **Document the Status**
   Update UPSTREAM.md:
   ```markdown
   ## Status
   - **Upstream Status**: Inactive (last commit: 2023-06-15)
   - **Our Strategy**: Maintaining as independent fork
   - **Alternative Projects**: [list alternatives considered]
   ```

3. **Decide on Strategy**
   
   **Option A - Full Maintenance**:
   - Accept that you're now the maintainer
   - Consider making your fork the "primary" version
   - Update README to reflect this
   
   **Option B - Minimal Maintenance**:
   - Only fix critical issues
   - Plan migration to alternative
   
   **Option C - Migration**:
   - Find replacement project
   - Plan transition timeline

4. **Communicate the Decision**
   - Update repository documentation
   - Inform stakeholders
   - Plan for long-term sustainability

## Scenario 7: Need Multiple Versions for Different Environments

**Situation**: You need different customizations for dev, staging, and production.

**Steps**:

1. **Branch Strategy**
   ```bash
   main              # Production version
   ├── staging       # Staging-specific changes
   └── dev           # Development-specific changes
   ```

2. **Use Configuration**
   ```python
   # config/environment.py
   import os
   
   ENV = os.getenv('ENVIRONMENT', 'production')
   
   if ENV == 'development':
       # Dev-specific settings
   elif ENV == 'staging':
       # Staging-specific settings
   else:
       # Production settings
   ```

3. **Feature Flags**
   ```python
   ENABLE_DEBUG = os.getenv('ENABLE_DEBUG', 'false') == 'true'
   ENABLE_EXPERIMENTAL = os.getenv('ENABLE_EXPERIMENTAL', 'false') == 'true'
   ```

4. **Document Environment Differences**
   ```markdown
   ## Environment Variations
   
   | Feature | Dev | Staging | Production |
   |---------|-----|---------|------------|
   | Debug Mode | ✓ | ✓ | ✗ |
   | Experimental | ✓ | ✗ | ✗ |
   | Caching | ✗ | ✓ | ✓ |
   ```

## Scenario 8: Security Vulnerability in Your Custom Code

**Situation**: You discover a security issue in code you've modified.

**Steps**:

1. **Immediate Assessment**
   - How severe is the vulnerability?
   - Is it in production?
   - What's exposed?

2. **Quick Fix**
   ```bash
   git checkout -b security/fix-vulnerability
   # Fix the issue
   git commit -m "security: fix [brief description]"
   git checkout main
   git merge security/fix-vulnerability
   git push origin main
   ```

3. **Notify Stakeholders**
   - If in production, follow incident response
   - Document the fix
   - Update security documentation

4. **Review and Prevent**
   - Audit similar code
   - Add security tests
   - Document lessons learned

5. **Consider Disclosure**
   - If it affects upstream, notify them
   - Follow responsible disclosure practices

## Scenario 9: Large Upstream Refactoring

**Situation**: Upstream completely refactored code you've modified.

**Steps**:

1. **Pause Before Merging**
   ```bash
   # Don't immediately merge
   git fetch upstream
   # Review changes first
   git diff main upstream/main
   ```

2. **Understand the Refactoring**
   - Read upstream documentation
   - Check migration guides
   - Review their changes carefully

3. **Plan Your Approach**
   
   **Option A - Re-implement on New Base**:
   ```bash
   git checkout -b reimplement-on-new-base upstream/main
   # Re-apply your modifications to new structure
   ```
   
   **Option B - Staged Migration**:
   ```bash
   # Merge in steps
   git merge upstream/v2.0  # First milestone
   # Test and fix
   git merge upstream/v2.1  # Next milestone
   ```

4. **Test Extensively**
   - Create comprehensive test plan
   - Test in staging environment
   - Verify all custom features still work

5. **Document the Migration**
   ```markdown
   ## Major Refactoring - 2025-11-08
   - Migrated to upstream version 2.0 (major refactor)
   - Re-implemented custom features:
     - Feature A: Now in module X instead of Y
     - Feature B: Adapted to new API pattern
   - Testing: Full regression test passed
   - Rollback plan: Version 1.9.9-custom tagged for rollback
   ```

## Scenario 10: Deciding to Stop Maintaining

**Situation**: You no longer need the custom version.

**Steps**:

1. **Evaluate Options**
   - Can you use upstream directly now?
   - Were your changes merged upstream?
   - Is there a better alternative?

2. **Plan the Transition**
   ```markdown
   ## Deprecation Plan
   - **Date Decided**: 2025-11-08
   - **Reason**: Features merged into upstream v3.0
   - **Timeline**:
     - 2025-11-15: Switch to upstream in dev
     - 2025-11-22: Switch to upstream in staging
     - 2025-11-29: Switch to upstream in production
   - **Cleanup Date**: 2025-12-06
   ```

3. **Archive the Project**
   ```bash
   # Add DEPRECATED.md
   echo "# Deprecated
   
   This custom version is no longer maintained.
   
   ## Reason
   [Explanation]
   
   ## Migration
   Use upstream version X.Y.Z
   
   ## Archive Date
   2025-12-06" > DEPRECATED.md
   
   git add DEPRECATED.md
   git commit -m "docs: deprecate custom version"
   ```

4. **Remove from Active Projects**
   ```bash
   # Move to archive directory
   mkdir -p ../archived-projects
   git mv projects/project-name ../archived-projects/
   ```

## Quick Reference

| Scenario | Key Action | Risk Level |
|----------|------------|------------|
| New Project | Document thoroughly | Low |
| Bug Fix | Test and consider upstream | Medium |
| Breaking Changes | Understand before adapting | High |
| Inactive Upstream | Decide on long-term strategy | Medium |
| Security Issue | Fix immediately, then review | Critical |
| Stop Maintaining | Plan transition carefully | Low |

## Getting Help

When stuck:
1. Review upstream documentation and issues
2. Check if others have similar custom versions
3. Consult project-specific documentation
4. Document your decision-making process

## Related Documentation

- [Syncing with Upstream](./syncing-upstream.md)
- [Managing Modifications](./managing-modifications.md)
- [CONTRIBUTING.md](../../CONTRIBUTING.md)
