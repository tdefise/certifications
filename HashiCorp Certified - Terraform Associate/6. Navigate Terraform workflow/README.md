## Fundamentals

### 6a) Describe Terraform workflow (Write -> Plan -> Create)

The core Terraform workflow has three steps:

- **Write**: Author infrastructure as code.
- **Plan**: Preview changes before applying.
- **Apply**: Provision reproducible infrastructure.

#### Write

To avoid the burden and the security risk of each team member arranging all sensitive inputs locally, it's common for teams to migrate to a model in which **Terraform operations are executed in a shared Continuous Integration (CI) environment**.

#### Plan

For teams collaborating on infrastructure, Terraform's plan output **creates an opportunity for team members to review each other's work**.
This allows the team to ask questions, evaluate risks, and **catch mistakes before any potentially harmful changes are made**.

#### Apply

Once a pull request has been approved and merged, it's important for the team to review the final concrete plan that's run against the shared team branch and the **latest version of the state file**.

This plan has the potential to be different than the one reviewed on the pull request due to issues like merge order or recent infrastructural changes.

### 6b) Initialize a Terraform working directory

The ``terraform init`` command initializes a working directory containing Terraform configuration files.

This is the **first** command that should be run after writing a new Terraform configuration or cloning an existing one from version control.

This command is **always safe to run multiple times**, to bring the working directory up to date with changes in the configuration.

Terraform automatically creates or updates the dependency lock file each time you run the ``terraform init`` command.

A dependency lock file (**.terraform.lock.hcl**) is used to determine which versions of those dependencies are potentially compatible with the current configuration and which versions are currently selected for use.

It is recommended to have this file in the version control repository in order to discuss potential changes to with external dependencies through code review

### 6c) Validate a Terraform configuration

The ``terraform validate`` command validates the configuration files in a directory, **referring only to the configuration**.

Validate runs checks that verify whether a configuration is **syntactically valid** and **internally consistent**, regardless of any provided variables or existing state.

It is safe to run this command automatically, for example as a post-save check in a text editor

### 6d) Generate and review an execution plan for Terraform

The ``terraform plan`` command creates an execution plan, which lets you preview the changes that Terraform plans to make to your infrastructure. By default, when Terraform creates a plan it:

- Reads the current state of any already-existing remote objects to make sure that the Terraform state is up-to-date.
- Compares the current configuration to the prior state and noting any differences.
- Proposes a set of change actions that should, if applied, make the remote objects match the configuration.

The plan command alone **does not actually carry out the proposed changes**.
This command is used to check whether the proposed changes match what is expected before applying the changes or share the changes.

### 6f) Destroy Terraform managed infrastructure

The ``terraform destroy`` command is a convenient way to destroy all remote objects managed by a particular Terraform configuration.

Terraform is sometimes used to manage ephemeral infrastructure for development purposes, in which case you can use terraform destroy to conveniently clean up all of those temporary objects once you are finished with your work.

You can also create a speculative destroy plan, to see what the effect of destroying would be, by running the following command ``terraform plan -destroy``
