## Fundamentals

### 1a Explain what IaC is

Infrastructure-as-Code help you to write scripts to:

- Create
- Update
- Destroy

resources

#### Problems it address

- Avoid configurations due to human errors
- Help to manage the expected state of configuration to stay secure and compliant
- Help to better transfer knowledge between team members

#### Declarative vs Imperative

Within **Declarative**, we state **what** we want to get.
Declaritive tends to use scripting languagues eg. JSON, YAML, XML

This comes with the following pros:

- Implentation of all the ideas and requiremments
- Uses scripting languagues eg. JSON, YAML, XML

There are some cons:

- Required to have an additional skillset
- The timeline is extended

Within **Imperative**, we state **how** we want to get it.
Imperative tends to use scripting languagues eg. Python, Ruby, JavaScript

#### Infrastructure Lifecyle

The lifecyle of a resource is the following, plan -> design -> build -> test -> deliver -> maintain -> retire

Day 0-2 is a simplified way to describe phases of an infrastructure lifecycle:

- Day 0: Plan and design
- Day 1: Develop and iterate
- Day 2: Go live and maitain

#### Idempotent vs Non-Idempotent

**Idempotence** is the property of certain operations in mathematics and computer science whereby they can be applied multiple times without changing the result beyond the initial application.

For example, if you are use an system that is idempotent:

- When you **initially** ask for two VMs to be deployed, two VMs will be deployed
- When you ask again to deploy those two VMs, it will eventually deploy them again by either modifying or deleting and recreating them

When using an non-idempotent method, you will end up with four VMs

#### Provisionning vs Deploying vs Orchestrating

- **Provisionning** is the fact of deploying an asset.
- **Deploying** is the fact of putting a new application, or a new version on a provisionned asset.
- **Orchestrating** is the fact of arranging or coordinating multiple systems.

#### Configuration drift

Configuration drift is caused by inconsistent configuration items across computers or devices. Configuration drift occurs naturally in data center environments when changes to software and hardware are made ad hoc and are not recorded or tracked in a comprehensive and systematic fashion.

Note that within Terraform, configuration drifts are handled throught the ``refresh`` and ``plan`` commands

#### Mutable vs Immutable

Mutable infrastructures allow for regular updates and modifications after the software has been deployed, whereas immutable infrastructures do not allow modifications once the software has been deployed.

<https://www.hashicorp.com/resources/what-is-mutable-vs-immutable-infrastructure>

#### GitOps

GitOps is an operational framework that takes DevOps best practices used for application development such as version control, collaboration, compliance, and CI/CD tooling, and applies them to infrastructure automation.

### 1b Describe advantages of IaC patterns

- **Reliability**: It make changes **idempotent**, consistent, repeatable and predictable
- **Manageability**:
  - It enable **mutation via code**
  - It can be revised, with minimal changes
- **Sensibility**: It can potentially avoid financial and reputational losses.
