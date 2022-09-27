## Use the Terraform CLI (outside of core workflow)

### 4a) Given a scenario: choose when to use terraform fmt to format code

The ``terraform fmt`` command is used to rewrite Terraform configuration files to a canonical format and style.

### 4b)  Given a scenario: choose when to use terraform taint to taint Terraform resources

The ``terraform taint`` command informs Terraform that a particular object has become degraded or damaged.

Terraform represents this by marking the object as "tainted" in the Terraform state, and Terraform will propose to replace it in the next plan you create.

The ``<address>`` argument is the address of the resource to mark as tainted.

Usage:

- ``terraform taint [options] <address>``

> *Note that this command is deprecated. For Terraform v0.15.2 and later, we recommend using the -replace option with terraform apply instead*

### 4c) Given a scenario: choose when to use terraform import to import existing infrastructure into your Terraform state

The ``terraform import`` command imports existing resources into Terraform.

Import will find the existing resource from ID and import it into your Terraform state at the given ADDRESS.

ADDRESS must be a valid resource address.

To import a resource, a ``resource`` block must be firstly written, establishing the name by which it will be known to Terraform:

````hcl
resource "aws_instance" "example" {
  # ...instance configuration...
}
````

Then, run ``terraform import`` in order to attach an existing instance to this resource configuration

> terraform import aws_instance.example i-abcd1234

### 4d)

### 4e)

### 4f)
