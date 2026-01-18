---
title: "Changelog"
version: "2.1"
last_updated: "2026-01-18"
---

# CHANGELOG

All notable changes to the AI Design System.

---

## [2.1.0] - 2026-01-18

### Added
- **PROMPTS/ folder** with 6 task-specific prompt templates:
  - `01-NEW-COMPONENT.md` - Component creation
  - `02-FORM-FIELD.md` - Form field with validation
  - `03-FIX-ACCESSIBILITY.md` - Accessibility fixes
  - `04-DARK-MODE.md` - Dark mode implementation
  - `05-CODE-REVIEW.md` - Code review checklist
  - `06-MIGRATION.md` - Framework migration
- **LAYER-09-CI-AUTOMATION.md** - CI/CD documentation
- **GitHub Actions workflow** (`.github/workflows/design-system.yml`)
- **Markdownlint configuration** (`.markdownlint.json`)
- **Standalone domain files**:
  - `MARKETING-DOMAIN/09a-TEMPLATES.md`
  - `MARKETING-DOMAIN/10a-SETUP.md`
  - `MARKETING-DOMAIN/12a-IMPLEMENTATION.md`
  - `APPLICATION-DOMAIN/09b-COMPONENTS.md`
  - `APPLICATION-DOMAIN/10b-SETUP.md`
  - `APPLICATION-DOMAIN/11b-TESTING.md`
  - `APPLICATION-DOMAIN/12b-IMPLEMENTATION.md`
- **Standalone appendix files**:
  - `APPENDICES/APPENDIX-A-TAILWIND-UTILITIES.md`
  - `APPENDICES/APPENDIX-B-COMPONENT-DECISIONS.md`
  - `APPENDICES/APPENDIX-C-RADIX-PRIMITIVES.md`
  - `APPENDICES/APPENDIX-D-TROUBLESHOOTING.md`
  - `APPENDICES/APPENDIX-E-MIGRATION-PATHS.md`
- YAML front-matter to core files (title, version, audience, dependencies, last_updated)

### Changed
- **LLM-AGENT-GUIDE.md** - Enhanced with task-specific retrieval paths and mandatory output rules
- **LAYER-00-STRATEGIC-DECISIONS.md** - Updated tool versions:
  - Tailwind CSS v3 → v4 (CSS-native config)
  - shadcn/ui v1 → v2 CLI (`npx shadcn@latest`)
  - State: Zustand → Jotai (React 19 compatible)
  - Testing: Jest → Vitest + Playwright
  - Added React 19 (Server Components, Actions)
- **_FILE-MANIFEST.md** - Reflects new file structure
- **00-MASTER.md** - Version bump to 2.1

### Removed
- 12 alias/pointer files (replaced with standalone content):
  - `APPENDICES/A-TAILWIND-UTILITY-REFERENCE.md` (alias)
  - `APPENDICES/B-COMPONENT-DECISION-TREE.md` (alias)
  - `APPENDICES/C-RADIX-UI-PRIMITIVES.md` (alias)
  - `APPENDICES/D-TROUBLESHOOTING-AND-FAQ.md` (alias)
  - `APPENDICES/E-MIGRATION-PATHS.md` (alias)
  - `MARKETING-DOMAIN/09a-TAILWIND-UI-TEMPLATES.md` (alias)
  - `MARKETING-DOMAIN/10a-SETUP-AND-BUILD.md` (alias)
  - `MARKETING-DOMAIN/12a-IMPLEMENTATION-GUIDE.md` (alias)
  - `APPLICATION-DOMAIN/09b-SHADCN-COMPONENTS.md` (alias)
  - `APPLICATION-DOMAIN/10b-SETUP-AND-BUILD.md` (alias)
  - `APPLICATION-DOMAIN/11b-TESTING-STRATEGY.md` (alias)
  - `APPLICATION-DOMAIN/12b-IMPLEMENTATION-GUIDE.md` (alias)
- `APPENDICES-COMBINED.md` (de-merged into 5 standalone files)

### Fixed
- Improved LLM retrieval efficiency (de-merged files load faster)
- Consistent YAML front-matter across all spec files

---

## [2.0.0] - 2026-01-15

### Added
- Initial complete design system (15 canonical files)
- Marketing domain guide (Tailwind UI)
- Application domain guide (shadcn/ui)
- Combined appendices
- LLM Agent Guide
- Status files (PHASE-1-COMPLETE, SYSTEM-COMPLETE, READY-TO-LAUNCH)

---

## Version Numbering

- **Major (X.0.0)**: Breaking changes to file structure or retrieval paths
- **Minor (0.X.0)**: New features, files, or significant enhancements
- **Patch (0.0.X)**: Bug fixes, typos, minor clarifications
