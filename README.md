# release

Shared reusable GitHub Actions workflows for inference-labs-inc repositories.

## Workflows

| Workflow | Purpose | Consumers |
|---|---|---|
| `publish-pypi.yml` | Build maturin wheels and publish to PyPI | dsperse, jstprove, lightning |

## Usage

```yaml
jobs:
  release:
    uses: inference-labs-inc/release/.github/workflows/publish-pypi.yml@<sha>
    with:
      rust-toolchain: nightly-2025-03-27
      version-files: |
        pyproject.toml
        crates/my-crate/Cargo.toml
      test-import: "from my_package import something"
    secrets: inherit
```

Pin callers by commit SHA, not branch or tag.

## Security

This repo's workflows run with the calling repo's permissions and secrets. Branch protection is mandatory — see repo settings.
