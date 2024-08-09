# Golang CI Workflow

This repository uses GitHub Actions to automate the Continuous Integration (CI) process for Go projects. The workflow is defined in the `.github/workflows/golang-ci.yaml` file.

## Workflow Overview

The workflow consists of three main jobs:
1. **Linting**: This job runs the `golangci-lint` tool to ensure code quality and adherence to coding standards.
2. **Testing**: This job runs the test suite to ensure that the code behaves as expected.
3. **Building**: This job builds and pushes the Docker image to Amazon ECR.

## Workflow Triggers

The workflow is triggered by `push` and `pull_request` events on the `main` branch, allowing it to automatically run on changes and pull requests targeting the main branch.

## Inputs

- **go-version**: (Optional) The version of Go to use (e.g., '1.16', 'stable'). Defaults to `stable`.
- **use-private-module**: (Optional) A flag to indicate whether to use a private module. Defaults to `false`.
- **github-owner**: (Optional) The GitHub owner of the private module.
- **ecr-repository**: (Required) The name of the ECR repository to push the Docker image to.
- **aws-region**: (Optional) The AWS region where the ECR repository is located. Defaults to `ap-south-1`.
- **dockerfile**: (Optional) The path to the Dockerfile to use for building the Docker image. Defaults to `Dockerfile`.
- **platforms**: (Optional) The target platforms for the Docker image build (e.g., 'linux/amd64', 'linux/arm64'). Defaults to `linux/amd64`.
- **prefix**: (Optional) A prefix to use for the Docker image tags. Defaults to `sha`.
- **type**: (Optional) The type of build (e.g., 'release', 'snapshot'). Defaults to `sha`.

## Secrets

- **GH_TOKEN**: (Optional) The GitHub token to use for importing private modules.
- **ASSUME_ROLE**: (Required) The role to assume for AWS operations.

## Permissions

The workflow requires the following permissions:
- `contents: read`
- `packages: read`
- `statuses: write`
- `id-token: write`

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

### Build Job

The build job builds and pushes the Docker image to Amazon ECR. It is defined in a separate workflow file located at `.github/workflows/golangci-build.yaml`.

#### Inputs

- `ecr-repository`: The name of the ECR repository to push the Docker image to.
- `aws-region`: The AWS region where the ECR repository is located.
- `dockerfile`: The path to the Dockerfile to use for building the Docker image.
- `platforms`: The target platforms for the Docker image build.
- `prefix`: A prefix to use for the Docker image tags.
- `type`: The type of build.
- `use-private-module`: Flag to use private module.
- `github-owner`: The GitHub owner of the private module.

#### Secrets

- `ASSUME_ROLE`: The role to assume for AWS operations.
- `GH_TOKEN`: The GitHub token to use for importing private modules.

## Usage

To use this workflow in your repository, you can call it from another workflow file. Below is an example of how to call this workflow with both required and optional inputs:

```yaml
# Golang CI Workflow

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
  id-token: write

jobs:
  ci:
    uses: infraspecdev/golang-generic-workflows/.github/workflows/golang-ci.yaml@main
    with:
      # Go version to use for the workflow
      go-version: 'stable' # Optional

      # Flag to use a private module
      use-private-module: false # Optional

      # GitHub owner of the private module
      github-owner: '' # Optional

      # ECR repository name for Docker image
      ecr-repository: 'your-ecr-repository' # Required

      # AWS region for the ECR repository
      aws-region: 'ap-south-1' # Optional

      # Path to the Dockerfile
      dockerfile: 'Dockerfile' # Optional

      # Target platforms for Docker image build
      platforms: 'linux/amd64' # Optional

      # Prefix for Docker image tags
      prefix: 'sha' # Optional

      # Type of build (e.g., 'release', 'snapshot')
      type: 'sha' # Optional

    secrets:
      # GitHub token for importing private modules
      GH_TOKEN: ${{ secrets.GH_TOKEN }} # Optional

      # AWS Assume Role ARN for operations
      ASSUME_ROLE: ${{ secrets.ASSUME_ROLE }} # Required
