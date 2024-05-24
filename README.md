# terraform

https://www.youtube.com/watch?v=V4waklkBC38

- IAC Tool
- declarative
- cloud agnostic

https://www.terraform-best-practices.com/

# What is Infrastructure as Code

Manual Configuration

- easily start using new service offerings to quickly prototype architectures
- easily to mis-configure a service though human error
- hard to manage the expected state of configuration for compliance
- hard to transfer configuration knowledge to other team members

IaC
- automate creating, updating or destroying cloud infrastructure
- blueprint of your infrastructure
- easily share, version or inventory your infrastructure

# Popular IaC Tools
declarative tools
- what you see is what you get
- zero chance of misconfiguration
- using scripting languages eg JSON, YAML, XML

imperative tools
- say what you want and the rest is filled in
- end up with misconfiguration
- does more than declarative
- python, ruby, js, golang

terraform is declarative but the Language features imperative-like functionality. The terraform language is HCL (HashiCorp Configuration Langauge) and supports Loops (for each), dynamic blocks, locals and has complex data structures like maps and collections.

# The Infrastructure Lifecycle
This means a number of clearly defined and distinct work phases which are used to plan, design, build, test, deliver, maintain and retire cloud infrastructure.

<p><b>Day 0</b> - plan and design<br>
<b>Day 1</b> - develop and iterate<br>
<b>Day 2</b> - go live and maintain</p>

# Advantages
IaC makes changes idempotent, consistent, repeatable and predictable.
IaC enables mutation via code with minimal changes.
With IaC you avoid financial and reputational losses to even loss of life when considering government and military dependencies on infrastructure.

# Notable features of terraform
- Installable modules
- plan and predict changes
- dependency graphing
- state management
- provision infrastructure in familiar languages
  - via AWS CDK
- terraform registry with 1000+ providers

# terraform lifecycle
- Write or update code
- init: initialize your project
- plan: what will change/be generated
- validate: types/values are valid and required attributes are present
- apply: execute theplan and provision the infrastructure
- destroy: destroy the remote infrastructure
- repeat

# Change Management
approach to apply change and resolving conflicts brought about by change. It is the procedure that will be followed when resources are modified and applied via configuration script. Change automation is creating a consistent, systematic and predictable way of managing change request via controls and policies. terraform uses change automation in the form of execution plans and resources graphs to apply and review complex changesets.

# Visualize an execution plan

``terraform graph | dot -Tsvg > graph.svg``

For this, install GraphViz. This is called the dependency graph. terraform walks this graph to generate plans, refresh state, and more.

# Install terraform

https://learn.hashicorp.com/tutorials/terraform/install-cli
Create a ``main.tf`` file. To connect to the correct (cloud) provider, copy and paste the code from "USE PROVIDER"

https://registry.terraform.io/browse/providers

into your ``main.tf`` file.

Also on https://registry.terraform.io/providers/hashicorp/google/latest/docs, you can find the documentation for many resources.

```
terraform {
  required_providers {
    google = {
      source = "hashicorp/google"
      version = "4.2.0"
    }
  }
}

provider "google" {
  # Configuration options
  credentials = "google_key_from_sa.json"
}
```

Also possible to link a json key of a service account under "google"."credentials". This file must be present in the directory of the terraform directory.
# How to save credentials accordingly

Over gcloud init + gcloud auth???

Example:

```
resource "google_storage_bucket" "auto-expire" {
  name          = "auto-expiring-bucket"
  location      = "EU"
  force_destroy = true

  lifecycle_rule {
    condition {
      age = 3
    }
    action {
      type = "Delete"
    }
  }
}
```

Use ``terraform init`` to initialize the directory (you must be in the correct one). This creates a ``.terraform`` directory and a ``terraform.lock.hd`` file (tells us what providers and modules we use). 

``terraform fmt`` corrects to format of the files.
``terraform validate`` tells if all files have the required attributes (not the correct content).
``terraform plan`` generates a plan of what it would deploy. if we are happy, then ``terraform apply``. Then ``yes``.
``terraform refresh`` refreshes the state file. Use the flag ``-auto-approve`` to skip the confirmation.
# Input variables

https://www.terraform.io/docs/language/values/variables.html

Then, you can reference it with var.{var_name} in the file. Run it with ``terraform plan -var={var_name}="???"``. Or, create a ``terraform.tfvars`` file with ``{var_name}="???``. The file ``terraform.tfstate`` defines the current state. There is also a ``terraform.tfstate.backup`` file.

