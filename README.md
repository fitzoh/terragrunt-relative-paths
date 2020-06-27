# Possible Terragrunt Issue
This repo reproduces a possible inconsistency with how the `path_relative_*_include()` functions are handled when run with the `terragrunt *-all` commands.

The docs for the `*-all` commands state that they run the base command in each subfolder.
```
   plan-all             Display the plans of a 'stack' by running 'terragrunt plan' in each subfolder
   apply-all            Apply a 'stack' by running 'terragrunt apply' in each subfolder
   output-all           Display the outputs of a 'stack' by running 'terragrunt output' in each subfolder
   destroy-all          Destroy a 'stack' by running 'terragrunt destroy' in each subfolder
   validate-all         Validate 'stack' by running 'terragrunt validate' in each subfolder
```

This repo contains a parent `terragrunt.hcl` file that reads a yaml file from the child that includes it.
The parent `terragrunt.hcl` contains the `skip=true` attribute, so it should not be executed in isolation.

The child directory (`/child`) contains a simple `terragrunt.hcl` file which only includes the parent, an empty `main.tf` file to allow `terragrunt` to execute, and a simple `config.yml` file with a single key/value pair.

## Demonstration
Run `terragrunt plan` in `/module`. It completes without error.

Run `terragrunt plan-all` in the repository root.
It fails with an error due to it not finding a file at `${path_relative_to_include()}/config.yml`.

From the documentation, I would expect these two commands to behave identically.
