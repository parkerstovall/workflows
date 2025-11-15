# Reusable GitHub Actions Workflows

A collection of reusable GitHub Actions workflows for automating common tasks across multiple repositories.

## 📦 Available Workflows

### 1. Publish NPM Package (`Publish_NPM.yaml`)

Publishes an NPM package and creates a GitHub release. Automatically checks if the version already exists to prevent duplicate publishes.

**Features:**
- ✅ Publishes package to NPM registry
- ✅ Creates GitHub release with version tag
- ✅ Skips if version already exists
- ✅ Supports monorepos with custom package directories

**Usage:**

```yaml
name: Publish Package

on:
  push:
    branches: [main]

jobs:
  publish:
    uses: parkerstovall/workflows/.github/workflows/Publish_NPM.yaml@main
    secrets:
      npm_token: ${{ secrets.NPM_TOKEN }}
    with:
      package_directory: '.' # Optional, defaults to root
```

**Inputs:**
| Name | Required | Default | Description |
|------|----------|---------|-------------|
| `npm_token` | ✅ | - | NPM authentication token |
| `package_directory` | ❌ | `.` | Directory containing package.json |

**Permissions Required:**
- `contents: write` (automatically granted)
- NPM token in repository secrets

---

### 2. Push Package Update (`Push_PackageUpdate.yaml`)

Updates a package dependency in a target repository, bumps the version, and either pushes directly or creates a pull request.

**Features:**
- ✅ Checks out target repository
- ✅ Updates specified package to latest version
- ✅ Automatically bumps patch version
- ✅ Creates PR or pushes directly
- ✅ Prevents failures if no changes detected

**Usage:**

```yaml
name: Update Consumer Repo

on:
  push:
    branches: [main]
  workflow_dispatch:

jobs:
  update-consumer:
    uses: parkerstovall/workflows/.github/workflows/Push_PackageUpdate.yaml@main
    with:
      repo_token: ${{ secrets.PAT_TOKEN }}
      repo: 'owner/consumer-repo'
      package_name: 'my-package'
      package_directory: '.'
      create_pr: true
```

**Inputs:**
| Name | Required | Default | Description |
|------|----------|---------|-------------|
| `repo_token` | ✅ | - | GitHub token with write permissions |
| `repo` | ✅ | - | Target repository (format: `owner/repo`) |
| `package_name` | ✅ | - | Name of the package to update |
| `package_directory` | ❌ | `.` | Directory containing package.json |
| `create_pr` | ❌ | `false` | Create PR instead of direct push |

**Permissions Required:**
- Personal Access Token (PAT) with `repo` scope for target repository

---

### 3. PR Validation (`PR.yaml`)

Internal workflow that validates all GitHub Actions YAML files using actionlint.

**Features:**
- ✅ Runs on every PR touching workflow files
- ✅ Validates syntax and semantics
- ✅ Shows inline annotations on errors
- ✅ Checks for common mistakes

This workflow automatically runs in this repository and doesn't need to be called externally.

---

## 🚀 Getting Started

### Prerequisites

1. **For NPM Publishing:**
   - Create an NPM token at [npmjs.com](https://www.npmjs.com/)
   - Add it as `NPM_TOKEN` in your repository secrets

2. **For Package Updates:**
   - Create a Personal Access Token (PAT) with `repo` scope
   - Add it as a secret in the repository that will trigger the update

### Example: Complete Publishing Pipeline

```yaml
# .github/workflows/publish.yaml
name: Publish and Update Consumers

on:
  push:
    branches: [main]
    paths:
      - 'package.json'
      - 'src/**'

jobs:
  publish:
    uses: parkerstovall/workflows/.github/workflows/Publish_NPM.yaml@main
    secrets:
      npm_token: ${{ secrets.NPM_TOKEN }}

  update-app:
    needs: publish
    uses: parkerstovall/workflows/.github/workflows/Push_PackageUpdate.yaml@main
    with:
      repo_token: ${{ secrets.PAT_TOKEN }}
      repo: 'myorg/my-app'
      package_name: '@myorg/my-package'
      create_pr: true

  update-website:
    needs: publish
    uses: parkerstovall/workflows/.github/workflows/Push_PackageUpdate.yaml@main
    with:
      repo_token: ${{ secrets.PAT_TOKEN }}
      repo: 'myorg/my-website'
      package_name: '@myorg/my-package'
      create_pr: false
```

---

## 🔒 Security Best Practices

1. **Use Secrets:** Never hardcode tokens in workflow files
2. **Limit Token Scope:** Use fine-grained PATs with minimum required permissions
3. **Review PRs:** Always review automatically created PRs before merging
4. **Pin Versions:** Consider pinning workflow versions with commit SHA instead of `@main`

```yaml
# Recommended: Pin to specific version
uses: parkerstovall/workflows/.github/workflows/Publish_NPM.yaml@v1.0.0

# Or use commit SHA for maximum security
uses: parkerstovall/workflows/.github/workflows/Publish_NPM.yaml@a1b2c3d
```

---

## 🛠️ Development

This repository uses actionlint to validate all workflow files. PRs are automatically checked for syntax errors and common mistakes.

### Local Testing

Install actionlint:
```bash
brew install actionlint
```

Validate workflows:
```bash
actionlint .github/workflows/*.yaml
```

---

## 📝 License

MIT

---

## 🤝 Contributing

Contributions are welcome! Please open an issue or submit a pull request.

1. Fork the repository
2. Create your feature branch
3. Commit your changes
4. Push to the branch
5. Open a Pull Request

---

## 📧 Support

For issues or questions, please open an issue in this repository.
