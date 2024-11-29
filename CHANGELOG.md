# Change log

## V2 to V3 breaking changes
### [start-integration-infrastructure](./.github/actions/start-integration-infrastructure/action.yml): 
* If you were using the `docker_compose_command` input to change the file used, you will need to expand this to the full command without `-d component,list`. e.g. an invocation of the action in v2 like this:
  ```yaml
  - name: Start integration infrastructure
    uses: UKHomeOffice/sas-github-workflows/.github/actions/start-integration-infrastructure@v2
    with:
      docker_compose_command: './docker-compose.yml'
  ```
  would become:
  ```yaml
  - name: Start integration infrastructure
    uses: UKHomeOffice/sas-github-workflows/.github/actions/start-integration-infrastructure@v3
    with:
      docker_compose_command: 'docker compose -f ./docker-compose.yml up'
  ```
* If you are using runners that don't have a version of docker with the `docker compose` subcommand you will need to provide `docker_compose_command` to keep the previous behaviour that uses the separate `docker-compose` binary.

### [test-gradle](./.github/workflows/test-gradle.yml), [test-npm](./.github/workflows/test-npm.yml): 
* The `dockerComposeCommand` inputs for these workflows is passed to `start-integration-infrastructure`, so the above changes will also apply here.
