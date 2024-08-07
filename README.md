# Golang CI Workflow

This repository uses GitHub Actions to automate the Continuous Integration (CI) process for Go projects. The workflow is defined in the `.github/workflows/golang-ci.yaml` file.

## Workflow Overview

The workflow consists of two main jobs:
1. **Linting**: This job runs the `golangci-lint` tool to ensure code quality and adherence to coding standards.
2. **Testing**: This job runs the test suite to ensure that the code behaves as expected.

## Workflow Triggers

The workflow is triggered by `push` and `pull_request` events on the `main` branch, allowing it to automatically run on changes and pull requests targeting the main branch.

## Inputs

- **go-version**: (Required) The version of Go to use (e.g., '1.16', 'stable').
- **use-private-module**: (Optional) A flag to indicate whether to use a private module. Defaults to `false`.
- **github-owner**: (Optional) The GitHub owner of the private module.

## Secrets

- **GH_TOKEN**: (Optional) The GitHub token to use for importing private modules.

## Permissions

The workflow requires the following permissions:
- `contents: read`
- `packages: read`
- `statuses: write`

## Jobs

### Lint Job

The lint job uses the `golangci-lint` tool to check the code for issues. It is defined in a separate workflow file located at `.github/workflows/golangci-lint.yaml`.

#### Inputs

- `go-version`: The version of Go to use.
- `use-private-module`: Flag to use private module.
- `github-owner`: The GitHub owner of the private module.

#### Secrets

- `GH_TOKEN`: The GitHub token to use for importing private modules.

### Test Job

The test job runs the test suite to ensure that the code behaves as expected. It is defined in a separate workflow file located at `.github/workflows/golangci-test.yaml`.

#### Inputs

- `go-version`: The version of Go to use.
- `use-private-module`: Flag to use private module.
- `github-owner`: The GitHub owner of the private module.

#### Secrets

- `GH_TOKEN`: The GitHub token to use for importing private modules.

## Usage

To use this workflow in your repository, you can call it from another workflow file. Below is an example of how to call this workflow:

```yaml
name: Golang CI

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

permissions:
  contents: read
  packages: read
  statuses: write

jobs:
  ci:
    uses: infraspecdev/golang-generic-workflows/.github/workflows/golang-ci.yaml@main
    with:
      go-version: 'stable'
      use-private-module: true
      github-owner: 'your-github-username'
    secrets:
      GH_TOKEN: ${{ secrets.YOUR_GITHUB_TOKEN }}
