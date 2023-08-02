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
    uses: UKHomeOffice/sas-github-workflows/.github/workflows/actions-check-dist.yml@v2
```

----

## Scan a docker image using Anchore

This workflow builds and scans a docker image using Anchore, optionally, pushing to a repository with the SHA.

When the `smoketest` of label is applied, the image will be pushed to either Docker (default) or ECR.

**ECR**
* Requires secret value of `AWS_ACCESS_KEY_ID`
* Requires secret value of `AWS_SECRET_ACCESS_KEY`

**Docker**
* Requires secret value of `DOCKER_USER_NAME` or `QUAY_ROBOT_USER_NAME`
* Requires secret value of `DOCKER_PASSWORD` or `QUAY_ROBOT_TOKEN`

To push to `ECR`, an addition input is required within the with: `ecr: 'true'`.

### anchore.yml

```yaml
name: "Anchore Scan"

on:
  push:
    branches: [ "main" ]
  pull_request:
    types: [ labeled, opened, reopened, synchronize ]
  schedule:
    - cron: '45 12 * * 1'

jobs:
  scan:
    uses: UKHomeOffice/sas-github-workflows/.github/workflows/anchore.yml@v2
    with:
      image: 'quay.io/ukhomeofficedigital/hocs-outbound-proxy'
    secrets: inherit
```

----

## Scan a docker image using Anchore - gradle

This workflow builds and scans a docker image using Anchore, optionally, pushing to a repository with the SHA.

When the `smoketest` of label is applied, the image will be pushed to either Docker (default) or ECR.

**ECR**
* Requires secret value of `AWS_ACCESS_KEY_ID`
* Requires secret value of `AWS_SECRET_ACCESS_KEY`

**Docker**
* Requires secret value of `DOCKER_USER_NAME` or `QUAY_ROBOT_USER_NAME`
* Requires secret value of `DOCKER_PASSWORD` or `QUAY_ROBOT_TOKEN`

To push to `ECR`, an addition input is required within the with: `ecr: 'true'`.

### anchore-gradle.yml

```yaml
name: "Anchore Scan"

on:
  push:
    branches: [ "main" ]
  pull_request:
    types: [ labeled, opened, reopened, synchronize ]
  schedule:
    - cron: '45 12 * * 1'

jobs:
  scan:
    uses: UKHomeOffice/sas-github-workflows/.github/workflows/anchore-gradle.yml@v2
    with:
      image: 'quay.io/ukhomeofficedigital/hocs-frontend'
    secrets: inherit
```

----

## Scan a docker image using Anchore - npm

This workflow builds and scans a docker image using Anchore, optionally, pushing to a repository with the SHA.

When the `smoketest` of label is applied, the image will be pushed to either Docker (default) or ECR.

**ECR**
* Requires secret value of `AWS_ACCESS_KEY_ID`
* Requires secret value of `AWS_SECRET_ACCESS_KEY`

**Docker**
* Requires secret value of `DOCKER_USER_NAME` or `QUAY_ROBOT_USER_NAME`
* Requires secret value of `DOCKER_PASSWORD` or `QUAY_ROBOT_TOKEN`

To push to `ECR`, an addition input is required within the with: `ecr: 'true'`.

### anchore-npm.yml

```yaml
name: "Anchore Scan"

on:
  push:
    branches: [ "main" ]
  pull_request:
    types: [ labeled, opened, reopened, synchronize ]
  schedule:
    - cron: '45 12 * * 1'

jobs:
  scan:
    uses: UKHomeOffice/sas-github-workflows/.github/workflows/anchore-npm.yml@v2
    with:
      installCommand: 'ci --production=false --no-optional'
      buildCommand: 'build-prod'
      image: 'quay.io/ukhomeofficedigital/hocs-frontend'
    secrets: inherit
```

----

## Scan a repository using Codeql - gradle

This is a [CodeQL](https://codeql.github.com/) static analysis action for jvm languages.
This build can use the caching gradle actions over generic job that uses the `autobuild` step.
Typically, this is run on changes to source code only, ignoring test code.

### codeql-analysis-gradle.yml

```yaml
name: 'CodeQL'
on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
  schedule:
    - cron: '45 12 * * 1'

jobs:
  analyze:
    uses: UKHomeOffice/sas-github-workflows/.github/workflows/codeql-analysis-gradle.yml@v2
```

----

## Scan a repository using Codeql - npm

This is a [CodeQL](https://codeql.github.com/) static analysis action for javascript.
Because this is an interpreted language we don't need the `autobuild` step.
Typically, this is run on on changes to source code only, ignoring test code.

### codeql-analysis-npm.yml

```yaml
name: 'CodeQL'
on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
  schedule:
    - cron: '45 12 * * 1'

jobs:
  analyze:
    uses: UKHomeOffice/sas-github-workflows/.github/workflows/codeql-analysis-npm.yml@v2
```

----

## Scan a repository using Codeql - pip

This is a [CodeQL](https://codeql.github.com/) static analysis action for python.
Because this is an interpreted language we don't need the `autobuild` step.
Typically, this is run on on changes to source code only, ignoring test code.

### codeql-analysis-pip.yml

```yaml
name: 'CodeQL'
on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
  schedule:
    - cron: '45 12 * * 1'

