## Fundamentals

### 8a) Demonstrate use of variables and outputs

#### Input variables

Input variables let you customize aspects of Terraform modules without altering the module's own source code.

This functionality allows you to share modules across different Terraform configurations, making your module composable and reusable.

Here below is how to create a variable

````hcl
variable "instance_type" {
    type = string

}
````

#### Output variable

Output values make information about your infrastructure available on the command line, and can expose information for other Terraform configurations to use.

Output values have several uses:

- A child module can use outputs to expose a subset of its resource attributes to a parent module.
- A root module can use outputs to print certain values in the CLI output after running terraform apply.
- When using remote state, root module outputs can be accessed by other configurations via a terraform_remote_state data source.

````hcl
output "instance_ip_addr" {
  value = aws_instance.server.private_ip
}
````

#### Side Note: Local Values

A local value assigns a name to an expression, so you can use the name multiple times within a module instead of repeating the expression.

````hcl
locals {
  service_name = "IT"
  owner        = "Thomas Defise"
}
````

Use local values only in **moderation**, in situations where a single value or result is used in many places **and** that value is likely to be changed in future.

### 8b) Describe secure secret injection best practice

Terraform recommends to avoid placing secrets in Terraform config or state file wherever possible.

#### Technique n°1: Environmental Variables

Takes advantage of Terraform’s native support for reading environment variables.

- **TF_LOG**: Enables detailed logs to appear on stderr which is useful for debugging.
  > export TF_LOG=trace
- **TF_LOG_PATH**: Specifies where the log should persist its output to.

### 8c) Understand the use of collection and structural types

### 8d) Create and differentiate resource and data configuration

### 8e) Use resource addressing and resource parameters to connect resources together

A **resource address** is a string that identifies zero or more resource instances in your overall configuration.

An address is made up of two parts:

> [module path][resource spec]

- Module Path: Addresses a module within the tree of modules
  > module.module_name[module index]
  - **module**: Module keyword indicating a child module (non-root). Multiple module keywords in a path indicate nesting.
  - **module_name**: User-defined name of the module.
  - **[module index] *(Optional)***: Index to select an instance from a module call that has multiple instances, surrounded by square bracket characters ([ and ]).
- Resource spec: Addresses a specific resource instance in the selected module
  > resource_type.resource_name[instance index]
  - **resource_type**: Type of the resource being addressed.
  - **resource_name**: User-defined name of the resource.
  - **[instance index] *(Optional)***: Index to select an instance from a resource that has multiple instances, surrounded by square bracket characters ([ and ]).

Examples:

- ``count`` Example
  
  > ````hcl
  >resource "aws_instance" "web" {
  >  # ...
  >  count = 4
  >}
  >````

> aws_instance.web[3] -> Refers to only the last instance in the config
> aws_instance.web -> Refers to all four "web" instances.

- ``for_each`` Example
  
  > ````hcl
  >resource "aws_instance" "web" {
  >  # ...
  >  for_each = {
  >    "terraform": "value1",
  >    "resource":  "value2",
  >    "indexing":  "value3",
  >    "example":   "value4",
  >  }
  >}
  >````

> aws_instance.web[3] -> Refers to only the last instance in the config
> aws_instance.web -> Refers to all four "web" instances.

### 8f) Use Terraform built-in functions to write configuration

The Terraform language includes a number of built-in functions that you can call from within expressions to transform and combine values.

- Numeric Functions:
  - ``abs``: Returns the absolute value of the given number.
  - ``ceil``: Returns the closest *whole number* that is greater than or equal to the given value, which may be a fraction.
  - ``floor``: Returns the closest whole number that is less than or equal to the given value, which may be a fraction.
  - ``log``: Returns the logarithm of a given number in a given base.
  
  > log(number, base)

  - ``max``: Takes one or more numbers and returns the greatest number from the set
  - ``min``: Takes one or more numbers and returns the smallest number from the set.
  - ``parseint``: Parses the given string as a representation of an integer in the specified base and returns the resulting number.
  - ``pow``: Calculates an exponent, by raising its first argument to the power of the second argument.
  - ``signum``: Determines the sign of a number, returning a number between -1 and 1 to represent the sign.
- String functions:
  - ``chomp``: Removes newline characters at the end of a string.

### 8g)

### 8h)
