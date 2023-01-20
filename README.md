# Standardised Workflows

This repository contains a set of centrally configured, consistent and reusable CI pipeline components.

## semver-check.yml
This workflow ensures one `minor`,`major`,`patch`, or `skip-release` label is present on a PR.

### Example usage
```yaml
name: 'SemVer label Checker'
on:
  pull_request:
    types: [ labeled, unlabeled, opened, reopened, synchronize ]

jobs:
  check:
    uses: UKHomeOffice/sas-github-actions/.github/workflows/semver-check.yml@v1
```
