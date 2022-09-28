## Interact with Terraform modules

### Intro

When more and more infrastructure is provisionned with Terraform, there could be the following problems:

- Understanding and navigating the configuration files will become increasingly difficult
- Updating the configuration will become more risky, as an update to one section may cause unintended consequences to other parts of your configuration
- There will be an increasing amount of duplication of similar blocks of configuration, for instance when configuring separate dev/staging/production environments
- There will be difficulties to share parts of the configuration between projects and teams

In order to avoid those problems, modules are available, in order to:

- **Organize configuration**: Modules make it easier to navigate, understand, and update your configuration by keeping related parts of the configuration together.
- **Encapsulate configuration**: Modules allow the possibility to encapsulate configuration into distinct logical components. Encapsulation can help prevent unintended consequences, such as a change to one part of your configuration accidentally causing changes to other infrastructure, and reduce the chances of simple errors like using the same name for two different resources.
- **Re-use configuration**: Modules can save time and reduce costly errors by re-using configuration by team members who have published modules.
- **Provide consistency and ensure best practices**: Modules also help to provide consistency in your configurations. Not only does consistency make complex configurations easier to understand, it also helps to ensure that best practices are applied across all of your configuration.

A Terraform module is a set of Terraform configuration files in a single directory.

When running Terraform commands directly from such a directory, it is considered the **root module**

Terraform commands will only directly use the configuration files in one directory, which is usually the current working directory.
However, **the configuration can use module blocks to call modules in other directories.**

#### Side Note: Best Practises

- Name provider ``terraform-<PROVIDER>-<NAME>``
- Write configurations with modules in mind
- Use local modules to organize and encapsulate the code
- Use the public Terraform Registry to find useful modules.
- Publish and share modules.

#### Side Note: Example

- **LICENSE**: Contains the license under which the module will be distributed.
- **README.md**: Contains documentation describing how to use the module, in markdown format.

````md
# AWS S3 static website bucket

This module provisions AWS S3 buckets configured for static website hosting.
````

- **main.tf**: Contains the main set of configuration for the module.

````hcl
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "3.14.0"

  name = var.vpc_name
  cidr = var.vpc_cidr

  azs             = var.vpc_azs
  private_subnets = var.vpc_private_subnets
  public_subnets  = var.vpc_public_subnets

  enable_nat_gateway = var.vpc_enable_nat_gateway

  tags = var.vpc_tags
}
````

- **variables.tf**: Contains the variable definitions for the module.
- **outputs.tf**: Contains the output definitions for the module.

### 5a) Contrast module source options

Modules can either be loaded from the local filesystem, or a remote source.

Terraform supports a variety of remote sources, including:

- Terraform Registry
- Most version control systems
- HTTP URLs
- Terraform Cloud
- Terraform Enterprise private module registries

### 5b) Interact with module inputs and outputs

### 5c) Describe variable scope within modules/child modules

### 5d) Discover modules from the public Terraform Module Registry

### 5e) Defining module version
