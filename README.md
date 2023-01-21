# Standardised Workflows

This repository contains a set of centrally configured, consistent and reusable CI pipeline components.

----

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

----

## semver-tag.yml
This workflow tags the commit SHA with a SemVer value on PR merge.
* This will not trigger if a label is added to the PR with the value of `skip-release`.
* This will increment the last SemVer tag by either `minor`,`major`, or `patch`.
* This will also walk a `major` version tag along with the SemVer value.
e.g. `v1` with tag `1.2.3`.

### Example usage
```yaml
name: 'SemVer Tag'
on:
  pull_request:
    types: [ closed ]

jobs:
  check:
    uses: UKHomeOffice/sas-github-actions/.github/workflows/semver-tag.yml@v1
```
