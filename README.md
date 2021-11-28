# terraform

https://www.youtube.com/watch?v=V4waklkBC38

- IAC Tool
- declarative
- cloud agnostic

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
44:02