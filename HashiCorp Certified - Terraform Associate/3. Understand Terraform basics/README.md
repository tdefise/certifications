## Understand Terraform basics

### 3a) Handle Terraform and provider installation and versioning

### 3b) Describe plug-in based architecture

Terraform is a tool for building, changing, and versioning infrastructure safely and efficiently.
Terraform is built on a plugin-based architecture, enabling developers to extend Terraform by writing new plugins or compiling modified versions of existing plugins.

Terraform is logically split into two main parts:

- Terraform Core: A statically-compiled binary written in the Go programming language. The compiled binary is the command line tool (CLI) terraform, the entrypoint for anyone using Terraform. The code is open source and hosted at github.com/hashicorp/terraform.
The primary responsibilities of Terraform Core are:
  - Infrastructure as code: reading and interpolating configuration files and modules
  - Resource state management
  - Construction of the Resource Graph
  - Plan execution
  - Communication with plugins over RPC

- Terraform Plugins: Are written in Go and are executable binaries invoked by Terraform Core over RPC. Each plugin exposes an implementation for a specific service, such as AWS, or provisioner, such as bash.
The primary responsibilities of Provider Plugins are:
  - Initialization of any included libraries used to make API calls
  - Authentication with the Infrastructure Provider
  - Define Resources that map to specific Services

### 3c)

### 3d) Describe how Terraform finds and fetches providers

**Providers** allow Terraform to interact with cloud providers, SaaS providers, and other APIs.

Provider configurations belong in the **root module** of a Terraform configuration.

#### Multiple Provider Configurations

It is possible to define multiple configurations for the same provider, and select which one to use on a per-resource or per-module basis.

````hcl
provider "aws" {
  alias  = "west"
  region = "us-west-2"
}
````

### 3e) Explain when to use and not use provisioners and when to use local-exec or remote-exec

Terraform includes the concept of provisioners as a measure of pragmatism, knowing that there are always certain behaviors that cannot be directly represented in Terraform's declarative model

Providers are used to model specific actions on the local machine or on a remote machine in order to prepare servers or other infrastructure objects for service.

*However, Terraform do not recommend using provisioners for any of the use-cases described in the following sections.*

Here are some use-cases:

- Passing data into virtual machines and other compute resources: Used to pass in data about other related infrastructure that the software on that server will need to do its job
  - *Amazon EC2*: user_data or user_data_base64 on aws_instance, aws_launch_template, and aws_launch_configuration.
  - *Microsoft Azure*: custom_data on azurerm_virtual_machine or azurerm_virtual_machine_scale_set.
- Running configuration management software: Terraform includes a number of specialized provisioners for launching specific configuration management products

#### local-exec

The ``local-exec`` provisioner invokes a local executable after a resource is created.

Here are somme common arguments:

- **command *(Required)***: Command to execute.
- **working_dir *(Optional)***: Specifies the working directory where command will be executed.
- **interpreter *(Optional)***: List of interpreter arguments used to execute the command.

````hcl
resource "null_resource" "example" {
  provisioner "local-exec" {
    command = "Get-Date > completed.txt"
    interpreter = ["PowerShell", "-Command"]
  }
}
````

##### Failure Behavior

By default, provisioners that fail will also cause the Terraform apply itself to fail. The on_failure setting can be used to change this. The allowed values are:

- ``continue``: Ignore the error and continue with creation or destruction.
- ``fail``: Raise an error and stop applying (the default behavior). If this is a creation provisioner, taint the resource.

````hcl
resource "aws_instance" "web" {
  # ...

  provisioner "local-exec" {
    command    = "echo The server's IP address is ${self.private_ip}"
    on_failure = continue
  }
}
````

#### remote-exec

The ``remote-exec`` provisioner invokes a script on a remote resource after it is created.
The ``remote-exec`` provisioner requires a connection and supports both ``ssh`` and ``winrm``.

````hcl
resource "aws_instance" "web" {
  # ...

  # Establishes connection to be used by all
  # generic remote provisioners (i.e. file/remote-exec)
  connection {
    type     = "ssh"
    user     = "root"
    password = var.root_password
    host     = self.public_ip
  }

  provisioner "remote-exec" {
    inline = [
      "puppet apply",
      "consul join ${aws_instance.web.private_ip}",
    ]
  }
}
````

#### file

The ``file`` provisioner copies files or directories from the machine running Terraform to the newly created resource.
The ``file`` provisioner supports both ``ssh`` and ``winrm`` type connections.

````hcl
resource "aws_instance" "web" {
  # ...

  # Copies the myapp.conf file to /etc/myapp.conf
  provisioner "file" {
    source      = "conf/myapp.conf"
    destination = "/etc/myapp.conf"
  }

  # Copies the string in content into /tmp/file.log
  provisioner "file" {
    content     = "ami used: ${self.ami}"
    destination = "/tmp/file.log"
  }

  # Copies the configs.d folder to /etc/configs.d
  provisioner "file" {
    source      = "conf/configs.d"
    destination = "/etc"
  }

  # Copies all files and folders in apps/app1 to D:/IIS/webapp1
  provisioner "file" {
    source      = "apps/app1/"
    destination = "D:/IIS/webapp1"
  }
}
````
