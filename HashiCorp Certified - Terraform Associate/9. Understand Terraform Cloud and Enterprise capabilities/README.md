## 9. Understand Terraform Cloud and Enterprise capabilities

### 9a) Describe the benefits of Sentinel, registry, and workspaces

#### Enforce Policy with Sentinel

Policies are rules that Terraform Cloud enforces on Terraform runs. You can use policies to validate that the Terraform plan complies with security rules and best practices.

You can use two policy-as-code frameworks to define fine-grained, logic-based policies:

- **Sentinel**: Define policies with the Sentinel policy language and use imports to parse the Terraform plan, state, and configuration
- **Open Policy Agent**: define policies with the Rego policy language.

Here below in an example of a rule

````hcl
# Allowed Types
allowed_types = [
    "t2.small",
    "t2.medium",
    "t2.large",
]

# Main rule
main = rule {
    validate_ec2_instance_types(allowed_types)
}
````

#### Registry

##### Private Registry

Terraform Cloud's private registry works similarly to the public Terraform Registry and helps to share Terraform providers and Terraform modules across within the organization.

##### Public Providers and Modules

Public modules and providers are hosted on the public Terraform Registry and Terraform Cloud can automatically synchronize them to an organization's private registry.

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
