# release

Shared reusable GitHub Actions workflows for inference-labs-inc repositories.

## Workflows

| Workflow | Purpose | Consumers |
|---|---|---|
| `publish-release.yml` | Build maturin wheels and publish to PyPI and crates.io | dsperse, jstprove, lightning |

## Usage

```yaml
jobs:
  release:
    uses: inference-labs-inc/release/.github/workflows/publish-release.yml@<sha>
    with:
      rust-toolchain: nightly-2025-03-27
      version-files: |
        pyproject.toml
        crates/my-crate/Cargo.toml
      test-import: "from my_package import something"
    secrets: inherit
```

## Security

This repo's workflows run with the calling repo's permissions and secrets. All actions are SHA-pinned, inputs are routed through environment variables to prevent script injection, and publish jobs require an `environment: release` gate with OIDC authentication (no stored secrets). Pin callers by commit SHA, not branch or tag.
