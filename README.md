# Standardised Workflows

This repository contains a set of centrally configured, consistent and reusable CI pipeline components.

----
## Check npm + ncc based GitHub Actions have a valid dist folder

This workflow ensures that code changes in /src are reflected in the /dist folder produced by ncc.

### actions-check-dist.yml

```yaml
name: 'Dist Diff'
on:
  pull_request:
    types: [ opened, reopened, synchronize ]

jobs:
  diff:
    uses: UKHomeOffice/sas-github-workflows/.github/workflows/actions-check-dist.yml@v1
```

----
## Scan a docker image using Anchore on a branch - npm

This workflow builds and scans a docker image using Anchore. 
Optionally it pushes the image to a repository, tagging it with the SHA.

* Needs a secret value of DOCKER_USER_NAME or QUAY_ROBOT_USER_NAME
* Needs a secret value of DOCKER_PASSWORD or QUAY_ROBOT_TOKEN
* Will only push with a label of `smoketest`

### docker-npm-branch.yml

```yaml
name: 'Docker Build Branch'
on:
  pull_request:
    types: [ labeled, opened, reopened, synchronize ]

jobs:
  build:
    uses: UKHomeOffice/sas-github-workflows/.github/workflows/docker-npm-branch.yml@v1
    with:
      installCommand: 'ci --production=false --no-optional'
      buildCommand: 'build-prod'
      image: 'quay.io/ukhomeofficedigital/hocs-frontend'
      repository: 'quay.io'
    secrets: inherit
```

----
## Publish a docker image - npm

This workflow builds and publishes a docker image.
Optionally it pushes the image to a repository, tagging it with the SHA.

* Needs a secret value of DOCKER_USER_NAME or QUAY_ROBOT_USER_NAME
* Needs a secret value of DOCKER_PASSWORD or QUAY_ROBOT_TOKEN

### docker-npm-main.yml

```yaml
name: 'Docker Build Main'
on:
  pull_request:
    types: [ closed ]

jobs:
  build:
    uses: UKHomeOffice/sas-github-workflows/.github/workflows/docker-npm-main.yml@v1
    with:
      installCommand: 'ci --production=false --no-optional'
      buildCommand: 'build-prod'
      image: 'quay.io/ukhomeofficedigital/hocs-frontend'
    secrets: inherit

```

----
## Check a PR has a valid SemVer increment

This workflow ensures one `minor`,`major`,`patch`, or `skip-release` label is present on a PR.

### semver-check.yml
```yaml
name: 'SemVer label Checker'
on:
  pull_request:
    types: [ labeled, unlabeled, opened, reopened, synchronize ]

jobs:
  check:
    uses: UKHomeOffice/sas-github-workflows/.github/workflows/semver-check.yml@v1
```

----
## Create a SemVer tag on PR merge

This workflow tags the commit SHA with a SemVer value on PR merge.
* This will not trigger if a label is added to the PR with the value of `skip-release`.
* This will increment the last SemVer tag by either `minor`,`major`, or `patch`.
* This will also walk a `major` version tag along with the SemVer value.
  e.g. `v1` with tag `1.2.3`.

### semver-tag.yml
```yaml
name: 'SemVer Tag'
on:
  pull_request:
    types: [ closed ]

jobs:
  check:
    uses: UKHomeOffice/sas-github-workflows/.github/workflows/semver-tag.yml@v1
```

----
## Lint, audit and test an npm based project

This will run `npm run lint` and `npm test` on a repository after building it with `npm ci`.
* It will run tests in parallel against 2 versions of node; `18`, `19`.
* Optionally this workflow will install dependencies required to run tests.
* Optionally this workflow will start components using docker-compose to run end-to-end tests against.

  ### inputs:

| input | required | default | effective command |
|---|---|---|---|
| nodeVersionMatrix | false | [ "18.x", "19.x" ] | |
| dependencyCommand | false | 'ci' | npm --loglevel warn ci |
| buildCommand | false | 'build' | npm run build |
| lintCommand | false | 'lint' | npm run lint |
| osDependencies | false | null | sudo apt-get install -y [packages] |
| dockerComposeCommand | false | './ci/docker-compose.yml' | docker-compose -f ./ci/docker-compose.yml up -d [components] |
| dockerComposeComponents | false | null | |
| healthcheckScript | false | './ci/healthcheck.sh' | bash ./ci/healthcheck.sh |

### test-npm.yml - unit tests only
```yaml
name: 'Test'
on:
  pull_request:
    types: [ opened, reopened, synchronize ]

jobs:
  test:
    uses: UKHomeOffice/sas-github-workflows/.github/workflows/test-npm.yml@v1
```````

### test-npm.yml - install extra OS deps and docker compose with custom npm arguments
```yaml
name: 'Test'
on:
  pull_request:
    types: [ opened, reopened, synchronize ]

jobs:
  test:
    uses: UKHomeOffice/sas-github-workflows/.github/workflows/test-npm.yml@v1
    with:
      dependencyCommand: 'ci --production=false --no-optional'
      buildCommand: 'build-prod'
      osDependencies: 'libreoffice'
      dockerComposeComponents: 'postgres'
```````
