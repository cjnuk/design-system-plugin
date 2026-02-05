# Publishing to Claude Marketplace

This guide documents the process for publishing the design-system plugin to the Claude marketplace.

## Pre-Publication Checklist

### 1. Plugin Metadata

Verify `plugin.json` contains all required fields:

```json
{
  "name": "design-system",
  "version": "2.2.0",
  "description": "AI-powered design system with tokens, components, and patterns for Tailwind + shadcn/ui applications",
  "author": {
    "name": "Your Name",
    "url": "https://github.com/yourusername"
  },
  "repository": "https://github.com/yourusername/design-system-plugin",
  "license": "MIT",
  "keywords": ["design-system", "tailwind", "shadcn", "components", "accessibility"]
}
```

**Required fields:**
- [ ] `name` - Unique identifier (lowercase, hyphens only)
- [ ] `version` - Semantic version (MAJOR.MINOR.PATCH)
- [ ] `description` - Clear, concise purpose (max 200 chars)
- [ ] `author.name` - Maintainer name
- [ ] `license` - SPDX license identifier

**Recommended fields:**
- [ ] `author.url` - GitHub profile or website
- [ ] `repository` - GitHub repo URL for updates
- [ ] `keywords` - 3-5 relevant terms for search

### 2. Documentation

- [ ] `README.md` - Overview, features, screenshots
- [ ] `INSTALLATION.md` - Step-by-step setup
- [ ] `GETTING-STARTED.md` - Quick start for new users
- [ ] `QUICK-REFERENCE.md` - Command/skill cheatsheet

### 3. Skills Validation

All skills must follow the standard structure:

```
skills/
├── ds-init/
│   └── SKILL.md          # Required: Skill definition
├── ds-component/
│   ├── SKILL.md          # Required
│   └── reference.md      # Optional: Extended docs
└── ...
```

**Skill checklist:**
- [ ] Each skill has valid frontmatter (skill, description, triggers)
- [ ] Triggers are unique across all skills
- [ ] No broken internal links
- [ ] Code examples are syntactically correct

### 4. Version Consistency

Ensure version is consistent across:
- [ ] `plugin.json` → `version`
- [ ] `README.md` → badge/header
- [ ] `CHANGELOG.md` → latest entry
- [ ] `marketplace.json` (if using personal marketplace)

### 5. Quality Checks

```bash
# Markdown linting
npx markdownlint-cli2 "**/*.md"

# Link validation
npx markdown-link-check README.md INSTALLATION.md

# YAML validation (for templates)
npx yaml-lint templates/**/*.yaml
```

---

## Distribution Methods

### Method 1: Personal Marketplace (Current)

Your plugin is available via a personal marketplace:

```bash
# Users add your marketplace
claude plugin marketplace add yourusername/claude-marketplace

# Users install plugin
claude plugin install design-system-plugin@yourusername-marketplace
```

**To update the marketplace:**

1. Update plugin files in `design-system-plugin/`
2. Bump version in `plugin.json`
3. Update `marketplace.json`:
   ```json
   {
     "plugins": [{
       "name": "design-system-plugin",
       "version": "2.2.0",  // Update this
       "source": "./plugins/design-system-plugin"
     }]
   }
   ```
4. Commit and push to marketplace repo

### Method 2: Git Clone (Direct)

Users clone directly to their plugins directory:

```bash
cd ~/.claude/plugins
git clone https://github.com/yourusername/design-system-plugin design-system
```

**Advantages:**
- No marketplace dependency
- Easy updates via `git pull`
- Works offline after clone

### Method 3: Official Claude Marketplace (Future)

When the official Claude marketplace launches:

1. Register as a plugin developer at marketplace.anthropic.com (TBD)
2. Submit plugin for review
3. Pass automated checks:
   - Valid plugin.json schema
   - No malicious code patterns
   - Skill definitions well-formed
   - Documentation complete
4. Publish to marketplace
5. Users install via: `claude plugin install design-system`

---

## Release Process

### 1. Prepare Release

```bash
# Update version
# Edit plugin.json, README.md, CHANGELOG.md

# Run quality checks
npx markdownlint-cli2 "**/*.md"

# Commit changes
git add -A
git commit -m "chore: bump version to X.Y.Z"
```

### 2. Create Git Tag

```bash
git tag -a v2.2.0 -m "Release v2.2.0: Feature description"
git push origin main --tags
```

### 3. Create GitHub Release

1. Go to GitHub → Releases → "Draft a new release"
2. Choose the tag you just created
3. Title: `v2.2.0`
4. Description: Copy from CHANGELOG.md
5. Attach ZIP of plugin directory (optional)
6. Publish release

### 4. Update Personal Marketplace

```bash
cd claude-marketplace
# Update marketplace.json version
git add -A
git commit -m "chore: update design-system-plugin to v2.2.0"
git push origin main
```

### 5. Announce

- Update README badges
- Post to relevant communities
- Update documentation site (if any)

---

## Marketplace.json Schema

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "type": "object",
  "required": ["name", "description", "owner", "plugins"],
  "properties": {
    "name": {
      "type": "string",
      "pattern": "^[a-z0-9-]+$",
      "description": "Unique marketplace identifier"
    },
    "description": {
      "type": "string",
      "maxLength": 500
    },
    "owner": {
      "type": "object",
      "required": ["name"],
      "properties": {
        "name": { "type": "string" },
        "url": { "type": "string", "format": "uri" }
      }
    },
    "plugins": {
      "type": "array",
      "items": {
        "type": "object",
        "required": ["name", "source", "version"],
        "properties": {
          "name": { "type": "string" },
          "source": { "type": "string" },
          "version": { "type": "string", "pattern": "^\\d+\\.\\d+\\.\\d+$" },
          "description": { "type": "string" }
        }
      }
    }
  }
}
```

---

## Versioning Strategy

Follow semantic versioning:

| Change Type | Version Bump | Example |
|-------------|--------------|---------|
| Breaking changes to skill APIs | MAJOR | 2.0.0 → 3.0.0 |
| New skills, features, improvements | MINOR | 2.1.0 → 2.2.0 |
| Bug fixes, documentation updates | PATCH | 2.2.0 → 2.2.1 |

**Breaking changes include:**
- Renaming or removing skills
- Changing skill triggers
- Modifying project-config.yaml schema
- Removing template files

---

## Troubleshooting

### Plugin not loading after install

```bash
# Verify plugin structure
ls -la ~/.claude/plugins/design-system/.claude-plugin/

# Should contain plugin.json
```

### Skills not appearing

```bash
# Check skill files exist
ls ~/.claude/plugins/design-system/skills/*/SKILL.md

# Verify frontmatter is valid YAML
head -20 ~/.claude/plugins/design-system/skills/ds-init/SKILL.md
```

### Marketplace not syncing

```bash
# Remove and re-add marketplace
claude plugin marketplace remove yourusername-marketplace
claude plugin marketplace add yourusername/claude-marketplace
```

---

## Future Considerations

When the official Claude marketplace launches, expect:

1. **Automated review** - Plugins may require passing CI checks
2. **Signing** - Plugins may need cryptographic signatures
3. **Metrics** - Download counts, ratings, reviews
4. **Updates** - Automatic update notifications
5. **Monetization** - Paid plugins or sponsorship options

Keep plugin.json fields current to ease migration to official marketplace.
