# release

Shared reusable GitHub Actions workflows for inference-labs-inc repositories.

## Workflows

| Workflow | Purpose | Consumers |
|---|---|---|
| `publish-release.yml` | Build maturin wheels (Linux x86_64, Linux aarch64, macOS aarch64, sdist) and publish to PyPI and crates.io | dsperse, jstprove, lightning |

## Usage

```yaml
jobs:
  release:
    uses: inference-labs-inc/release/.github/workflows/publish-release.yml@<sha>
    with:
      version: ${{ github.ref_name }}
      rust-toolchain: nightly-2025-03-27
      version-files: |
        pyproject.toml
        crates/my-crate/Cargo.toml
      test-import: "from my_package import something"
    secrets: inherit
    permissions:
      contents: write
      id-token: write
      attestations: write
```

### Inputs

| Input | Required | Default | Description |
|---|---|---|---|
| `version` | yes | — | Version to inject (e.g. `v1.2.3`). Caller determines from trigger context. |
| `rust-toolchain` | yes | — | Rust toolchain (e.g. `nightly-2025-03-27`, `1.88.0`) |
| `version-files` | yes | — | Newline-separated Cargo.toml/pyproject.toml files to version-stamp |
| `test-import` | yes | — | Python import statement to verify wheel (e.g. `from pkg import X`) |
| `maturin-working-directory` | no | `.` | Directory containing pyproject.toml for maturin |
| `system-deps-linux` | no | `""` | Space-separated dnf packages for manylinux container |
| `system-deps-macos` | no | `""` | Space-separated brew packages for macOS |
| `publish` | no | `true` | Set `false` for build-only test runs (skips PyPI, crates.io, GitHub Release) |
| `publish-crates-io` | no | `false` | Publish a Rust crate to crates.io |
| `crate-name` | no | `""` | Crate name (required if `publish-crates-io` is true) |
| `crate-features` | no | `""` | Features to enable (e.g. `--all-features`) |
| `crate-version-files` | no | `""` | Cargo.toml files to version-stamp for crates.io (defaults to `version-files`) |
| `slsa-attestation` | no | `false` | Generate SLSA build provenance attestation for the crate |

### Build matrix

Every invocation builds all four artifact types: Linux x86_64 wheel (manylinux 2_28), Linux aarch64 wheel (manylinux 2_28 via zig), macOS aarch64 wheel, and source distribution. Linux x86_64 and macOS wheels are smoke-tested after build.

## Security

This repo's workflows run with the calling repo's permissions and secrets. All actions are SHA-pinned, inputs are routed through environment variables to prevent script injection, and publish jobs require an `environment: release` gate with OIDC authentication (no stored secrets). PyPI publishing uses Sigstore attestations via `pypa/gh-action-pypi-publish`. Pin callers by commit SHA, not branch or tag.
