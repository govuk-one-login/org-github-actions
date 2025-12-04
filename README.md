# Organization GitHub Actions

Reusable GitHub Actions workflows for GDS Digital Identity AWS infrastructure repositories.

## Available Workflows

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
      terraform_dir: terraform         # Optional, defaults to 'terraform'
      tflint_config: .tflint.hcl      # Optional, defaults to '.tflint.hcl'
      terraform_version: 1.13.3        # Optional, defaults to '1.13.3'
      tflint_version: v0.50.3         # Optional, defaults to 'v0.50.3'
```

**Minimal usage (uses defaults):**
```yaml
jobs:
  terraform-validate:
    uses: govuk-one-login/org-github-actions/.github/workflows/terraform-validate.yaml@v1
```

## Composable actions

These actions can be composed into a custom workflow so we can extend the
default workflows with additional behaviors

### Generate Policy Wrappers

Generates CloudFormation wrappers for IAM policy template files to enable security scanning.

**Usage:**
```yaml
jobs:
  checkov:
    runs-on: ubuntu-latest
    steps:
      - uses: govuk-one-login/org-github-actions/wrap-iam-policies@v1
        with:
          policy_directory: terraform/policies  # Optional, defaults to 'terraform
      - uses: govuk-one-login/org-github-actions/checkov@v1
    
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
2. Test in `ct-aws-sample-pipeline` using `@main`
3. When stable, promote to `v1`:
   ```bash
   git tag -f v1
   git push -f origin v1
   ```
4. All production repos automatically get the update on next run

## Permissions

Both workflows include the necessary permissions:
- `security-events: write` - For SARIF uploads
- `contents: read` - For repository access
- `actions: read` - For workflow access
