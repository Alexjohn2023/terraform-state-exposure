# Terraform State Exposure & Workspace Isolation Lab

## Overview

This project demonstrates two important Terraform concepts:

1. **Terraform State Exposure**

   * Why sensitive values can still appear in `.tfstate`
   * Why `sensitive = true` is not enough
   * How Terraform 1.10+ Ephemeral Values help protect secrets

2. **Terraform Workspaces**

   * Managing multiple environments from a single codebase
   * Isolating state files for Development, Staging, and Production
   * Reducing code duplication and configuration drift

---

## Why This Matters

Many teams store secrets in services such as:

* AWS Systems Manager Parameter Store
* AWS Secrets Manager
* Azure Key Vault
* HashiCorp Vault

However, Terraform may still write these values to the state file.

For example:

```hcl
variable "db_password" {
  type      = string
  sensitive = true
}
```

While `sensitive = true` hides values from terminal output, it does **not** prevent them from being stored in `terraform.tfstate`.

If the state file is exposed, credentials may be exposed as well.

---

## Lab 1 – Demonstrating State Exposure

### Initialize Terraform

```bash
terraform init
```

### Apply Configuration

```bash
terraform apply
```

### Inspect the State File

```bash
cat terraform.tfstate
```

or

```bash
grep -i "password" terraform.tfstate
```

Observe how sensitive values can still be present in the state file.

---

## Lab 2 – Protecting Secrets with Ephemeral Values

Terraform 1.10 introduced Ephemeral Values.

Example:

```hcl
variable "db_password" {
  type      = string
  sensitive = true
  ephemeral = true
}
```

Ephemeral values are not written to:

* Terraform state files
* Terraform plan files

This significantly reduces the risk of credential exposure.

> Note: Ephemeral values must be used with supported workflows and write-only arguments where applicable.

---

## Lab 3 – Environment Isolation with Workspaces

Create environments:

```bash
terraform workspace new dev
terraform workspace new staging
terraform workspace new prod
```

List workspaces:

```bash
terraform workspace list
```

Switch environments:

```bash
terraform workspace select dev
```

Apply environment-specific variables:

```bash
terraform apply -var-file="dev.tfvars"
```

Production:

```bash
terraform workspace select prod
terraform apply -var-file="prod.tfvars"
```

Each workspace maintains its own state while sharing the same Terraform configuration.

---

## Benefits of Workspaces

* Single codebase
* Reduced duplication
* Easier maintenance
* Environment isolation
* Consistent deployments

---

## Learning Objectives

By completing this lab, you will:

* Understand how Terraform stores state
* Identify risks associated with sensitive data in state files
* Learn how Ephemeral Values improve secret handling
* Create and manage Terraform Workspaces
* Deploy multiple environments from a single codebase

---

## Requirements

* Terraform 1.10+
* AWS CLI (optional)
* Git
* Basic Terraform knowledge

---

## References

* Terraform Documentation: https://developer.hashicorp.com/terraform
* Terraform Ephemeral Values: https://developer.hashicorp.com/terraform/language/manage-sensitive-data
* Terraform Workspaces: https://developer.hashicorp.com/terraform/cli/workspaces

---

## Author

Alexander Njoku

This repository was created as a hands-on lab for learning Terraform state management, secret protection, and multi-environment deployment strategies.
