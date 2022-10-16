## 7. Implement and maintain state

### Intro

When using open source Terraform, you are responsible for maintaining a **state** file as the source of truth for your cloud infrastructure

#### Backend

A **backend** defines where Terraform stores its state data files.

To configure a backend, a nested ``backend`` block within the top-level ``terraform`` block. The following example configures the ``remote`` backend.

````hcl
terraform {
  backend "remote" {
    organization = "Thomas Defise"

    workspaces {
      name = "my-supper-app"
    }
  }
}
````

### 7a) Describe default local backend

A backend defines where Terraform stores its state data files.

Terraform uses persisted state data to keep track of the resources it manages. Most non-trivial Terraform configurations either integrate with Terraform Cloud or use a backend to store state remotely.

By default, Terraform uses a backend called ``local``, which stores state as a local file on disk.

If a configuration includes no backend block, Terraform defaults to using the ``local`` backend, which stores state as a plain file in the current working directory.

### 7b) Outline state locking

If supported by the backend used, Terraform will lock the state for all operations that could write state.
This prevents others from acquiring the lock and potentially corrupting your state.

State locking happens automatically on all operations that could write state.

#### Side Note: Unlock the state

Terraform has a ``force-unlock`` command to manually unlock the state if unlocking failed.

### 7c) Handle backend authentication methods

Accessing remote state generally requires access credentials, since state data contains extremely sensitive information.

Terraform writes the backend configuration in plain text in two separate files.

- The ``.terraform/terraform.tfstate`` file contains the backend configuration for the current working directory.
- All plan files capture the information in ``.terraform/terraform.tfstate`` at the time the plan was created. This helps ensure Terraform is applying the plan to correct set of infrastructure.

### 7d) Describe remote state storage mechanisms and supported standard backends

Terraform has a built-in selection of backends:

- **remote**: The remote backend is unique among all other Terraform backends because it can both store state snapshots and execute operations for Terraform Cloud's CLI-driven run workflow. It used to be called an "enhanced" backend.
- **:
- **:
- **:
- **:

#### Workspaces

When managing multiple environment, there can be the following challenges:

- Multiple stage to manage
- Multiple variable values
- Multiple set of credentials to manage

This add complexity.

In order to create a workspace, use the ``terraform workspace new <workspace>`` and then ``terraform plan``

For instance we have six environnment:

||Dev|UAT|NONF|Staging|Prod|DR|
|-|-|-|-|-|-|
|CIDR|10.0.0.0/24|10.0.1.0/24|10.0.2.0/24|10.0.3.0/24|10.0.4.0/24|10.1.4.0/24|
|Instance Type|Small|Medium|Medium|Medium|Medium|Medium|
|Instance Count|2|2|2|2|4|4|

Here is for instance, an example of **locals.tf**

````hcl
locals {
  
  common_tags{
    environment = terraform.workspace
  }

  name_prefix = "${var.naming_prefix}-${terraform.workspace}"
}
````

Here is for instance, an example of **terraform.tfvars**

````hcl
cidr_block = {
    Dev     = "10.0.0.0/24"
    UAT     = "10.0.1.0/24"
    NONF    = "10.0.2.0/24"
    Staging = "10.0.3.0/24"
    Prod    = "10.0.4.0/24"
    DR      = "10.1.4.0/24"
}
````

Here is for instance, an example of **network.tf**

````hcl
module "virtual network" {
  source  = ...
  version = ...

  name                = "example-network"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.workplace.name
  address_space       = var.cidr_block[terraform.workspace]
  dns_servers         = ...
  
  ....

}
````

There are some important limitations on backend configuration:

- A configuration can only provide one backend block.
- A backend block cannot refer to named values (like input variables, locals, or data source attributes).

*Note that it is not required to have a backend when using Terraform Cloud because Terraform Cloud automatically manages state in the workspaces associated with your configuration.*

### 7e) Describe effect of Terraform refresh on state

The ``terraform refresh`` command reads the current settings from all managed remote objects and updates the Terraform state to match.

### 7f) Describe backend block in configuration and best practices for partial configurations

A backend defines where Terraform stores its state data files.

Terraform uses persisted state data to keep track of the resources it manages. Most non-trivial Terraform configurations either integrate with Terraform Cloud or use a backend to store state remotely.

In regards of credentials and sensitive data, Terraform recommends using environment variables to supply credentials and other sensitive data.

#### Partial Configuration

It is not required to have all required argument in the backend configuration.
When some or all of the arguments are omitted, we call this a *partial configuration*.

When using partial configuration, Terraform requires at a minimum that an empty backend configuration is specified in one of the root Terraform configuration files, to specify the backend type. For example:

````hcl
terraform {
  backend "consul" {}
}
````

### 7g) Understand secret management in state files

Terraform state can contain sensitive data, depending on the resources in use and your definition of "sensitive."

- When using **local state**, state is stored in plain-text JSON files.
- When using **remote state**, state is only ever held in memory when used by Terraform. It may be encrypted at rest, but this depends on the specific remote state backend.

This means that storing state remotely **can provide better security**

### Side Note: Unmanage a resource

You can use ``terraform state rm`` in the less common situation where you wish to remove a binding to an existing remote object without first destroying it, which will effectively make Terraform "forget" the object while it continues to exist in the remote system.
