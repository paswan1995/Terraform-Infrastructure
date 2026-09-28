***05/Jan/2025***

# Terraform

***Topic: Terraform locals***

* [offical docs](https://developer.hashicorp.com/terraform/language/values/locals)
    * Official Definition
        * What are Locals?
            * `Locals are variables that exist only inside your Terraform code.`
            * `Think of them as temporary variables that help you avoid repeating the same values multiple times.`
        
        * Real-Life Example
            * Imagine you're writing your address 20 times.
            * Without locals:
```
Company:
OpenAI

Office:
OpenAI

Invoice:
OpenAI

ID Card:
OpenAI
```

   * You are repeating the same value again and again.
   * Instead, define it once:
     * `company = OpenAI` 
   
   * Then use:
```
Company = company
Office = company
Invoice = company
ID Card = company
```

* This is exactly what locals do.

***Syntax***

```
locals {
  local_name = value
}
```

***Example:***

```
locals {
  company = pana
}
```

* Now anywhere in Terraform: `local.company`
* returns: `pana`
---

* Think of It Like Variables
    * Example:
```
variable "region" {

}
```
* Used as: `var.resion` 
---

* Locals are similar.
    * Example: 

```
locals {
  environment= "dev"
}
```

* Used as: `local.environment`

* Notice the difference 

| Variables | Locals |
|-----------|--------|
| `var.region` | `local.environment` |

* Variables vs Locals
    * This is the most important difference.

* Variables
    * `Variables receive values from outside Terraform.`

* Example:
    * `terraform apply -var-file="terraform.tfvars"`

* Terraform reads:
    * `region = eastus`
    * from your `terraform.tfvars`

---

# Locals

* Locals are created inside Terraform.
    * Example:
```
locals {
  company = "ccb"
}
```

* No one passes this value.
* Terraform creates it internally.

# Visual Difference

```
User
   │
   ▼
terraform.tfvars
   │
   ▼
Variables
(var.region)


Terraform
creates
itself
   │
   ▼
locals {
 company = "ccb"
}
```


# Example 1 (Without Locals)

* Suppose you write:

```
resource "azurerm_resource_group" "rg" {
  name = "pana-dev" 

}

resource "azurerm_virtual_network" "vnet" {
  name = "pana-dev-network"
}

resource "azurerm_storage_accounts" "sa" {
  name = "panadevstorage"
}
```

* See how many times you repeat: `pana`

# Example 2 (With Locals)

```
locals {
  project = "pana"
}
```

* Now 

```
resource "azurerm_resource_group" "rg" {
  
  name = "${local.project}-dev"
}

resource "resource_virtual_network" "vnet" {

  name = "${local.projet}-network"


}

resource "resource_storage_account" "sa" {
  
  name = "${local.project}-storgae"
}
```

* Now if the project changes from `pana` to `ccb`

* You only change one line: 

```
locals {
    project = "ccb" 
}
```

* Everything updates automatically.

# Another Example

```
locals {
  owner = "Anil"
}

```

* Now:

```
tags {
  owner = local.owner
}
```

* Terraform becomes: `Owner = Anil`
---

# Why Do DevOps Engineers Use Locals?

* Suppose you have 100 resources.
* Every resource needs:

```
Environment = dev
Owner = Anil
Project = Pana
```

* Without locals:

```
tags = {

  Environment = "dev"

  Owner = "Anil"

  Project = "Pana"

}
```

* repeated 100 times.

* Instead:

```
locals {

  environment = "dev"

  Owner = "Anil"

  Project = "Pana"

}

```

* Now:

```
tags = {

  Environment = local.environment

  Owner = local.owner 

  Project = local.project
}
```

* Much cleaner.

---

# Variables vs Locals (Interview Question)

| Variables | Locals |
|-----------|--------|
| Receive values from users or `terraform.tfvars` | Defined inside Terraform |
| Can change for different environments | Used for internal reusable values |
| Access with `var.name` | Access with `local.name` |

# When Should You Use Locals?

* You are repeating the same value in many places.
* You want cleaner code.
* You want to build names dynamically.
* You want to avoid hardcoding values multiple times.

---

# Example from Azure

* Suppose your Resource Group name is: `ntier`

* Instead of writing: `ntier-network`

* you can write:

```
locals {
  
  suffix = "network"

}
```

* Then: `name = "${var.resource_group.name}-${local.suffix}"`
  
* Terraform produces: `ntier-network`

---

# Key Points for Your Notes

* Terraform Locals

```

• Locals are internal variables used only inside Terraform.

• They help avoid repeating the same values multiple times.

• Locals cannot be passed from terraform.tfvars.

• Variables are accessed using:   var.variable_name

• Locals are accessed using:      local.local_name

• Use locals for:

  - Reusable values
  - Naming conventions
  - Tags
  - Prefixes
  - Suffixes
  - Calculated values

Syntax:

locals {

  project = "Pana"
}


Usage: local.project 
 
```


# Conditional Expressions
  * [Offical docs](https://developer.hashicorp.com/terraform/language/expressions/conditionals)

* What is a Conditional Expression?
  * `A conditional expression allows Terraform to make a decision.`
  
* Instead of always using one value, Terraform asks:
  * `If this condition is true, use one value. Otherwise, use another value.`

# Real-Life Example

* Suppose you're going to the office.
  * Decision:

```
If it is ranning
  carry an umbrella 

Else 
  Don't carry an umbrella
```
* This is a conditional expression.

*** Another Example***

```
If salary > 50,000
  Buy Iphone

Else 
  Buy Andriod
```

* Terraform works exactly the same way.


***Syntax***

`condition ? true_value : false_value`

* Read it as:

```
If condition is True
  use true_vale

Else 
  use false_value
```

***Example 1***

```
variable "environment" {

  default = "dev" 
}

locals {

  vm_size = var.environment == "prod" ? "standard_D4s_v3" : "standard_B2s"

}
```
* Terraform asks:
* `Is environment == prod?`
* If yes:
  * `Standard_D4s_v3`

* If no:
  *  `Standard_B2s`

* Visual Flow

```
            Is environment = prod?

                      │
             ┌────────┴────────┐
             │                 │
            YES               NO
             │                 │
             ▼                 ▼
 Standard_D4s_v3      Standard_B2s

```

***Example 2***

* Suppose:

```
variable "create_vm" {

  default = true 
}
```

* Then 

```
locals {

  message = var.create_vm ? "VM will be created" : "VM will not created"

}
```

* If 

```
created_vm = true
```

* Output

```
Vm will be created
```

* If
```
create_vm = false
```

* Output

```
VM will not be created
```

# Example 3 (Azure)

* suppose:

```
variable "environment" {
  default = "dev"

}

```
* Then 

```
locals {
  resource_group_name = var.environment == "prod" ? "prod-rg" : "dev-rg"
}
```

* If 

```
environment = prod
```

* Result

```
prod-rg
```

* Otherwise

```
dev-rg
```

# Example 4 (AWS)

```
variable "environment" {

  default = "dev"

}
```

```
resource "aws_instance" "server" {

  instance_type = var.environment == "prod" ? "t3.large" : "t2.micro" 

}
```
* Terraform decides:

```
Production

↓

t3.large
```

```
Development

↓

t2.micro
```


# Breaking the Syntax

* Example:

```
var.environment == "prod" ? "t3.large" : "t2.micro"
```

* Break it into parts:

| Part | Meaning |
|-------|---------|
| `var.environment == "prod"` | Condition |
| `?` | If true |
| `"t3.large"` | True value |
| `:` | Else |
| `"t2.micro"` | False value |


# Comparison Operators

* Conditional expressions usually use comparison operators.

| Operator | Meaning |
|----------|---------|
| `==` | Equal |
| `!=` | Not Equal |
| `>` | Greater Than |
| `<` | Less Than |
| `>=` | Greater Than or Equal |
| `<=` | Less Than or Equal |


*  Example

* `var.environment == "prod"`

* asks: 

* `Is environment equal to prod`
---



# Variables vs Conditional

* Without conditional

    * `instance_type = "t2.micro"`

* Always creates
    * `t2.micro`

* With conditional
    * `instance_type = var.environment == "prod" ? "t3.large" : "t2.micro"`

* Terraform decides automatically.

---

# Real DevOps Use Cases

* Conditional expressions are commonly used for:

  * Choosing VM size
  * Choosing AWS EC2 instance type
  * Naming resources
  * Creating production vs development resources
  * Tags
  * Storage account SKU
  * Load Balancer SKU

* Interview Question

  * What is a conditional expression? 
    * `A conditional expression is a Terraform feature that allow you to choose between two valuse based on the condition.`
    * syntax
      * `condition ? true_value : false_value`
---

***Notes for Your DevOps Notebook***
```sh
# Terraform Conditional Expressions

## Definition

- Conditional expressions allow Terraform to make decisions based on a condition.

- Syntax:
  - condition ? value_true : value_false

- Read as:

If condition is TRUE
    Use true_value

Else
    Use false_value

# Example:

- variable "environment" {
  default = "dev"

}

locals {

  vm_size = var.environment == "prod" ? "standard_D4s_v3" : "Standard_B2s" 
}

# Result:

- environment = prod
→ Standard_D4s_v3

- environment = dev
→ Standard_B2s

# Comparison Operators

== Equal 
!= Not Equal
> Greater Than
>= grater Than or Equal
< Less Than
<= Less Than or Equal 


# Real-world Uses

- VM Size

- EC2 Instance Type

- Resource Names

- Tags

- Production vs Development Deployments

# 

Think like this:

Is environment equal to "prod"?

        |
   ┌────┴────┐
   │         │
  YES       NO
   │         │
   ▼         ▼
Standard   Standard_B2s
_D4s_v3     


# Example 2

Suppose:

variable "age" {
  default = 20 
}

Now 

locals {
  status = var.age >= 18 ? "Adult" : "Minor" 
}

- Terraform asks:
    - Is age >= 18?

- If age = 20

20 >= 18

TRUE

↓

Adult

- If age = 15

15 >= 18

FALSE

↓

Minor

```
---

# Real DevOps Example

* Imagine your company has two environments:

***Development***

```
Environment: dev

Users: 5

Traffic: Low
```

* A small VM is enough:
  * `Standard_B2s`

***Production***

```
Environment: prod

Users: 20,000

Traffic: High

```

* Now you need a larger VM:

  * `Standard_D4s_v3`

* Instead of maintaining two different Terraform files, you can use one file with a conditional expression.


* Why do we use Conditional Expressions?
  * `Conditional expressions allow Terraform to choose between two values based on a condition. They help us write reusable code for different environments like development, testing, and production without duplicating configuration.`

---

***Look at this code***

```sh
variable "backup_enable" {

  default = true 
}

locals {
  backup_policy = "var.backup_enable" ? "Daily_backup" : "No_backup" 
}

# Forget Terraform for a minute. Let's focus on only this part:

- var.backup_enabled ? "Daily Backup" : "No Backup"

- Read it like this:

- If backup_enabled is TRUE → use "Daily Backup"
Otherwise → use "No Backup"

# This is exactly the same as writing:

IF backup_enabled == true
    Daily Backup
ELSE
    No Backup

# Step 1: What is the value?
  - We have 
    - backup_enabled = true

  - So Terraform asks:
    - Is backup_enabled TRUE?

- Answer: yes

# Step 2: Which side does Terraform choose?

* The syntax is:
  - condition ? true_vale : false_value

* Our code is:
  - var.backup_enable ? "Daily Backup" : "No Backup" 

# Break it apart:

Condition
↓
var.backup_enabled

If TRUE
↓
"Daily Backup"

If FALSE
↓
"No Backup"


```

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
resource "aws_vpc" "network" {
```

Azure Example

```hcl
resource "azurerm_resource_group" "base" {
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
***[Conditional Expression](https://developer.hashicorp.com/terraform/language/expressions/conditionals)***


# Activity 3: Complete AWS Network


* Final Architecture
```sh
                    Internet
                        │
                        │
                 Internet Gateway
                        │
                        │
                  Public Route Table
                        │
          ┌─────────────┴─────────────┐
          │                           │
          ▼                           ▼
    Public Subnet 1             Public Subnet 2
    172.16.0.0/24              172.16.1.0/24

===================================================

                 Private Route Table
                        │
          ┌─────────────┴─────────────┐
          │                           │
          ▼                           ▼
    Private Subnet 1            Private Subnet 2
    172.16.2.0/24              172.16.3.0/24

===================================================

                Entire Network

               VPC
          172.16.0.0/16
```

* What are we creating?

| Resource | Count | Why? |
|----------|------:|------|
| VPC | 1 | Private Network |
| Internet Gateway | 1 | Internet Access |
| Public Route Table | 1 | Route internet traffic |
| Private Route Table | 1 | Internal traffic only |
| Public Subnets | 2 | Web Servers |
| Private Subnets | 2 | App / DB Servers |

* ***Step 1 — Create VPC***

* What is a VPC?
  * A VPC (Virtual Private Cloud) is your own private network inside AWS.
  * Think of it as buying land before building a house.
  * Without land,
  * you cannot build:
    * EC2
    * Database
    * Load Balancer
    * Subnets
  * Everything must exist inside a VPC.

  * Real World Example
  * Imagine you are building a society.
```sh
Society
│
├── Building A
├── Building B
├── Garden
└── Parking
```
* AWS is exactly the same.

```sh
VPC
│
├── Subnet
├── EC2
├── Database
└── Load Balancer
```

***AWS Portal***

* Create VPC

![Preview](images/tf30.png)
![Preview](images/tf31.png)

* Step 2 — Create Internet Gateway
  * What is an Internet Gateway?
    * Without an Internet Gateway,
    * your VPC cannot communicate with the Internet.
        * Think of it as the main gate of your society.
```sh
Road

↓

Main Gate

↓

Society
```
* AWS

```sh
Internet

↓

Internet Gateway

↓

VPC
```

* Without this gate, nothing can enter or leave.

* AWS Portal
![Preview](images/tf32.png)
![Preview](images/tf33.png)
* After cretaed IGW 
  * we have to attached VPC to Internet Gateway
    * Click -> Actions --> Attach to VPC --> Choose --> ntier-vpc --> Click --> Attach

![Preview](images/tf34.png)


* Now Connection is ready.

```sh
Internet

↓

IGW

↓

VPC
```
* What have we actually done?
  * Most beginners just click buttons without understanding why. Let's understand it.
  * Imagine you build a new apartment.
    * `Your Apartment`
    * Can people from outside come inside?
      * `No.`
    * You need a main gate.

```
Road
   │
   ▼
Main Gate
   │
   ▼
Apartment
```
* AWS is exactly the same.

```
Internet
      │
      ▼
Internet Gateway
      │
      ▼
VPC
```

* The Internet Gateway (IGW) is the main gate of your VPC.
* Without it,
  * Nobody can enter.
  * Nobody can leave.

* Does creating an Internet Gateway mean the Internet is working?
    * `No`

* Right now your architecture is:
```sh
Internet
      │
      ▼
Internet Gateway
      │
      ▼
      VPC
```
* That's all.
* There is no path from the VPC to the Internet yet.
* Think about this:
* You built a society.
```
Road
   │
Main Gate
   │
Society
```
* But inside the society, there is no road leading to the main gate.
* Can a car reach the gate? `No.`
* Exactly the same thing happens in AWS.

***So what is missing?***
  * We need to tell AWS:
    * "If someone wants to go to the Internet, which path should they take?"
        * `That path is called a Route.`
        * `Routes are stored inside a Route Table.`

***_Step 3 — Route Tables_***

  * Before creating them, understand what a Route Table is.

***What is a Route Table?***
  * A Route Table is like Google Maps.
  * Imagine you are driving.
  * You ask: `How do I reach Mumbai?`
  * Google Maps replies:
```
Take Highway 44

↓

Take Expressway

↓

Reach Mumbai
```
* Google Maps gives you the route.
* AWS works the same way.
* Whenever a server wants to send traffic, AWS asks:
    * `Where should I send this packet?`
    * `The Route Table answers.`

* Without Route Table
```
EC2

↓

I want Internet

↓

???

↓

No idea

↓

Packet dropped
```


* With Route Table

```
EC2

↓

I want Internet

↓

Route Table

↓

Go to Internet Gateway

↓

Internet
```
***Why do we need TWO Route Tables?***

* Look at your tutor's diagram.
```
          Public Subnets

      Web Server

      Web Server


-------------------------------

         Private Subnets

      App Server

      Database
```

* Should the Database be directly accessible from the Internet? `No`
* Should the Application Server be directly accessible? `No`
* Only the Web Servers should receive Internet traffic.
* That's why we create:
```
Public Route Table

↓

Internet Allowed
```

* and 
```
Private Route Table

↓

Internet NOT Allowed
```

* Final Architecture

```
                   Internet
                       │
                       ▼
                Internet Gateway
                       │
          ┌────────────┴────────────┐
          │                         │
          ▼                         ▼
 Public Route Table        Private Route Table
          │                         │
     Public Subnets          Private Subnets
```

***Step 4 — Create the Public Route Table***

![Preview](images/tf35.png)

***Step 5 — Create the Private Route Table***

![Preview](images/tf36.png)

* Current Architecture

```
                    Internet
                        │
                        │
                 Internet Gateway
                        │
                        │
                 +----------------+
                 |    ntier-vpc   |
                 | 172.16.0.0/16  |
                 +----------------+
                  │             │
                  │             │
                  ▼             ▼
        Public Route      Private Route
            Table             Table
```
* Notice something...
* There are no subnets yet.
* There are no routes yet.
* There are no EC2 instances yet.
* So the route tables are currently empty containers waiting for routing rules.

***What is a Route?***

* This is one of the most important networking concepts.
* Suppose you live in Nagpur.
* You want to go to Mumbai.
* How do you know which road to take?
* You use Google Maps.
```
Nagpur

↓

Google Maps

↓

Mumbai
```

* Google Maps tells you

```
Take Highway 53

↓

Take Expressway

↓

Reach Mumbai
```

* AWS works exactly the same.
---

* Suppose an EC2 instance wants to communicate.
```
EC2

↓

Where should I send this packet?
```

* AWS checks
  * Route Table
    * The Route Table answers
```
Use Internet Gateway

or

Stay inside VPC

or

Use NAT Gateway

or

Use VPN
```

* Think of Route Table as Google Maps

```
Packet

↓

Route Table

↓

Destination Found

↓

Send Packet
```

* Without Route Table

```
Packet

↓

???

↓

Dropped
```

* Why do we need TWO Route Tables?
  * Your tutor's architecture
```
Internet

↓

Web Server

↓

Application Server

↓

Database
```

* Should Web Server have Internet? `Yes`
  * Because users access the website.
    * Therefore
```
Public Route Table

↓

Internet Access
```
```
Private Route Table

↓

No Internet Access
```
---

***Public Route Table***

* Imagine
```
Internet

↓

Web Server
```
* Public Route Table contains

```
Destination

0.0.0.0/0

↓

Internet Gateway
```

* Meaning
```
Any Internet traffic

↓

Internet Gateway
```

***Private Route Table***

  * Private Route Table contains
  * No Internet route.
  * Meaning
```
Database

↓

Internet

❌ Not Allowed
```

* So what is missing now?
* Right now your Public Route Table is empty.
* Let's verify that.

* `0.0.0.0/0` means It allows communication with any IP address.
    * Think of a Route Table like Google Maps
    * Imagine you're in Nagpur.
    * You ask Google Maps:
      * "How do I reach Mumbai?"
    * Google Maps doesn't decide whether you're allowed to go.
      * It only tells you which road to take.
      * A Route Table works exactly the same way.

* What does 0.0.0.0/0 actually mean?
    * Any IPv4 destination in the world.
    * The /0 means match all IPv4 addresses.
      * Examples:
        * `8.8.8.8        (Google DNS)`
        * `1.1.1.1        (Cloudflare)`
        * `142.250.x.x    (Google)`
        * `104.x.x.x      (GitHub)`
        * `20.x.x.x       (Microsoft Azure)`
    
    * All of these match:
        * `0.0.0.0/0`
        * `Because /0 means everything.`

* Then what does this route mean?
    * Suppose your Route Table contains:

| Destination | Target |
|-------------|--------|
| 0.0.0.0/0 | Internet Gateway |

   * This means: `If the destination is anywhere outside my VPC, send the packet to the Internet Gateway.`

   * Notice the wording:
      * It says send. It does not say allow.

***Why do we need BOTH routes?***

  * Every VPC has this automatically:

| Destination | Target | Purpose |
|-------------|--------|---------|
| `172.16.0.0/16` | Local | Communication inside the VPC |

  * We manually add:

| Destination | Target | Purpose |
|-------------|--------|---------|
| `0.0.0.0/0` | Internet Gateway | Communication with the Internet |

---

* Now that you understand 0.0.0.0/0, go ahead and:
1. Open ntier-public-rt
2. Go to the Routes tab
3. Click Edit routes
4. Add route-
    - `Destination: 0.0.0.0/0`
    - `Target: Internet Gateway`
    - `- Select ntier-igw`
5. Save the changes.

![Preview](images/tf37.png)
![Preview](images/tf38.png)

* Your architecture is currently:

```
                    Internet
                        │
                        ▼
               Internet Gateway
                        │
                        ▼
              Public Route Table
          0.0.0.0/0 → Internet
```

* There are no subnets connected to this route table.
* It's like building a highway but not connecting any villages to it.

* Final Architecture

```
                    Internet
                        │
                        ▼
                Internet Gateway
                        │
                        ▼
              Public Route Table
             0.0.0.0/0 → IGW
                    │
        ┌───────────┴───────────┐
        │                       │
        ▼                       ▼
 Public Subnet 1         Public Subnet 2
 ```

 * Now only these subnets can use the Internet.
  * What about Private Subnets?
```
Private Route Table

↓

Local only
```
* No Internet route.

***Now let's create the subnets***

* According to your tutor's diagram, we'll create 4 subnets:

| Subnet | Type | CIDR | AZ |
|---------|------|------|----|
| public-1 | Public | 172.16.0.0/24 | ap-south-1a |
| public-2 | Public | 172.16.1.0/24 | ap-south-1b |
| private-1 | Private | 172.16.2.0/24 | ap-south-1a |
| private-2 | Private | 172.16.3.0/24 | ap-south-1b |

---

***Step 6 — Create Public Subnet***

* Why are we putting public-1 and private-1 in the same Availability Zone (us-east-1a)?
* Think of one Availability Zone as one building.
```
us-east-1a

├── Public Subnet
│      └── Web Server
│
└── Private Subnet
       └── App Server
```

* The Web Server can communicate with the App Server within the same AZ, which can reduce latency.

* Then we repeat the same pattern in another Availability Zone for high availability.

```
us-east-1b

├── Public Subnet
│      └── Web Server
│
└── Private Subnet
       └── App Server
```

* If `us-east-1a` goes down, the infrastructure in `us-east-1b` can continue serving traffic.

***How AWS Companies Actually Build It***

* A common production setup looks like this:

```
                    Internet
                        │
                Internet Gateway
                        │
        ┌───────────────┴───────────────┐
        │                               │
     us-east-1a                     us-east-1b
        │                               │
   Public Subnet                  Public Subnet
        │                               │
      EC2 Web                       EC2 Web
        │                               │
        └───────────────┬───────────────┘
                        │
                 Load Balancer
                        │
        ┌───────────────┴───────────────┐
        │                               │
    Private Subnet                 Private Subnet
        │                               │
     App Server                     App Server
        │                               │
        └───────────────┬───────────────┘
                        │
                    Database
```

* Why two public subnets? → To keep the web tier available if one AZ fails.
* Why two private subnets? → To keep the application tier available if one AZ fails.
* Why Internet Gateway? → To allow internet connectivity for public resources.
* Why separate route tables? → So public and private subnets can have different routing behavior.

* Understanding the reason behind each component is what separates someone who has memorized AWS from someone who can design and troubleshoot real cloud infrastructure.

![Preview](images/tf39.png)

* Your Network Architecture

```
                     Internet
                         │
                         │
                  Internet Gateway
                         │
                         │
                 Public Route Table
             0.0.0.0/0 → Internet Gateway
                         │
          ┌──────────────┴──────────────┐
          │                             │
     public-1                     public-2
   172.16.0.0/24               172.16.1.0/24
      us-east-1a                 us-east-1b


====================================================
            Internal VPC Communication
====================================================


          ┌──────────────┴──────────────┐
          │                             │
     private-1                    private-2
   172.16.2.0/24               172.16.3.0/24
      us-east-1a                 us-east-1b
          │                             │
          └──────────────┬──────────────┘
                         │
                 Private Route Table
               172.16.0.0/16 → local
```
![Preview](images/tf40.png)

* Why did we create 2 Public and 2 Private subnets?

  * Suppose your application has:
    - Frontend
    - Backend
    - Database

* A common production architecture is:

```
Internet
      │
Load Balancer
      │
──────────────────────────────
Public Subnets
──────────────────────────────

Frontend Server 1 (AZ-A)

Frontend Server 2 (AZ-B)

──────────────────────────────
Private Subnets
──────────────────────────────

Application Server 1 (AZ-A)

Application Server 2 (AZ-B)

Database Server 1 (AZ-A)

Database Server 2 (AZ-B)
```
* Why?
  * If AZ-A goes down:
    - Frontend 2
    - Application 2
    - Database 2

  * continue running in AZ-B.
  * This is called High Availability (HA).

---

* In real AWS production environments, we solve this by adding a NAT Gateway.

```
Internet
     │
Internet Gateway
     │
Public Subnet
     │
NAT Gateway
     │
Private Route Table
     │
Private Subnets
```

* This allows:
  - Private servers to access the Internet outbound (for updates, package downloads, APIs).
  - The Internet cannot initiate connections to the private servers. 

---
* we now manually built the same type of network that is commonly used in production AWS environments:
- ✅ 1 VPC
- ✅ 1 Internet Gateway
- ✅ 2 Availability Zones
- ✅ 2 Public Subnets
- ✅ 2 Private Subnets
- ✅ 2 Route Tables
- ✅ Public routing to the Internet
- ✅ Private routing within the VPC
- ✅ Proper subnet-to-route-table associations
- ✅ High Availability across two AZs

* This manual setup will make the Terraform implementation much easier to understand because you'll know exactly what each Terraform resource is creating behind the scenes.

***Complete AWS Network***

* One Route Table can be associated with many subnets.
  * Example:
```
                   Public Route Table
              +--------------------------+
              | 10.0.0.0/16 → local      |
              | 0.0.0.0/0 → IGW          |
              +--------------------------+
                    ▲      ▲      ▲
                    │      │      │
              Public1  Public2  Public3
```

* All three public subnets use the same Route Table.

* Example with 100 Public Subnets
```
Public Route Table
        ▲
        │
 ┌──────┼──────────────────────────┐
 │      │      │      │      │      │
 ▼      ▼      ▼      ▼      ▼      ▼
S1     S2     S3     S4    ...    S100
```
* Every subnet follows the same routing rules.
* AWS allows this.

* Then why do we create multiple Route Tables?
  * Because different subnets need different rules.
  * For example:

***Public Subnets***
* Need Internet.

```
Destination      Target
------------------------------
0.0.0.0/0        Internet Gateway
```

***Private Subnets***
  * Should not have direct Internet access.
* They might have:
```
Destination      Target
------------------------------
0.0.0.0/0        NAT Gateway
```
* or sometimes no Internet route at all.

* So normally we create:
```
VPC
│
├── Public Route Table
│      ▲
│      │
│  Public-1
│  Public-2
│  Public-3
│
└── Private Route Table
       ▲
       │
   Private-1
   Private-2
   Private-3
```

* Notice something:
- One Public Route Table is shared by all Public Subnets.
- One Private Route Table is shared by all Private Subnets.

* Why do we create an Internet Gateway?
  * `An Internet Gateway connects our VPC to the Internet. Without an Internet Gateway, resources inside the VPC cannot communicate with the Internet.`
  * Think of it like this:
```
My House (VPC)

↓

Main Gate (Internet Gateway)

↓

Road (Internet)
```

* Why do we create a Route Table?
  * A Route Table works exactly like Google Maps.
  * It tells AWS: `Where should I send this network traffic?`
  * For example:
```
Destination

10.0.0.0/16

↓

Stay inside VPC
```
* Another rule:
```
Destination

0.0.0.0/0

↓

Internet Gateway
```
* So the Route Table is simply a list of routing rules.
* `A Route Table contains routing rules that tell AWS where network traffic should go.`
* Examples:
    - Stay inside VPC
    - Go to Internet Gateway
    - Go to NAT Gateway
    - Go to VPN

* Let's revise everything you've learned so far.

| Resource | Purpose |
|----------|---------|
| `aws_vpc` | Creates a VPC |
| `aws_internet_gateway` | Connects the VPC to the Internet |
| `aws_route_table` | Stores routing rules |
| `aws_route` | Adds a route (for example, `0.0.0.0/0 → IGW`) |
| `aws_subnet` | Creates a subnet inside the VPC |
| `aws_route_table_association` | Connects a subnet to a Route Table |

* Public vs Private Subnet

| Public Subnet | Private Subnet |
|---------------|----------------|
| Web Server | Database |
| Load Balancer | Redis |
| Bastion Host | Internal APIs |
| NAT Gateway | Backend Services |

* What Makes a Subnet Private?
  * `"A Private Subnet means map_public_ip_on_launch = false."`
  
***resource vs data***

| Use | When? |
|------|--------|
| `resource` | Create a new AWS resource |
| `data` | Read an existing AWS resource |

* So What Does NAT Gateway Do?
  * Visual Flow 

```
Private EC2
10.0.10.20
      │
      ▼
NAT Gateway
      │
Changes Source Address
      │
10.0.10.20
      ▼
3.110.55.25 (Elastic IP)
      │
      ▼
Internet
```
* This process is called Network Address Translation (NAT).

* That's where the name NAT Gateway comes from.

* Why does a NAT Gateway require an Elastic IP?
  * A NAT Gateway communicates with the public internet on behalf of instances in private subnets. Since private IP addresses are not routable on the internet, the NAT Gateway needs a public IP. AWS uses an Elastic IP so the NAT Gateway has a static public address for outbound communication.

* Why don't we place the NAT Gateway inside a Private Subnet?
  * Because the NAT Gateway itself must be able to reach the Internet Gateway.
  * So AWS requires the NAT Gateway to be placed in a Public Subnet.

* Architecture
```
                        Internet
                            │
                            ▼
                    Internet Gateway
                            │
                            ▼
                     Public Subnet
                            │
                      NAT Gateway
                            ▲
                            │
                 Private Route Table
                            ▲
                            │
                      Private Subnet

```

* If the NAT Gateway were inside a private subnet, it wouldn't have a route to the Internet Gateway, so it couldn't forward traffic to the internet.


1. Elastic IP
= `aws_eip.nat` 
  * = Gives the NAT Gateway a permanent Public IP.

2. NAT Gateway
= `aws_nat_gateway.nat`
  * =  Allows Private EC2 instances to access the internet.

3. Private Route
= `aws_route.private_nat` 
```
destination_cidr_block = "0.0.0.0/0"
nat_gateway_id         = (known after apply)


```
* This means:
```
If destination is Internet

↓

Send traffic to NAT Gateway
```

****Final Architecture***

```
                           Internet
                               ▲
                               │
                        Internet Gateway
                               ▲
                               │
                   Public Route Table
                 0.0.0.0/0 → Internet Gateway
                               ▲
        ┌──────────────────────┼──────────────────────┐
        │                      │                      │
        ▼                      ▼                      ▼
    Public-1               Public-2             Public-3
        │
        │
        ▼
   NAT Gateway
   Elastic IP
        ▲
        │
        │
      Private Route Table
      0.0.0.0/0 → NAT Gateway
        ▲
   ┌────┼────┐
   ▼    ▼    ▼
Private-1
Private-2
Private-3
```

# ec2 
```sh


# =============================================================================
# Create AWS VPC
# This resource creates a new Virtual Private Cloud (VPC) in AWS.
# CIDR: 10.0.0.0/16
# Name: ntier
# =============================================================================

resource "aws_vpc" "base" {

  # CIDR block of the VPC
  cidr_block = "10.0.0.0/16"

  # Tags help identify resources in AWS Console
  tags = {
    Name = "ntier"
  }

}

# This Internet Gateway connects the VPC to the Internet.

resource "aws_internet_gateway" "igw" {
  # Attach an internet getway to thge VPC
  # aws_vpc.base.id means:
  # "Get the ID of the VPC whose Terraform local name is 'base'."

  vpc_id = aws_vpc.base.id

  # Tags help identify the Internet Gateway in AWS Console.

  tags = {

    Name = "ntier-igw"
  }
}

# Create Public Route Table
# This Route Table will be used by public subnets.


resource "aws_route_table" "public" {
  # Associate this Route Table with the VPC
  vpc_id = aws_vpc.base.id

  # Name shown in AWS Console

  tags = {
    Name = "ntier-public-rt"
  }
}

# Create Public Route
# This route sends all Internet traffic (0.0.0.0/0)
# to the Internet Gateway.

resource "aws_route" "public_internet" {
  # Attach this route to the Public Route Table
  route_table_id = aws_route_table.public.id

  # Any destination outside the VPC
  destination_cidr_block = "0.0.0.0/0"

  # Send the traffic to the Internet Gateway

  gateway_id = aws_internet_gateway.igw.id



}

# Create Public Subnet 1
# This subnet will be used to launch public EC2 instances.

resource "aws_subnet" "public_1" {

  # The subnet belongs to this VPC
  vpc_id = aws_vpc.base.id

  # CIDR block for this subnet

  cidr_block = "10.0.0.0/24"

  # Availability Zone

  availability_zone = "us-east-1a"

  # Automatically assign Public IP to EC2 instances
  map_public_ip_on_launch = true

  # Tags 

  tags = {
    Name = "public-1"
  }
}



# Create aws route_table_association for public subnet 1
# Creates a relationship (association) between a subnet and a route table.

resource "aws_route_table_association" "public_1" {
  # Associate the Public Route Table with this subnet

  subnet_id = aws_subnet.public_1.id

  route_table_id = aws_route_table.public.id
}

# public subnet 2

resource "aws_subnet" "public_2" {

  # The subnet belongs to this VPC

  vpc_id = aws_vpc.base.id

  availability_zone = "us-east-1b"

  map_public_ip_on_launch = true

  # CIDR block for this subnet

  cidr_block = "10.0.1.0/24"

  tags = {
    Name = "public-2"
  }
}

# Create aws route_table_association for public subnet 2

resource "aws_route_table_association" "public_2" {
  # Associate the Public Route Table with this subnet

  subnet_id = aws_subnet.public_2.id

  route_table_id = aws_route_table.public.id

}


# public subnet 3

resource "aws_subnet" "public_3" {

  # this subnet belongs to this VPC 
  vpc_id = aws_vpc.base.id

  cidr_block = "10.0.2.0/24"

  availability_zone = "us-east-1c"

  map_public_ip_on_launch = true

  tags = {
    Name = "public-3"
  }
}


# Create aws route_table_association for public subnet 3

resource "aws_route_table_association" "public_3" {
  # Associate the Public Route Table with this subnet

  subnet_id = aws_subnet.public_3.id

  route_table_id = aws_route_table.public.id

}

# Create Private Subnet 1
# This subnet will be used to launch private EC2 instances.

resource "aws_subnet" "private_1" {
  # The subnet belongs to the VPC 
  # No map_public_ip_on_launch here.
  # The default is false, so EC2 instances won't get Public IPs.

  vpc_id = aws_vpc.base.id

  # CIDR block for this subnet

  cidr_block = "10.0.10.0/24"

  availability_zone = "us-east-1a"

  tags = {
    Name = "private-1"
  }
}


# create Private Subnet 2

resource "aws_subnet" "private_2" {

  vpc_id = aws_vpc.base.id

  cidr_block = "10.0.11.0/24"

  availability_zone = "us-east-1b"

  tags = {
    Name = "private-2"
  }


}

# create Private Subnet 3

resource "aws_subnet" "private_3" {

  vpc_id = aws_vpc.base.id

  cidr_block = "10.0.12.0/24"

  availability_zone = "us-east-1c"

  tags = {
    Name = "private-3"
  }
}

# Create Private Route Table 1

resource "aws_route_table" "private" {
  vpc_id = aws_vpc.base.id

  # Name shown in AWS Console

  tags = {
    Name = "ntier-private-rt"
  }
}

# Create aws route_table_association for private subnet 1

resource "aws_route_table_association" "private_1" {
  subnet_id      = aws_subnet.private_1.id
  route_table_id = aws_route_table.private.id

}


# Create aws route_table_association for private subnet 2
resource "aws_route_table_association" "private_2" {
  subnet_id      = aws_subnet.private_2.id
  route_table_id = aws_route_table.private.id
}

# Create aws route_table_association for private subnet 3
resource "aws_route_table_association" "private_3" {

  subnet_id = aws_subnet.private_3.id

  route_table_id = aws_route_table.private.id

}

# NAT Gateway

resource "aws_eip" "nat" {
  domain = "vpc"

  tags = {
    Name = "ntier-nat-eip"
  }
}

resource "aws_nat_gateway" "nat" {
  allocation_id = aws_eip.nat.id

  subnet_id = aws_subnet.public_1.id

  tags = {
    Name = "ntier-nat-gateway"
  }
}


# aws route for private subnets to use NAT Gateway

resource "aws_route" "private_nat" {
  #Create a route inside the Private Route Table
  route_table_id = aws_route_table.private.id

  # Any destination outside the VPC
  destination_cidr_block = "0.0.0.0/0"

  # Send the traffic to the NAT Gateway / Inside the Private Route Table, if the destination is 0.0.0.0/0 (the internet), send the traffic to the NAT Gateway.
  nat_gateway_id = aws_nat_gateway.nat.id

}

# security group for public EC2 instances

resource "aws_security_group" "sg" {
  name        = "allow_all_traffic"
  description = "Allow all inbound and outbound traffic"
  vpc_id      = aws_vpc.base.id

  tags = {
    Name = "allow_all_traffic"

  }
}


resource "aws_vpc_security_group_ingress_rule" "http" {
  security_group_id = aws_security_group.sg.id
  cidr_ipv4         = "0.0.0.0/0"
  from_port         = 80
  to_port           = 80
  ip_protocol       = "tcp"

}


resource "aws_vpc_security_group_ingress_rule" "ssh" {
  security_group_id = aws_security_group.sg.id
  cidr_ipv4         = "0.0.0.0/0" #Allow everyone from anywhere on the Internet.
  from_port         = 22
  to_port           = 22
  ip_protocol       = "tcp"
}

resource "aws_vpc_security_group_ingress_rule" "https" {
  security_group_id = aws_security_group.sg.id
  cidr_ipv4         = "0.0.0.0/0"
  from_port         = 443
  to_port           = 443
  ip_protocol       = "tcp"
}


resource "aws_vpc_security_group_egress_rule" "all_outbound" {
  security_group_id = aws_security_group.sg.id
  cidr_ipv4         = "0.0.0.0/0"
  ip_protocol       = "-1"
}

# key pair for EC2 instances

resource "aws_key_pair" "terraform" {
  key_name   = "terraform-key"
  public_key = file("c:/Users/Paswa/.ssh/id_ed25519.pub")
}

# ec2 instance for public subnet1 

resource "aws_instance" "web" {
  ami                    = "ami-0b6d9d3d33ba97d99" # Amazon Linux 2 AMI (HVM), SSD Volume Type
  instance_type          = "t3.micro"
  subnet_id              = aws_subnet.public_1.id
  vpc_security_group_ids = [aws_security_group.sg.id]
  key_name               = aws_key_pair.terraform.key_name # Use the key pair created earlier"

  tags = {
    Name = "web-server"
  }
}

```

