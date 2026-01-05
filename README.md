# Organization GitHub Actions

Reusable GitHub Actions workflows for GDS Digital Identity AWS infrastructure repositories.

## Using Actions
Composite actions are defined within individual folders, e.g. `checkov/action.yml`.

Workflows can then be created in calling repositories with any combination of these actions.

### Getting Started
See [`example-workflow.yml`](./example-workflow.yml) for a minimal example workflow file that can be used within other repositories.

## Available Actions
Each of the following examples uses only a single action within each job, but this is just for simplicity. 
It can often be useful to combine multiple actions within a single job.

### Checkov Security Scan

Runs Checkov security scanning with SARIF output for GitHub Security tab integration.

**Usage:**
```yaml
jobs:
  checkov:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6
      - uses: govuk-one-login/org-github-actions/checkov@v1
        with:
          config_file: .checkov.yaml        # Optional, defaults to '.checkov.yaml'
          working_directory: terraform      # Optional, defaults to '.'
```

**Minimal usage (uses defaults):**
```yaml
jobs:
  checkov:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6
      - uses: govuk-one-login/org-github-actions/checkov@v1
```

### wrap-iam-policies

Generates CloudFormation wrappers for IAM policy template files, allowing them to be checked by tools such as Checkov.

**Usage:**
```yaml
jobs:
  checkov-with-wrap-iam-policies:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6
      - uses: govuk-one-login/org-github-actions/wrap-iam-policies@v1
        with:
          policy_directory: 'terraform/policies'    # Optional, defaults to 'terraform/policies'
          working_directory: '.'                    # Optional, defaults to '.'
      - uses: govuk-one-login/org-github-actions/checkov@v1
        with:
          working_directory: terraform
```

> Note: 
>
> For the above example to work, the `policy_directory` of `wrap-iam-policies` must be within the `working_directory` of `checkov` 

**Minimal usage (uses defaults):**
```yaml
jobs:
  wrap-iam-policies:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6
      - uses: govuk-one-login/org-github-actions/wrap-iam-policies@v1
```


### Terraform Validate

Runs Terraform formatting and validation checks.

**Usage:**
```yaml
jobs:
  terraform-validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6
      - uses: govuk-one-login/org-github-actions/terraform-validate@v1
        with:
          terraform_dir: terraform         # Optional, defaults to 'terraform'
          terraform_version: 1.13.3        # Optional, defaults to '1.13.3'
```

**Minimal usage (uses defaults):**
```yaml
jobs:
  terraform-validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6
      - uses: govuk-one-login/org-github-actions/terraform-validate@v1
```


### TFLint

Runs TFLint validation checks.

> Note: a tflint config file must be present in the calling repository to use this action.

**Usage:**
```yaml
jobs:
  tflint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6
      - uses: govuk-one-login/org-github-actions/tflint@v1
        with:
          tflint_version: v0.50.3         # Optional, defaults to 'v0.50.3'
          terraform_dir: terraform        # Optional, defaults to 'terraform'
          tflint_config: .tflint.hcl      # Optional, defaults to '.tflint.hcl'
```

**Minimal usage (uses defaults):**
```yaml
jobs:
  tflint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6
      - uses: govuk-one-login/org-github-actions/tflint@v1
```

### pre-commit

Runs specific pre-commit tasks defined within the calling repository.

> Note: pre-commit must be set up within the calling repository (i.e. it should have a `.pre-commit-config.yaml`) to use this action.

To run multiple `pre-commit-tasks`, pass in a comma seperated list of task ids.

**Usage:**
```yaml
jobs:
  pre-commit:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6
      - uses: govuk-one-login/org-github-actions/pre-commit@v1
        with:
          python-version: 3.13                             # Optional, defaults to '3.13'
          pre-commit-tasks: yamllint, trailing-whitespace  # Requried, must match id of existing pre-commit hook(s)
```

**Minimal usage (uses defaults):**
```yaml
jobs:
  pre-commit:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6
      - uses: govuk-one-login/org-github-actions/pre-commit@v1
        with:
          pre-commit-tasks: yamllint
```

## Versioning Strategy

### Production Repositories
Use the stable `@v1` tag for production repositories:
```yaml
uses: govuk-one-login/org-github-actions/.github/workflows/checkov.yaml@v1
```

### Testing Repository (ct-aws-sample-pipeline)
Use `@main` for testing new workflow changes:
```yaml
uses: govuk-one-login/org-github-actions/.github/workflows/checkov.yaml@main
```

### Release Process
1. Merge changes to `main`
2. Test in [`ct-aws-sample-pipeline`](https://github.com/govuk-one-login/ct-aws-sample-pipeline) using `@main`
3. When stable, promote to `v1`:
   ```bash
   git tag -f v1
   git push -f origin v1
   ```
4. All production repos automatically get the update on next run


## Re-Usable Workflows (Legacy)
There are some re-usable workflows available within this repository, which can be referenced within your own workflows.

> [!IMPORTANT]
>
> In the future, these may not be kept as up to date as the composable actions. 
> It is instead recommended to use the composable actions (as described above) in your workflows


### Checkov Security Scan

Runs Checkov security scanning with SARIF output for GitHub Security tab integration.

**Usage:**
```yaml
jobs:
  checkov:
    uses: govuk-one-login/org-github-actions/.github/workflows/checkov.yaml@v1
    with:
      config_file: .checkov.yaml        # Optional, defaults to '.checkov.yaml'
      working_directory: terraform      # Optional, defaults to '.'
```

**Minimal usage (uses defaults):**
```yaml
jobs:
  checkov:
    uses: govuk-one-login/org-github-actions/.github/workflows/checkov.yaml@v1
```

### Terraform Validate

Runs Terraform formatting, validation, and TFLint checks.

**Usage:**
```yaml
jobs:
  terraform-validate:
    uses: govuk-one-login/org-github-actions/.github/workflows/terraform-validate.yaml@v1
    with:
      terraform_dir: terraform        # Optional, defaults to 'terraform'
      tflint_config: .tflint.hcl      # Optional, defaults to '.tflint.hcl'
      terraform_version: 1.13.3       # Optional, defaults to '1.13.3'
      tflint_version: v0.50.3         # Optional, defaults to 'v0.50.3'
```

**Minimal usage (uses defaults):**
```yaml
jobs:
  terraform-validate:
    uses: govuk-one-login/org-github-actions/.github/workflows/terraform-validate.yaml@v1
```

### Permissions

The re-useable workflows include the necessary permissions:
- `security-events: write` - For SARIF uploads
- `contents: read` - For repository access
- `actions: read` - For workflow access
