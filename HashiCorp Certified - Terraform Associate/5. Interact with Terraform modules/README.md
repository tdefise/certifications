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

#### Data Types

The ``type`` argument in a ``variable`` block allows you to restrict the type of value that will be accepted as the value for a variable. If no type constraint is set then a value of any type is accepted.

Type constraints are created from a mixture of type keywords and type constructors. The supported type keywords are:

- string: A sequence of Unicode characters representing some text, like "hello".
- number: A numeric value. The number type can represent both whole numbers like 15 and fractional values like 6.283185.
- bool: A boolean value, either true or false. bool values can be used in conditional logic.



The type constructors allow you to specify complex types such as collections:

- list(<TYPE>): A sequence of values

````hcl
variable "users" {
  type    = list
  default = ["root", "user1", "user2"]
}
````

- set(<TYPE>): A collection type that represents a set of unique values.

````hcl
variable "users_set" {
  type = set(string)
  default = (["root", "user1", "user2"])
}
````

- map(<TYPE>): A set of key-value pairs with unique keys, the key type must be string, and the value type is arbitrary.

````hcl
variable "plans" {
  type = map
  default = {
    small  = "1xCPU-1GB"
    medium = "1xCPU-2GB"
    large = "2xCPU-4GB"
  }
}
````

- object({<ATTR NAME> = <TYPE>, ... }): An object refers to a set of conforming types consisting of attributes with names and types
- tuple([<TYPE>, ...]): A tuple is like a list and is a contiguous set of values, but each element has an independent type.

#### Data 

##### Input Variables

Input variables let customize aspects of Terraform modules **without altering the module's own source code**.
This functionality allows you to share modules across different Terraform configurations, making your module composable and reusable.

Each input variable accepted by a module must be declared using a variable block:

````hcl
variable "image_id" {
  type = string
}

variable "availability_zone_names" {
  type    = list(string)
  default = ["us-west-1a"]
}
````

#### Variable Definitions (.tfvars) Files

To set lots of variables, it is more convenient to specify their values in a variable definitions file (with a filename ending in either ``.tfvars`` or ``.tfvars.json``) and then specify that file on the command line with ``-var-file``:

````bash
terraform apply -var-file="testing.tfvars"
````

Terraform also automatically loads a number of variable definitions files if they are present:

- Files named exactly ``terraform.tfvars`` or ``terraform.tfvars.json``.
- Any files with names ending in ``.auto.tfvars`` or ``.auto.tfvars.json``.


##### Side Note: Sensitive 

Setting a variable as ``sensitive`` prevents Terraform from showing its value in the plan or apply output, when you use that variable elsewhere in your configuration.

Terraform will still record sensitive values in the state, and so anyone who can access the state data will have access to the **sensitive values in cleartext**.

````hcl
variable "user_information" {
  type = object({
    name    = string
    address = string
  })
  sensitive = true
}
````

##### Side Note: Command Line

To specify individual variables on the command line, use the ``-var`` option when running the ``terraform plan`` and ``terraform apply`` commands:

````bash
terraform apply -var="image_id=ami-abc123"
````

#### Local Variables

A local value assigns a name to an expression, so you can use the name multiple times within a module instead of repeating the expression.

````hcl
locals {
  service_name = "forum"
  owner        = "Community Team"
}
````

Once a local value is declared, you can reference it in expressions as ``local.<NAME>.``

*Note that Terraform recommends to use local values in moderation, such as in situations where a single value or result is used in many places and that value is likely to be changed in future.*

#### Output Variables

Output values make information about your infrastructure available on the command line, and can expose information for other Terraform configurations to use.

Each output value exported by a module must be declared using an ``output`` block:

````hcl
output "instance_ip_addr" {
  value = aws_instance.server.private_ip
}
````

### 5c) Describe variable scope within modules/child modules

### 5d) Discover modules from the public Terraform Module Registry

### 5e) Defining module version

Published modules support versioning, automatically generate documentation, allow browsing version histories, show examples and READMEs, and more.

Public modules are managed via Git and GitHub.
The list below contains all the requirements for publishing a module:

- **GitHub**. The module must be on GitHub and must be a public repo. This is only a requirement for the public registry. If you're using a private registry, you may ignore this requirement.
- **Named ``terraform-<PROVIDER>-<NAME>``**: Module repositories must use this three-part name format, where ``<NAME>`` reflects the type of infrastructure the module manages and ``<PROVIDER>`` is the main provider where it creates that infrastructure. The ``<NAME>`` segment can contain additional hyphens. Examples: terraform-google-vault or terraform-aws-ec2-instance.
- **Repository description. The GitHub repository description is used to populate the short description of the module. This should be a simple one sentence description of the module.
- **Standard module structure**: The module must adhere to the standard module structure. This allows the registry to inspect your module and generate documentation, track resource usage, parse submodules and examples, and more.
- **x.y.z tags for releases**: The registry uses tags to identify module versions. Release tag names must be a semantic version, which can optionally be prefixed with a v. For example, v1.0.4 and 0.9.2. To publish a module initially, at least one release tag must be present. Tags that don't look like version numbers are ignored.
