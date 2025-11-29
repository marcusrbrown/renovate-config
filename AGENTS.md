# AGENTS.md

## Project Overview

This repository contains shareable Renovate configuration presets for managing dependencies across GitHub repositories. It provides modular, composable JSON configuration files that define automated dependency update strategies, automerge rules, package grouping, and scheduling policies.

**Key Technologies:**

- Package Manager: `pnpm@10.23.0`
- Release Management: `semantic-release@25.0.2`
- Code Quality: ESLint (`@bfra.me/eslint-config`), Prettier (`@bfra.me/prettier-config`)
- Configuration Format: JSON/JSON5 with Renovate schema validation

**Architecture:**

- `default.json` - Main preset configuration extending base configs
- `onboarding.json` - Configuration for new repository setup
- `archived-repository.json` - Preset for archived repositories
- `.github/renovate.json5` - Self-hosted Renovate configuration

## Setup Commands

```bash
# Install dependencies
pnpm install

# Bootstrap project (prefer offline, minimal logs)
pnpm run bootstrap
```

## Development Workflow

### Initial Setup

1. Clone the repository
2. Ensure `pnpm@10.23.0` is installed (managed via `packageManager` field)
3. Run `pnpm install` to install dependencies
4. Git hooks will be automatically configured via `simple-git-hooks`

### Making Changes

- Edit JSON configuration files following Renovate schema
- Run linting before committing (automated via pre-commit hook)
- Use conventional commit format for all commits

### Configuration Files

**Renovate Presets (JSON):**

- Follow `https://docs.renovatebot.com/renovate-schema.json` schema
- Use 2-space indentation
- Maintain backward compatibility with existing consumers

**Workflow Files (YAML):**

- Located in `.github/workflows/`
- Use 2-space indentation
- Follow GitHub Actions best practices

## Testing Instructions

### Linting and Formatting

```bash
# Run all linting checks
pnpm run lint

# Auto-fix linting and formatting issues
pnpm run fix
```

### Pre-commit Validation

The project uses `simple-git-hooks` with `lint-staged` to automatically validate changes:

- Formats and lints: `*.{js,json,jsx,md,toml,ts,tsx,yml,yaml}`
- Runs automatically on `git commit`
- Uses ESLint with Prettier integration

### Manual Validation

Before committing, ensure:

1. All JSON files are valid and follow Renovate schema
2. `pnpm run lint` passes without errors
3. Configuration changes are tested in a test repository
4. Documentation is updated if adding/changing presets

### CI Validation

The main workflow (`.github/workflows/main.yaml`) runs on:

- Push to `main` branch
- Pull requests targeting `main`
- Manual workflow dispatch

**CI Checks:**

- Linting via ESLint
- Dependency installation validation
- Semantic release dry-run (on PRs)

## Code Style

### JSON Configuration Files

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "description": "Clear description of preset purpose",
  "extends": ["base-preset"],
  "packageRules": [
    {
      "description": "Always describe package rule purpose",
      "matchPackageNames": ["package-name"],
      "enabled": true
    }
  ]
}
```

**Rules:**

- Always include `$schema` for validation
- Add `description` field for all presets and package rules
- Use 2-space indentation (enforced by Prettier)
- Group related package rules together
- Use descriptive rule names

### Prettier Configuration

Extends `@bfra.me/prettier-config/120-proof`:

- Print width: 120 characters
- Tab width: 2 spaces
- Single quotes for most files
- Special handling for `.vscode` (4 spaces) and `.md` files (double quotes)

### ESLint Configuration

Uses `@bfra.me/eslint-config` with Prettier integration:

- Extends from `eslint-config-prettier`
- Integrates `eslint-plugin-prettier`
- Supports JavaScript, JSON, YAML, Markdown

## Build and Deployment

### Release Process

This project uses `semantic-release` for automated versioning and releases:

**Release Triggers:**

- Automatic: Merges to `main` branch
- Manual: Workflow dispatch with dry-run option

**Release Steps:**

1. Conventional commits are analyzed for version bump
2. `semantic-release` generates changelog
3. Git tag is created
4. GitHub release is published
5. `semantic-release-export-data` exports release metadata

**Commit Message Format:**

```text
type(scope): subject

body

footer
```

Common types:

- `feat:` - New feature (minor version bump)
- `fix:` - Bug fix (patch version bump)
- `docs:` - Documentation only
- `build:` - Build system changes
- `chore:` - Maintenance tasks

**Breaking Changes:** Include `BREAKING CHANGE:` in footer for major version bump

### Version Management

- Version: `0.0.0-development` (managed by semantic-release)
- All releases are automated via CI/CD
- No manual version updates in `package.json`

## Pull Request Guidelines

### Title Format

Follow conventional commit format:

```text
type(scope): Brief description
```

Examples:

```text
feat(presets): add new automerge rules for security updates
fix(config): correct schedule configuration syntax
docs(readme): update usage examples
```

### Required Checks Before Submission

```bash
# Run all linting and formatting
pnpm run fix

