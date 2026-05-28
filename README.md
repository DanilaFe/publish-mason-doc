# publish-mason-doc

A GitHub Action that generates documentation for a [Chapel](https://chapel-lang.org/) [mason](https://chapel-lang.org/docs/tools/mason/mason.html) package and publishes it to GitHub Pages — all in one step.

It installs Chapel via the official pre-built packages, runs `mason doc` to generate HTML documentation, and deploys the result to GitHub Pages.

## Usage

```yaml
name: Publish Documentation

on:
  push:
    branches: [main]

jobs:
  publish-docs:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pages: write
      id-token: write
    environment:
      name: github-pages
      url: ${{ steps.publish.outputs.page_url }}
    steps:
      - uses: actions/checkout@v4

      - name: Publish docs
        id: publish
        uses: YOUR_USERNAME/publish-mason-doc@v1
```

> **Note:** GitHub Pages must be enabled for your repository and the source must be set to **GitHub Actions** in *Settings → Pages*.

## Inputs

| Input | Required | Default | Description |
|---|---|---|---|
| `chapel-version` | No | `latest` | Chapel version to install (e.g. `2.8.0`). Resolved from the latest GitHub Release when set to `latest`. |
| `source-dir` | No | `.` | Relative path to the mason project root within the repository. |
| `docs-dir` | No | `doc` | Subdirectory (relative to `source-dir`) where `mason doc` writes its output. |
| `token` | No | `github.token` | GitHub token used for Pages deployment. |

## Outputs

This action does not define its own outputs. The `actions/deploy-pages` step used internally does not surface `page_url` through composite action outputs. If you need the deployed URL, you can retrieve it from the Pages API or your repository settings.

## Requirements

- **Runner**: `ubuntu-latest` (Ubuntu 22.04 or 24.04, `amd64` or `arm64`)
- **Permissions**: The calling job must have:
  ```yaml
  permissions:
    contents: read
    pages: write
    id-token: write
  ```
- **Environment**: It is strongly recommended to set `environment: { name: github-pages }` on the calling job so GitHub tracks the deployment correctly.
- **GitHub Pages**: Must be enabled with source set to **GitHub Actions** in your repository settings.

## Example: Mason project in a subdirectory

```yaml
- uses: YOUR_USERNAME/publish-mason-doc@v1
  with:
    source-dir: my-chapel-package
    chapel-version: '2.8.0'
```

## How it works

1. Resolves the Chapel version (fetches the latest release tag if `chapel-version: latest`)
2. Downloads and installs the official Chapel `.deb` package for the runner's Ubuntu version
3. Runs `mason doc` inside the mason project directory
4. Uploads the generated `doc/` folder as a GitHub Pages artifact
5. Deploys to GitHub Pages
