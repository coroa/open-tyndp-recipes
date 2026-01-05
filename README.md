# OpenTYNDP Custom Conda Recipes

Custom conda package recipes for the OpenTYNDP project, built and published to prefix.dev.

## Repository Structure

```
.
├── .github/
│   └── workflows/
|       ├── build-and-upload.yml # CI/CD workflow for building and uploading
│       └── pr-build.yml         # CI/CD workflow for PR test builds
├── recipes/
│   └── snakemake/               # Custom snakemake build
│       └── meta.yaml
├── pixi.toml                    # Development environment configuration
└── README.md
```

## Using Packages

Install packages from our custom channel:

```bash
# With pixi
pixi workspace channel add https://prefix.dev/open-tyndp 
pixi add "https://prefix.dev/open-tyndp::snakemake-minimal >=9"

# With conda/mamba
conda install -c https://prefix.dev/open-tyndp snakemake-minimal
```

## Adding New Recipes

1. Create a new directory under `recipes/` with your package name
2. Add a `meta.yaml` file following conda-build conventions
3. Create a PR - the workflow will automatically build and test
4. Once merged to `main`, packages are automatically uploaded to prefix.dev

### From conda-forge or bioconda-recipes packages

1. Copy the directory containing the meta.yaml file for the targeted package from its
   conda-forge feedstock or the bioconda-recipes repository.
2. Add a [local version identifier](https://packaging.python.org/en/latest/specifications/version-specifiers/#local-version-identifiers)
   to the version, like `9.15.3+opentyndp` or `9.15.3+opentyndp.1`.
3. Update the source to something like:
   
   ```yaml
   source:
     git_url: https://github.com/coroa/snakemake.git
     git_rev: v{{ version }}
   ```
4. Add setuptools-scm to the host requirements section
5. Create a PR to build and test


## Local Development

Build recipes locally using pixi:

```bash
# Build a specific recipe
pixi run build recipes/snakemake

# Upload to channel (requires PREFIX_API_KEY)
pixi run upload
```

## CI/CD Workflow

The repository uses GitHub Actions to:

- **On Pull Requests**: Build changed recipes and upload as artifacts for testing
- **On Merge to Main**: Build and upload packages to prefix.dev channel

## Recipe Guidelines

- Use `noarch: python` or `noarch: generic` when possible for platform-independent packages
- Always include tests in `meta.yaml`
- Pin dependencies appropriately
- For git-based sources, add `setuptools-scm` to build dependencies if the package uses it

## Example: Snakemake Custom Build

The `recipes/snakemake` directory contains a custom build of Snakemake from a specific git branch:

```yaml
source:
  git_url: https://github.com/coroa/snakemake
  git_rev: fix/unstuck-windows
```

This allows us to test unreleased fixes before they're merged upstream.

## License

Individual recipes may have different licenses. Check each recipe's `meta.yaml` for license information.
