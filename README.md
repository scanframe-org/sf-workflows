# Workflows

Central CI/CD pipeline configurations and self-hosted runner infrastructure for C++ projects.  
This repository provides reusable CI pipelines for both GitHub Actions and GitLab CI, along with
Docker Compose setups for self-hosted runners.

## Contents

### [GitHub Actions CI Pipeline](.github/README.md)

A reusable GitHub Actions workflow that can be called from any project. It provides build, coverage,
and deploy jobs with a matrix of toolchains (GNU, GW, GA, MSVC). Projects only need a small caller
workflow to integrate the full pipeline, including automatic and manual deployment to Nexus.

#### Template for GitHub Repositories

The [`templates/workflows/ci.yml`](templates/workflows/ci.yml) file is a ready-to-use caller workflow
that you can copy into your project's `.github/workflows/` directory. It calls the reusable workflow
from this repository and passes the necessary inputs and secrets. After copying, adjust the
`docker-image`, `matrix`, and `cmake-lib-branch` parameters as needed for your project.

### [Self-Hosted GitHub Runner](docker/github/README.md)

Docker Compose configuration for running a self-hosted GitHub Actions runner with Docker-in-Docker
support. Includes setup instructions, runner scope configuration (repo or organization), container
job support, and troubleshooting guides.

### [GitLab CI Pipeline](gitlab-ci/README.md)

GitLab CI pipeline configuration with environments, access tokens, and CI/CD variables for Nexus
package deployment. Documents the required variables for staging and stable deployment stages,
coverage reporting, and optional testing signals.

### [Self-Hosted GitLab Runner](docker/gitlab/README.md)

Configuration files for a self-hosted GitLab CI runner with Docker executor and S3/MinIO shared
cache. Includes a template for `config.toml` with placeholders for GitLab URL, registration token,
cache credentials, and default container image.

## Helper Scripts

### `github-runner.sh`

Helper script for managing the self-hosted GitHub Actions runner via Docker Compose. Supports
starting, stopping, checking status, viewing logs, and executing commands inside the runner
container.

### `gitlab-runner.sh`

Helper script for managing the self-hosted GitLab CI runner Docker container. Supports registering
the runner with GitLab, running it interactively or in the background, stopping, and attaching to
the container.

### `check-yaml-format.sh`

Quick script to lint all YAML files in the project using `yamllint` with the project's
`.yamllint.yml` configuration. Useful for CI pre-checks or local validation before committing.
