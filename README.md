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

``terraform graph | dot -Tsvg > grah.svg``

For this, install GraphViz. This is called the dependency graph. terraform walks this graph to generate plans, refresh state, and more.

# Install terraform

https://learn.hashicorp.com/tutorials/terraform/install-cli
Create a ``main.tf`` file. To connect to the correct (cloud) provider, copy and paste the code from "USE PROVIDER"

https://registry.terraform.io/browse/providers

into your ``main.tf`` file.

Also on https://registry.terraform.io/providers/hashicorp/google/latest/docs, you cann finde the documentation for many resources.

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
}
```
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
``terraform refresh`` refreshes the state file.
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

1:38:08