jobs:
  analyze:
    uses: UKHomeOffice/sas-github-workflows/.github/workflows/codeql-analysis-pip.yml@v2
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
    uses: UKHomeOffice/sas-github-workflows/.github/workflows/semver-check.yml@v2
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
    uses: UKHomeOffice/sas-github-workflows/.github/workflows/semver-tag.yml@v2
```

----

## Publish a docker image

This workflow builds and publishes a docker image to either Docker (default) or ECR with a SemVer value.

**ECR**
* Requires secret value of `AWS_ACCESS_KEY_ID`
* Requires secret value of `AWS_SECRET_ACCESS_KEY`

**Docker**
* Requires secret value of `DOCKER_USER_NAME` or `QUAY_ROBOT_USER_NAME`
* Requires secret value of `DOCKER_PASSWORD` or `QUAY_ROBOT_TOKEN`

To push to `ECR`, an addition input is required within the with: `ecr: 'true'`.

### semver-tag-docker.yml

```yaml
name: 'SemVer Tag and Docker Build'
on:
  pull_request:
    types: [ closed ]

jobs:
  build:
    uses: UKHomeOffice/sas-github-workflows/.github/workflows/semver-tag-docker.yml@v2
    with:
      image: 'quay.io/ukhomeofficedigital/hocs-toolbox'
    secrets: inherit

```

----

## Publish a docker image - gradle

This workflow builds and publishes a docker image to either Docker (default) or ECR with a SemVer value.

**ECR**
* Requires secret value of `AWS_ACCESS_KEY_ID`
* Requires secret value of `AWS_SECRET_ACCESS_KEY`

**Docker**
* Requires secret value of `DOCKER_USER_NAME` or `QUAY_ROBOT_USER_NAME`
* Requires secret value of `DOCKER_PASSWORD` or `QUAY_ROBOT_TOKEN`

To push to `ECR`, an addition input is required within the with: `ecr: 'true'`.

### semver-tag-docker-gradle.yml

```yaml
name: 'SemVer Tag and Docker Build'
on:
  pull_request:
    types: [ closed ]

jobs:
  build:
    uses: UKHomeOffice/sas-github-workflows/.github/workflows/semver-tag-docker-gradle.yml@v2
    with:
      image: 'quay.io/ukhomeofficedigital/hocs-audit'
    secrets: inherit

```

----

## Publish a docker image - npm

This workflow builds and publishes a docker image to either Docker (default) or ECR with a SemVer value.

**ECR**
* Requires secret value of `AWS_ACCESS_KEY_ID`
* Requires secret value of `AWS_SECRET_ACCESS_KEY`

**Docker**
* Requires secret value of `DOCKER_USER_NAME` or `QUAY_ROBOT_USER_NAME`
* Requires secret value of `DOCKER_PASSWORD` or `QUAY_ROBOT_TOKEN`

To push to `ECR`, an addition input is required within the with: `ecr: 'true'`.

### semver-tag-docker-npm.yml

```yaml
name: 'SemVer Tag and Docker Build'
on:
  pull_request:
    types: [ closed ]

jobs:
  build:
    uses: UKHomeOffice/sas-github-workflows/.github/workflows/semver-tag-docker-npm.yml@v2
    with:
      installCommand: 'ci --production=false --no-optional'
      buildCommand: 'build-prod'
      image: 'quay.io/ukhomeofficedigital/hocs-frontend'
    secrets: inherit

```

----


## Publish an npm package

This workflow builds and publishes an npm package with a SemVer value.


### semver-tag-npm.yml

```yaml
name: 'SemVer Tag and npm Publish'
on:
  pull_request:
    types: [ closed ]

jobs:
  build:
    uses: UKHomeOffice/sas-github-workflows/.github/workflows/semver-tag-npm.yml@v2
    with:
      installCommand: 'ci --ignore-scripts'
    secrets: inherit

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
| installCommand | false | 'ci' | npm --loglevel warn ci |
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
    uses: UKHomeOffice/sas-github-workflows/.github/workflows/test-npm.yml@v2
```````

### test-npm.yml - install extra OS deps and docker compose with custom npm arguments

```yaml
name: 'Test'
on:
  pull_request:
    types: [ opened, reopened, synchronize ]

jobs:
  test:
    uses: UKHomeOffice/sas-github-workflows/.github/workflows/test-npm.yml@v2
    with:
      dependencyCommand: 'ci --production=false --no-optional'
      buildCommand: 'build-prod'
      osDependencies: 'libreoffice'
      dockerComposeComponents: 'postgres'
```````

### test-python.yml 

Supports optional `pythonVersionMatrix` input that allows the specificity of Python version. A default is defined in the workflow.

```yaml
name: 'Test'
on:
  pull_request:
    types: [ opened, reopened, synchronize ]

jobs:
  test:
    uses: UKHomeOffice/sas-github-workflows/.github/workflows/test-python.yml@v2
```````

----