# Verify no errors
pnpm run lint
```

### PR Process

1. Create feature branch from `main`
2. Make changes following code style guidelines
3. Commit using conventional commit format
4. Push and create PR
5. Wait for CI checks to pass
6. Address review feedback
7. Squash merge to `main` (automated release will follow)

### Automerge Rules

The repository itself uses `local>marcusrbrown/renovate-config` preset, which includes:

- Immediate PR creation for own project dependencies
- No minimum release age for `@bfra.me/*` and `@marcusrbrown/*` packages
- Grouped updates for `semantic-release` and `conventional-changelog`
- Post-upgrade tasks: `pnpm run bootstrap && pnpm run fix`

## Working with Renovate Presets

### Preset Structure

**Base Preset (`default.json`):**

- Extends `github>bfra-me/renovate-config#v4`
- Adds personal customizations
- Sets schedule to "at any time"
- Disables rate limiting
- Preserves semver ranges

**Key Features:**

- Auto-assigns and requests review from `marcusrbrown`
- Groups semantic-release major updates
- Fast-tracks personal project dependencies
- Enforces minimum versions for preset itself (>=4.0.0)

### Testing Preset Changes

1. Create test repository or use existing test repo
2. Update `.github/renovate.json` to use branch:

   ```json
   {
     "extends": ["github>marcusrbrown/renovate-config:default#branch-name"]
   }
   ```

3. Trigger Renovate manually or wait for scheduled run
4. Verify PR creation, grouping, and automerge behavior
5. Check logs in Renovate dashboard for any errors

### Common Preset Patterns

**Package Rule Examples:**

Group related updates:

```json
{
  "description": "Group related updates",
  "matchPackageNames": ["package-a", "package-b"],
  "groupName": "related packages"
}
```

Automerge patch and minor:

```json
{
  "description": "Automerge patch and minor",
  "matchUpdateTypes": ["patch", "minor"],
  "automerge": true
}
```

Match by source URL:

```json
{
  "description": "Match by source URL",
  "matchSourceUrlPrefixes": ["https://github.com/org"],
  "schedule": ["before 5am"]
}
```

## Security Considerations

### Dependency Management

- Security updates have no minimum release age
- OpenSSF Scorecard integration for supply chain security
- `npm:unpublishSafe` prevents installation of unpublished packages
- GitHub Actions digests pinned to semver tags

### Secrets Management

- GitHub App token for releases (never commit tokens)
- Stored in repository secrets:
  - `APPLICATION_ID` - GitHub App ID
  - `APPLICATION_PRIVATE_KEY` - GitHub App private key

### Workflow Permissions

- Default: `contents: read` (read-only)
- Release job: Uses GitHub App token for write access
- Follows principle of least privilege

## Additional Notes

### Git Hooks

Pre-commit hook automatically runs:

```bash
pnpm exec lint-staged
```

This ensures all staged files pass linting before commit.

### Renovate Self-Hosting

This repository uses the presets it defines:

- See `.github/renovate.json5` for self-referential configuration
- Uses `local>marcusrbrown/renovate-config` to test changes
- Includes post-upgrade tasks to fix formatting after updates

### Monorepo Support

While this is not a monorepo, the presets can be used in monorepo contexts:

- Use package name patterns: `@scope/{/,}**`
- Match by source URL prefix for grouped dependencies
- Configure separate schedules per package ecosystem

### Common Gotchas

1. **JSON vs JSON5**: Use `.json` for presets (consumed externally), `.json5` for local configs
2. **Schema Validation**: Always include `$schema` for IDE autocompletion and validation
3. **Preset Versioning**: Consumers should pin to major versions: `marcusrbrown/renovate-config#v4`
4. **Testing Changes**: Test in separate repository before merging to avoid breaking consumers
5. **Release Timing**: Releases happen automatically after merge to `main` - no manual intervention needed

### Troubleshooting

**Linting Errors:**

```bash
# Check what would be fixed
pnpm run lint

# Apply fixes automatically
pnpm run fix
```

**Git Hooks Not Running:**

```bash
# Reinstall hooks
pnpm run prepare
```

**Dependency Issues:**

```bash
# Clean install
rm -rf node_modules pnpm-lock.yaml
pnpm install
```

**Semantic Release Not Triggering:**

- Verify commit message follows conventional format
- Check CI logs in GitHub Actions
- Ensure merge was to `main` branch
- Verify GitHub App token is valid

## Related Documentation

- [Renovate Documentation](https://docs.renovatebot.com/)
- [Renovate Presets](https://docs.renovatebot.com/config-presets/)
- [Conventional Commits](https://www.conventionalcommits.org/)
- [Semantic Release](https://semantic-release.gitbook.io/)
