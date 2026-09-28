# Terraform Revision Notes
**Level:** Beginner
**Topics Covered:** Introduction → Conditional Expressions

---

# 1. What is Terraform?

Terraform is an **Infrastructure as Code (IaC)** tool developed by HashiCorp.

It allows us to create, update, and destroy cloud resources using code instead of manually creating them from the cloud portal.

Example:

Instead of manually creating:

- VPC
- Subnet
- EC2
- Resource Group
- Virtual Network

We write Terraform code, and Terraform creates them automatically.

---

# 2. Infrastructure as Code (IaC)

Infrastructure is managed using code.

Instead of clicking in AWS or Azure Portal:

Portal
↓

VPC
↓

Subnet
↓

EC2

We write:

```hcl
resource "aws_vpc" "network" {
  ...
}
```

Benefits:

- Automation
- Repeatability
- Version Control
- Less Human Error
- Faster Deployments

---

# 3. Terraform Workflow

```
Write Code
     │
     ▼
terraform init
     │
     ▼
terraform fmt
     │
     ▼
terraform validate
     │
     ▼
terraform plan
     │
     ▼
terraform apply
     │
     ▼
Infrastructure Created
```

Destroy resources:

```bash
terraform destroy
```

---

# 4. Provider

A Provider tells Terraform which cloud platform to connect to.

AWS

```hcl
provider "aws" {
  region = var.region
}
```

Azure

```hcl
provider "azurerm" {
  features {}
}
```

---

# 5. Variables

Variables make Terraform code reusable.

Without Variable

```hcl
cidr_block = "10.0.0.0/16"
```

With Variable

```hcl
cidr_block = var.vpc_cidr
```

Actual value comes from:

```text
terraform.tfvars
```

Example

```hcl
vpc_cidr = "10.0.0.0/16"
```

---

# 6. terraform.tfvars

Stores actual values.

Example

```hcl
region = "ap-south-1"

vpc_cidr = "192.168.0.0/16"
```

variables.tf only declares variables.

terraform.tfvars provides values.

---

# 7. Resource

A Resource is an actual cloud object.

AWS Example

```hcl
resource "aws_vpc" "network" {}
```

Azure Example

```hcl
resource "azurerm_resource_group" "base" {}
```

Structure

```
resource "TYPE" "LOCAL_NAME"
```

Example

```
resource "aws_vpc" "network"
```

Type

```
aws_vpc
```

Local Name

```
network
```

---

# 8. Resource References

Syntax

```
RESOURCE_TYPE.LOCAL_NAME.ATTRIBUTE
```

Example

```hcl
aws_vpc.network.id
```

Breakdown

```
aws_vpc
↓

Resource Type

network
↓

Terraform Local Name

id
↓

Attribute
```

Azure Example

```hcl
azurerm_resource_group.base.name
```

Returns

```
ntier
```

---

# 9. Outputs

Outputs display values after apply.

Example

```hcl
output "vpc_id" {
  value = aws_vpc.network.id
}
```

Useful for

- IDs
- Names
- IPs
- URLs

---

# 10. count

Used to create multiple resources using one block.

Without count

```
Subnet1

Subnet2

Subnet3

Subnet4
```

With count

```hcl
count = 4
```

Terraform creates

```
Subnet[0]

Subnet[1]

Subnet[2]

Subnet[3]
```

---

# 11. count.index

Terraform starts counting from 0.

```
count = 4

↓

0

1

2

3
```

Example

```hcl
cidr_block = var.subnet_cidr[count.index]
```

Iteration

```
0 → 192.168.0.0/24

1 → 192.168.1.0/24

2 → 192.168.2.0/24

3 → 192.168.3.0/24
```

---

# 12. length()

Returns the number of elements in a list.

Example

```hcl
count = length(var.subnet_cidr)
```

If list has

```
6
```

Terraform creates

```
6 Resources
```

Advantage

No need to manually change count.

---

# 13. Splat Expression (*)

Syntax

```hcl
aws_subnet.subnets[*].id
```

Meaning

Get the IDs of ALL subnet resources.

Returns

```
Subnet1 ID

Subnet2 ID

Subnet3 ID
```

Example

```hcl
aws_subnet.subnets[*].tags["Name"]
```

Returns

```
web

app

db
```

---

# 14. Locals

Locals are internal variables.

Example

```hcl
locals {
  project = "pana"
}
```

Use

```hcl
local.project
```

Returns

```
pana
```

Difference

Variables

- Value comes from user

Locals

- Value is defined inside Terraform code

---

# 15. Conditional Expressions

Syntax

```hcl
condition ? true_value : false_value
```

Read as

```
If condition is TRUE

↓

Use first value

Else

↓

Use second value
```

Example

```hcl
var.environment == "prod" ? "Standard_D4s_v3" : "Standard_B2s"
```

If

```
environment = prod
```

Result

```
Standard_D4s_v3
```

If

```
environment = dev
```

Result

```
Standard_B2s
```

Important:

Terraform returns the selected value, NOT true or false.

---

# Common Interview Questions

### What is Terraform?

Terraform is an Infrastructure as Code (IaC) tool used to provision and manage infrastructure using code.

---

### What is a Provider?

A Provider is a plugin that enables Terraform to communicate with cloud platforms like AWS or Azure.

---

### What is a Resource?

A Resource represents a cloud object such as a VPC, EC2 instance, Resource Group, or Virtual Network.

---

### What is count?

count is a meta-argument used to create multiple instances of the same resource.

---

### What is count.index?

count.index is the current index of the resource being created. It starts from 0.

---

### What does length() do?

length() returns the number of elements in a list or string.

---

### What is a Local?

A Local is an internal variable defined inside Terraform configuration for reuse.

---

### What is a Conditional Expression?

A Conditional Expression chooses between two values based on a condition.

Syntax

```
condition ? true_value : false_value
```

---

# Quick Revision

✅ Provider → Connect Terraform to Cloud

✅ Variable → Input values

✅ terraform.tfvars → Actual values

✅ Resource → Cloud object

✅ Resource Reference → Access resource attributes

✅ Output → Display values after apply

✅ count → Create multiple resources

✅ count.index → Current resource number

✅ length() → Count list elements

✅ [*] → Get attribute from all resources

✅ Locals → Internal variables

✅ Conditional Expression → Decision making in Terraform

---

# Golden Formula

```
terraform.tfvars
        │
        ▼
Variables
        │
        ▼
Resources
        │
        ▼
Count
        │
        ▼
count.index
        │
        ▼
Functions
(length)
        │
        ▼
Locals
        │
        ▼
Conditional Expressions
        │
        ▼
Outputs
```
