# Terraform Workflow

This repository contains a reusable GitHub Actions workflow for Terraform projects.

## Usage

To use this workflow in your repository, create a `.github/workflows/terraform.yml` file with the following contents, assuming the Terraform code is in the `infra` directory:

```yaml
name: Terraform

on:
  push:
    branches:
      - main

env:
  working-directory: infra

jobs:
  terraform:
    name: Terraform
    uses: USA-RedDragon/terraform-workflow/.github/workflows/terraform.yaml@v1.0.0
    permissions:
      id-token: write      # Required for google-github-actions/auth
      contents: read       # Required for actions/checkout
      pull-requests: write # Required for commenting on PR
    with:
      apply: true
      working-directory: ${{ env.working-directory }}
      github-app-auth: true
      github-app-repositories: terraform-modules
      gcp-auth: true
    secrets:
      app-id: ${{ secrets.GITHUB_APP_ID }}
      app-private-key: ${{ secrets.GITHUB_APP_PRIVATE_KEY }}
      gcp-workload-identity-provider: ${{ secrets.GCP_WORKLOAD_IDENTITY_PROVIDER }}
      gcp-service-account: ${{ secrets.GCP_SERVICE_ACCOUNT }}
```

### GitHub App auth scoping

When `github-app-auth` is enabled, the generated token's owner defaults to the owner of the calling repository. Use `github-app-owner` to override it, and `github-app-repositories` (comma or newline-separated) to grant access to specific repositories — for example a shared Terraform modules repo. If both are unset, the token is scoped to the calling repository only.