# Local values

https://www.terraform.io/docs/language/values/locals.html

# Output values

https://www.terraform.io/docs/language/values/outputs.html

With this, you can get content from the deployment. Use ``terraform output {var_name}``.

# Modules

https://registry.terraform.io/browse/modules

Starts with a ``module`` Block and has the attribute ``source``. Do a ``terraform init`` to grab the module. Then, all is as always.

# Set up another provider

To the same and give it an additional attribute ``alias=NAME``. Reference it in the resource with the attribute provider=aws.NAME. In a module, you have to add something like
```
providers = {
  aws. aws.eu
}
```

# Splitting a main.tf into several files
Every `.tf` is used. You only can have on terraform and one provider block. It is common to make a `main.tf`, a `Outputs.tf` and a `variables.tf`. 

# Delete infrastructure
Use `terraform destroy`.

# Working with several workspaces

Use `terraform workspace list` to list all. A workspace is your environment.
Your workspace is defined in your terraform block

```
terraform {
  backend "local" {
    organization = "example_corp"

    workspaces {
      name = "my-app-prod"
    }
  }
}
```

Tutorial to use terraform cloud: https://www.terraform.io/docs/cloud/migrate/index.html

Create your own organization and a new workspace. You can use the defined workflows via version control, or CLI-driven or API-driven.
Authenticate with `terraform login`. The variables are local and so, you must set it in the workspace of the remote server via the browser.
You must also set the ENV variables which are required for the authentication to the provider in "Environment Variables".

# Provisioners

Provisioners can be used to model specific actions on the local machine or on a remote machine in order to prepare servers or other infrastructure objects for service.

# Local-Exec
Allows you to run local exec commands on your local machine.

https://www.terraform.io/docs/language/resources/provisioners/local-exec.html

You can set environment variables via

```
resource "aws_instance" "web" {
  # ...

  provisioner "local-exec" {
    command = "echo $FOO $BAR $BAZ >> env_vars.txt"

    environment = {
      FOO = "bar"
      BAR = 1
      BAZ = "true"
    }
  }
}
```

# Remote-exec
Allows you to execute commands on the target resource after a resource  is provisioned. It is unseful for provisioning a VM with a simple set of commands.

https://www.terraform.io/docs/language/resources/provisioners/remote-exec.html

You can use the field scripts to copy local scripts to the target resource and execute it there.

#File provisioners
Are used to copy files or directories from our local machine to the newly created resource.

https://www.terraform.io/docs/language/resources/provisioners/file.html

It may require a connection block within the provisioner for authentication.

# Connection provisioner

Most provisioners require access to the remote resource via SSH or WinRM, and expect a nested connection block with details about how to connect.

https://www.terraform.io/docs/language/resources/provisioners/connection.html

# Null Resources

A null_resource is a placeholder for resources that have no specific association to a provider resources. You can provide a connection and triggers to a resource. Triggers is a map of values which should cause this set of provisioners to re-run. Values are meant to be interpolated references to variables or attributes of other resources.

# Cheat Sheet:

```https://www.techbeatly.com/terraform-cheat-sheet/```

# Get a list of current providers (with modules)

```terraform providers```

# The terraform language

This is the syntax

```
<BLOCK TYPE> "<BLOCK LABEL>" "<BLOCK LABEL>" { 
  # Block body
  <IDENTIFIER> = <EXPRESSION> # Argument
}
```

Terraform language consists of blocks, expressions, and statements.

- Blocks are containers for other content and usually represent a resource or data source.
- Expressions are used to refer to or compute values. 
- Arguments are used to set the value of a parameter in a block.

# terraform configuration block

This is the first block in a terraform configuration file. It is used to configure the behavior of the terraform command-line interface and to define the required providers.

Example:
```
terraform {
  required_providers {
    aws = {
      source = "hashicorp/aws"
      version = ">= 3.0"
    }
  },
  experiments = [module_variable_optional_attrs]
  provider_meta "my-provider" {
    hello = "world"
  }
}
```

# HashiCorp Configuration Language (HCL)

HashiCorp Configuration Language is the language used to write terraform configuration files. It is designed to be easy to read and write and is used to define the structure of terraform configuration files.


# Input Variables
Input variables are used to parameterize terraform configurations. They allow you to define values that can be passed into a module or configuration at runtime.

Example:
```
variable "region" {
  description = "The AWS region to deploy resources in."
  type = string
  default = "us-west-2"
}
```

