# publish-mason-doc

A GitHub Action that generates documentation for a [Chapel](https://chapel-lang.org/) [mason](https://chapel-lang.org/docs/tools/mason/mason.html) package and publishes it to GitHub Pages.

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
        uses: DanilaFe/publish-mason-doc@main
```

> **Note:** GitHub Pages must be enabled for your repository and the source must be set to **GitHub Actions** in *Settings → Pages*.

## Inputs

| Input | Required | Default | Description |
|---|---|---|---|
| `chapel-version` | No | `latest` | Chapel version to install (e.g. `2.8.0`). Resolved from the latest GitHub Release when set to `latest`. |
| `source-dir` | No | `.` | Relative path to the mason project root within the repository. |
| `docs-dir` | No | `doc` | Subdirectory (relative to `source-dir`) where `mason doc` writes its output. |
| `token` | No | `github.token` | GitHub token used for Pages deployment. |

## Requirements

- **Runner**: Ubuntu with `apt`, `sudo`, `curl`, and `jq` available (e.g. `ubuntu-latest`). The action checks the GitHub Releases API to confirm a `.deb` package exists for the runner's Ubuntu version and architecture. This may be relevant if there is no Chapel release for the current version of Ubuntu used by the runner.
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
- uses: DanilaFe/publish-mason-doc@main
  with:
    source-dir: my-chapel-package
    chapel-version: '2.8.0'
```
