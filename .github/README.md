# GitHub Actions Workflows

This directory contains automated workflows for maintaining documentation quality and managing releases.

## Workflows

### 1. Documentation Quality (`documentation-quality.yml`)

Runs on every push and pull request to ensure documentation meets quality standards.

**Jobs:**
- **Markdown Linting**: Validates markdown syntax and formatting using markdownlint
- **Link Validation**: Checks all links (internal and external) for validity
- **Spell Checking**: Runs spell check on all markdown files using cspell
- **Structure Validation**: Ensures all required documentation files are present

**Triggers:**
- Push to `main` and `claude/**` branches
- Pull requests to `main`
- Manual dispatch

### 2. Continuous Integration (`ci.yml`)

Comprehensive CI pipeline for building and validating the documentation repository.

**Jobs:**
- **Build and Validate**: Validates repository structure, counts files, checks metadata
- **Security Scan**: Runs Trivy vulnerability scanner
- **Accessibility Check**: Validates images have alt text and other accessibility concerns

**Triggers:**
- Push to `main` and `claude/**` branches
- Pull requests to `main`
- Manual dispatch

**Artifacts:**
- Documentation quality report (available for 30 days)

### 3. Pull Request Validation (`pr-validation.yml`)

Automated validation and management of pull requests.

**Jobs:**
- **PR Title Check**: Validates PR titles follow conventional commit format
- **PR Size Check**: Warns about large PRs (>20 files changed)
- **PR Description Check**: Ensures PR has adequate description (>50 chars)
- **Documentation Impact**: Automatically labels PRs that change documentation
- **Auto-assign Reviewers**: Welcomes contributors and assigns reviewers
- **PR Checklist**: Validates completion of PR checklist items

**Triggers:**
- Pull request opened, synchronized, reopened, or edited

### 4. Release Documentation (`release.yml`)

Manages documentation releases and publishes to GitHub Pages.

**Jobs:**
- **Create Release**:
  - Generates changelog from git history
  - Creates documentation archive (ZIP)
  - Creates GitHub release with artifacts

- **Publish Pages**:
  - Builds documentation site
  - Publishes to GitHub Pages
  - Creates simple HTML index

**Triggers:**
- Push tags matching `v*.*.*` (e.g., v1.0.0)
- Manual dispatch with version input

## Configuration Files

### `.markdownlint.json`
Configuration for markdown linting rules:
- Line length: 120 characters (excluding code blocks and tables)
- Allows HTML elements: `<br>`, `<details>`, `<summary>`, `<kbd>`
- Allows duplicate headings in different sections

### `.cspell.json`
Spell checking configuration:
- Custom dictionary for project-specific terms (Codeflow, OpenCode, MCP, etc.)
- Ignores common paths (node_modules, .git, dist, build)

## Usage

### Running Workflows Locally

While GitHub Actions run automatically, you can validate locally:

```bash
# Validate YAML syntax
python3 -c "import yaml; yaml.safe_load(open('.github/workflows/ci.yml'))"

# Run markdownlint (requires npm install -g markdownlint-cli2)
markdownlint-cli2 "**/*.md"

# Run spellcheck (requires npm install -g cspell)
cspell "**/*.md"
```

### Manual Workflow Dispatch

Some workflows support manual triggering:

1. Go to **Actions** tab in GitHub
2. Select the workflow
3. Click **Run workflow**
4. Select branch and fill in required inputs (if any)

### Creating a Release

To create a new documentation release:

```bash
# Create and push a version tag
git tag -a v1.0.0 -m "Release version 1.0.0"
git push origin v1.0.0
```

This will automatically:
1. Generate a changelog
2. Create a documentation archive
3. Create a GitHub release
4. Publish to GitHub Pages (if configured)

## Workflow Status Badges

Add these to your README to show workflow status:

```markdown
![Documentation Quality](https://github.com/ferg-cod3s/codeflow-project-documentation/workflows/Documentation%20Quality/badge.svg)
![CI](https://github.com/ferg-cod3s/codeflow-project-documentation/workflows/Continuous%20Integration/badge.svg)
```

## Troubleshooting

### Workflow Failures

1. **Markdown Linting Fails**:
   - Check `.markdownlint.json` configuration
   - Review error messages in workflow logs
   - Fix reported issues or update configuration

2. **Link Checker Fails**:
   - Verify all internal links point to existing files
   - Check external links are accessible
   - Update or remove broken links

3. **Spell Check Fails**:
   - Add legitimate terms to `.cspell.json` words array
   - Fix actual typos in documentation

4. **PR Validation Fails**:
   - Ensure PR title follows format: `type: Description`
   - Add detailed PR description (>50 characters)
   - Complete PR checklist items

## Customization

### Adding New Workflows

1. Create a new `.yml` file in `.github/workflows/`
2. Define triggers, jobs, and steps
3. Test locally if possible
4. Commit and push to see it in action

### Modifying Existing Workflows

1. Edit the workflow file
2. Validate YAML syntax
3. Test changes on a branch first
4. Merge to main once validated

### Adding Custom Checks

To add custom validation:

1. Add a new job to appropriate workflow
2. Use existing actions or write bash scripts
3. Set appropriate failure conditions
4. Document in this README

## Best Practices

1. **Keep workflows focused**: Each workflow should have a clear purpose
2. **Use caching**: Cache dependencies to speed up workflows
3. **Fail fast**: Critical checks should fail the build early
4. **Provide feedback**: Use comments and labels to communicate with contributors
5. **Monitor performance**: Review workflow run times regularly
6. **Update dependencies**: Keep actions up to date

## Resources

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Workflow Syntax](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions)
- [GitHub Actions Marketplace](https://github.com/marketplace?type=actions)
