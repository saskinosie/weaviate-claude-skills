# Release Guide

This guide explains how to create and publish releases for the Weaviate Claude Skills project.

## Versioning Strategy

We follow [Semantic Versioning](https://semver.org/) (SemVer):

- **MAJOR** (1.0.0): Breaking changes to skill interfaces or structure
- **MINOR** (0.1.0): New skills, new features, backward-compatible changes
- **PATCH** (0.0.1): Bug fixes, documentation updates, minor improvements

### Examples:
- `v1.0.0` - Initial stable release
- `v1.1.0` - Add new skill or major feature
- `v1.1.1` - Fix typo, update dependencies
- `v2.0.0` - Change skill structure or remove features

## Creating a Release

### 1. Prepare the Release

Before creating a release:

```bash
# Ensure your local main branch is up to date
git checkout main
git pull origin main

# Review changes since last release
git log v1.0.0..HEAD --oneline  # Replace v1.0.0 with your last release tag

# Run tests (if you have any)
# python -m pytest

# Check that all documentation is up to date
```

### 2. Update Version Information

If you have version numbers in your code or docs, update them:

- [ ] Update any version references in README.md
- [ ] Update CHANGELOG.md (see below)
- [ ] Check skill SKILL.md files for version numbers

### 3. Create CHANGELOG Entry

Add a new section to `CHANGELOG.md` (create this file if it doesn't exist):

```markdown
## [1.1.0] - 2025-01-15

### Added
- New skill: weaviate-advanced-search for complex queries
- Support for multi-tenancy in collection-manager
- Examples for hybrid search in query-agent

### Changed
- Updated weaviate-client to v4.5.0
- Improved error messages in connection skill

### Fixed
- Fixed docker-compose port conflict issue
- Corrected example in data-ingestion skill

### Security
- Updated dependencies to patch vulnerabilities
```

### 4. Commit and Tag

```bash
# Commit any final changes
git add .
git commit -m "Prepare release v1.1.0"

# Create an annotated tag
git tag -a v1.1.0 -m "Release version 1.1.0"

# Push commits and tags
git push origin main
git push origin v1.1.0
```

### 5. Create GitHub Release

#### Option A: Via GitHub Web UI (Recommended)

1. Go to: `https://github.com/saskinosie/weaviate-claude-skills/releases`
2. Click **"Draft a new release"**
3. Click **"Choose a tag"** → Select `v1.1.0` (or type to create new)
4. **Release title**: `v1.1.0 - Brief Description`
5. **Description**: Copy from CHANGELOG.md and format nicely:

```markdown
## What's New in v1.1.0

### 🎉 New Features
- Added weaviate-advanced-search skill for complex queries
- Support for multi-tenancy in collection-manager
- New examples for hybrid search

### 🔧 Improvements
- Updated weaviate-client to v4.5.0
- Better error messages across all skills

### 🐛 Bug Fixes
- Fixed docker-compose port conflict
- Corrected data-ingestion examples

### 📦 Installation
Download the ZIP or clone:
\`\`\`bash
git clone -b v1.1.0 https://github.com/saskinosie/weaviate-claude-skills.git
\`\`\`

### 📚 Documentation
See [README.md](README.md) for installation and usage instructions.

**Full Changelog**: https://github.com/saskinosie/weaviate-claude-skills/compare/v1.0.0...v1.1.0
```

6. Check **"Set as the latest release"** (if applicable)
7. Click **"Publish release"**

#### Option B: Via GitHub CLI

```bash
# Install gh CLI if needed: https://cli.github.com/

# Create release with auto-generated notes
gh release create v1.1.0 \
  --title "v1.1.0 - Brief Description" \
  --notes-file CHANGELOG.md \
  --latest

# Or with manual notes
gh release create v1.1.0 \
  --title "v1.1.0 - Brief Description" \
  --notes "What's new in this release..." \
  --latest
```

### 6. Post-Release

After publishing:

- [ ] Verify the release appears on GitHub
- [ ] Test downloading the release ZIP
- [ ] Update any external documentation or links
- [ ] Announce on social media (see GITHUB_SETUP.md)
- [ ] Share in relevant communities (Weaviate, Claude)

## Release Schedule

Suggested release cadence:

- **Patch releases**: As needed for critical bugs
- **Minor releases**: Monthly or when new skills are added
- **Major releases**: When breaking changes are necessary

## Pre-releases / Beta Versions

For testing major changes before official release:

```bash
# Create a pre-release tag
git tag -a v2.0.0-beta.1 -m "Beta release for v2.0.0"
git push origin v2.0.0-beta.1

# On GitHub, check "Set as a pre-release" when creating
```

## Hotfix Releases

For critical bugs in production:

```bash
# Create a hotfix branch from the release tag
git checkout -b hotfix/v1.1.1 v1.1.0

# Make your fix
git add .
git commit -m "Fix critical bug in connection skill"

# Merge back to main
git checkout main
git merge hotfix/v1.1.1

# Tag and release
git tag -a v1.1.1 -m "Hotfix: Fix critical connection bug"
git push origin main
git push origin v1.1.1

# Create GitHub release as above
```

## Rollback a Release

If you need to rollback a release:

```bash
# Delete the tag locally and remotely
git tag -d v1.1.0
git push origin :refs/tags/v1.1.0

# Delete the GitHub release (via UI or CLI)
gh release delete v1.1.0
```

Then fix issues and re-release.

## Automated Releases (Optional)

Consider setting up GitHub Actions to automate releases:

Create `.github/workflows/release.yml`:

```yaml
name: Create Release

on:
  push:
    tags:
      - 'v*'

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Create Release
        uses: softprops/action-gh-release@v1
        with:
          generate_release_notes: true
          draft: false
          prerelease: false
```

## Questions?

Open an issue at https://github.com/saskinosie/weaviate-claude-skills/issues
