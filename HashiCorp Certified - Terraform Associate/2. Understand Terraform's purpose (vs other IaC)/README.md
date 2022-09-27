## 2. Understand Terraform's purpose (vs other IaC)

### 2a) Explain multi-cloud and provider-agnostic benefits

Provisioning infrastructure across multiple clouds increases fault-tolerance, allowing for more **graceful recovery** from cloud provider outages.

Terraform lets you use the same workflow to manage multiple providers and handle **cross-cloud dependencies**.

### 2b) Explain the benefits of state

Terraform requires some sort of database to map Terraform config to the real world.

#### Metadata

Terraform tracks metadata such as resource dependencies.

Terraform typically uses the configuration to determine **dependency order**. However, when a resource is deleted from a Terraform configuration, Terraform must know how to delete that resource from the remote system. Terraform can see that a mapping exists in the state file for a resource not in your configuration and plan to destroy. However, since the configuration no longer exists, the order cannot be determined from the configuration alone.

To ensure correct operation, Terraform retains a copy of the most recent set of dependencies within the state.

#### Performance

Terraform stores a cache of the attribute values for all resources in the state, so that when the ``plan`` command is used, it can quickly determine the changes that it needs to make to reach your desired configuration.

#### Syncing

In the default configuration, Terraform stores the state in a file in the current working directory where Terraform was run.

Remote state is the recommended solution to this problem. With a fully-featured state backend, Terraform can use remote locking as a measure to avoid two or more different users accidentally running Terraform at the same time, and thus ensure that each Terraform run begins with the most recent updated state.
