## 9. Understand Terraform Cloud and Enterprise capabilities

### 9a) Describe the benefits of Sentinel, registry, and workspaces

#### Workspaces

Each Terraform configuration has an associated backend that defines how Terraform executes operations and where Terraform stores persistent data, like state.

The persistent data stored in the backend belongs to a **workspace**.

Terraform starts with a single, **default** workspace named default that cannot be delete.

Within your Terraform configuration, you may include the name of the current workspace using the ``${terraform.workspace}`` interpolation sequence.

Terraform Cloud workspace variables let you:

- Customize configurations, modify Terraform's behavior, and store information like provider credentials.

### 9b) Differentiate OSS and Terraform Cloud workspaces

### 9c) Summarize features of Terraform Cloud

Terrafrom Cloud offers secure remote state storage, make it easier to collaborate on infrastructure development.