Example for a nested type:
```
variable "tags" {
  type = map(string)
  default = {
    Name = "my-instance"
    Environment = "dev"
  }
}

variable "docker_ports" {
  type = list(object({
    internal = number
    external = number
    protocol = string
  }))
  default = [
    {
      internal = 8300
      external = 8300
      protocol = "tcp"
    }
  ]
}
```

Example for a Validation block:
```
variable "region" {
  type = string
  validation {
    condition = length(var.region) > 0
    error_message = "Region must not be empty."
  }
}
```

# Variable Definition files

Files are named `*.tfvars`. They are used to define values for input variables.

Example:
```
region = "us-west-2"
var = [
  "value1", "value2"
]
```
It used the terraform language syntax.

# Variables via Environment Variables
Variables can be set via environment variables. The variable name must be prefixed with `TF_VAR_`.

Example:
In the Linux Shell:
```
export TF_VAR_region=us-west-2
```
How to access? Use `var.region`.

# Loading Input Variables from Files

Default autoloaded files are `terraform.tfvars`, `terraform.tfvars.json`, and `*.auto.tfvars`
Not autoloaded files can be loaded via the `-var-file` flag.

Example for such a command:
```
terraform apply -var-file="testing.tfvars"
```
Inline variables can be set via the `-var` flag.
Example:
```
terraform apply -var="region=us-west-2"
```
Environment variables can also be used via TF_VAR_*.

Order of precedence:
1. Environment variables
2. `terraform.tfvars` files
3. `terraform.tfvars.json` files
4. `*.auto.tfvars` and `*.auto.tfvars.json` files
5. -var and -var-file flags

# Output Variables
Output values are computed values after a terraform apply is performed.

- They can be used to expose information about the resources that were created.
- They can be used to obtain information after resource provisioning.
- output a file of values for programmatic use.
- cross-reference stacks via outputs in a state file via terraform_remote_state.

Example:
```
output "db_passqword" {
  value = aws_db_instance.mydb.password
  sensitive = true
  description = "The password for the database."
}
```
Sensitive outputs will still be visible within the statefile.

To print the output, use `terraform output`. To print a specific output, use `terraform output {output_name}`.
Use the `-json` flag to get the output in JSON format.

# Local Values
Local values are used to define intermediate values that can be used in multiple places within a module or configuration.

Example:
```
locals {
  region = "us-west-2"
  tags = {
    Name = "my-instance"
    Environment = "dev"
  }
}

locals {
  computed = concat(local.region, aws_instance.my_instance.id)
}
```
Access local values via `local.{local_name}`. Locals help to avoid code duplication and make the code more readable. It is best practice to use locals sparingly since Terraform is declarative and the overuse can make it difficult to determine what the code is doing.

# Data Sources
Data sources are used to fetch information from external sources and use it within a terraform configuration.

What is a data block?
- A data block is used to define a data source.

Example:
```
data "aws_ami" "example" {
  most_recent = true
  owners = ["self"]
  filter {
    name = "name"
    values = ["my-ami-*"]
  }
}
```
Access the data via `data.{data_source_name}.{attribute}`. You use filters to narrow down the data source.
Now, you can use the data source in a resource block. In this example, it is the most recent Amazon Machine Image (AMI) that is available.
Example
```
resource "aws_instance" "web" {
  ami = data.aws_ami.example.id
  instance_type = "t2.micro"
}
```

# References to Named Values (overview)
Named Values are built-in expressions that can be used to reference values in a configuration:
Resources : {resource_type}.{resource_name}.{attribute}
Data Sources : {data_source_type}.{data_source_name}.{attribute}
Modules : {module_name}.{output_name}
Local Values : local.{local_name}
Input Variables : var.{variable_name}
Output Variables : output.{output_name}

Filesystem and workspace info
path.module : The path to the module where the expression is evaluated.
path.root : The root directory of the configuration.
path.cwd : The current working directory where the terraform command is run.
terraform.workspace : The name of the workspace where the configuration is applied.
Block-local values
count.index : The index of the current resource or module instance.
each.key : The key of the current element in a map.
each.value : The value of the current element in a map.
self.{attribute} : The value of an attribute in the current resource or module block.

Named values resemble the attribute notation for map(object) values but are not objects and do not act as objects. You cannot use square brackets to access Named Values like an object.

# Types of input variables
Example
```
variable "region" {
  type = string
  default = "us-west-2"
}
```

4:11:54
