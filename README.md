<img src="https://github.com/bhuvan-raj/Opentofu-Quickscroll/blob/main/opentofu.png" alt="Banner" />





## 📖 Table of Contents

1. [What is OpenTofu?](#what-is-OpenTofu)
2. [Why OpenTofu Exists (History)](#Why-OpenTofu-Exists-(History))
3. [Benefits of OpenTofu](#benefits-of-opentofu)
4. [Installing OpenTofu](#installing-opentofu)
5. [First OpenTofu Project](#first-opentofu-project)
6. [Basic Concepts](#basic-concepts)
7. [State Management](#state-management)
8. [Using Providers](#using-providers)
9. [Modules in OpenTofu](#modules-in-opentofu)
10. [Variables and Outputs](#variables-and-outputs)
11. [Remote Backend Configuration](#remote-backend-configuration)
12. [Advanced Topics](#advanced-topics)
13. [OpenTofu vs Terraform](#opentofu-vs-terraform)
14. [Community and Contribution](#community-and-contribution)
15. [Useful Resources](#useful-resources)

---

## What is OpenTofu?

**OpenTofu** is an open-source Infrastructure as Code (IaC) tool that allows you to define and provision infrastructure using a declarative configuration language (HCL).

* Fully open-source under **MPL 2.0**
* Created as a **drop-in replacement** for Terraform
* Enables infrastructure automation using HCL

---

## Why OpenTofu Exists (History)

| Date            | Event                                                               |
| --------------- | ------------------------------------------------------------------- |
| Before Aug 2023 | Terraform was open-source (MPL 2.0)                                 |
| Aug 2023        | HashiCorp relicensed Terraform to BSL, restricting its use          |
| Sep 2023        | Community forks Terraform → renamed to **OpenTofu**                 |
| Now             | OpenTofu is governed by the **Linux Foundation**, not a corporation |

---

##  Benefits of OpenTofu

* **Truly Open Source**: Uses permissive MPL 2.0 license
* **100% Terraform Compatible**: Same HCL syntax, providers, modules
* **Drop-in Replacement**: Replace Terraform CLI with `tofu` and it just works
* **Community-Driven**: Transparent development and roadmap
* **Safe for Commercial Use**: No license lock-in for SaaS builders

---

---
## Opentofu Commands

| Command          | Description                                                                            |
| ---------------- | -------------------------------------------------------------------------------------- |
| `tofu init`      | Initializes the configuration directory, downloads providers and modules.              |
| `tofu plan`      | Shows what actions OpenTofu will take to achieve the desired state.                    |
| `tofu apply`     | Applies the planned changes to reach the target infrastructure state.                  |
| `tofu destroy`   | Destroys the infrastructure defined in your `.tf` files.                               |
| `tofu validate`  | Validates the syntax and internal consistency of your configuration.                   |
| `tofu fmt`       | Formats `.tf` files to a canonical style.                                              |
| `tofu show`      | Shows the current state or a plan file in human-readable form.                         |
| `tofu state`     | Interact with the state file (list, show, rm, mv, pull, push).                         |
| `tofu output`    | Displays output values from your configuration.                                        |
| `tofu taint`     | Marks a resource for recreation on the next apply.                                     |
| `tofu untaint`   | Removes the tainted mark from a resource.                                              |
| `tofu refresh`   | Updates the state file with real infrastructure values (deprecated in newer versions). |
| `tofu graph`     | Generates a visual dependency graph of resources.                                      |
| `tofu providers` | Shows which providers are used in the configuration.                                   |
| `tofu version`   | Displays the current OpenTofu version.                                                 |
| `tofu login`     | (Not needed unless working with remote backends like Spacelift, etc.)                  |



##  First OpenTofu Project

### File: `main.tf`

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "my_ec2" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
  tags = {
    Name = "OpenTofuInstance"
  }
}
```

### Commands:

```bash
tofu init
tofu plan
tofu apply
```

---

##  Basic Concepts

| Concept    | Description                             |
| ---------- | --------------------------------------- |
| `resource` | Defines cloud infrastructure components |
| `provider` | Interface to AWS, GCP, Azure, etc.      |
| `module`   | Reusable code blocks                    |
| `variable` | Input parameters to configuration       |
| `output`   | Expose info from modules or resources   |
| `state`    | Tracks real-world infra                 |

---

## 🗂 State Management

* Stores infrastructure status in `.tfstate`
* Local by default, can be moved to cloud (S3 + DynamoDB)

### Backend Example:

```hcl
terraform {
  backend "s3" {
    bucket = "my-tofu-states"
    key    = "dev/ec2.tfstate"
    region = "us-east-1"
    dynamodb_table = "my-lock-table"
  }
}
```

---

##  Using Providers

Providers are plugins that manage specific APIs (e.g., AWS, Azure).

### Example:

```hcl
provider "aws" {
  region = "us-east-1"
}
```

---

##  Modules in OpenTofu

```hcl
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "5.0.0"

  name = "opentofu-vpc"
  cidr = "10.0.0.0/16"

  azs             = ["us-east-1a", "us-east-1b"]
  public_subnets  = ["10.0.1.0/24", "10.0.2.0/24"]
}
```

---

##  Variables and Outputs

### `variables.tf`

```hcl
variable "instance_type" {
  type    = string
  default = "t2.micro"
}
```

### `outputs.tf`

```hcl
output "instance_id" {
  value = aws_instance.my_ec2.id
}
```

---

##  Remote Backend Configuration

* Enables shared state and collaboration
* Supported backends: AWS S3, Azure Blob, GCS, etc.

---

##  Advanced Topics

### Looping with `for_each`:

```hcl
resource "aws_instance" "servers" {
  for_each = toset(["one", "two"])
  ami           = "ami-xyz"
  instance_type = "t2.micro"
  tags = {
    Name = each.key
  }
}
```

### Dynamic Blocks:

```hcl
resource "aws_security_group" "example" {
  dynamic "ingress" {
    for_each = var.ports
    content {
      from_port   = ingress.value
      to_port     = ingress.value
      protocol    = "tcp"
      cidr_blocks = ["0.0.0.0/0"]
    }
  }
}
```

---

##  OpenTofu vs Terraform

| Feature           | OpenTofu         | Terraform (Post-Aug 2023) |
| ----------------- | ---------------- | ------------------------- |
| License           | MPL 2.0          | BSL (restricted)          |
| Governance        | Linux Foundation | HashiCorp                 |
| Community RFCs    | ✅ Yes            | ❌ No                      |
| Commercial Safety | ✅ Yes            | ⚠️ Limited                |

---

##  Community and Contribution

* GitHub: [https://github.com/opentofu/opentofu](https://github.com/opentofu/opentofu)
* Docs: [https://opentofu.org/docs/](https://opentofu.org/docs/)
* Slack: [https://slack.opentofu.org](https://slack.opentofu.org)
* Join weekly dev calls and RFCs

---

##  Useful Resources

* [Official Website](https://opentofu.org)
* [Get Started](https://opentofu.org/docs/)
* [Blog](https://blog.opentofu.org)
* [Module Registry](https://registry.terraform.io/)
* [Community RFCs](https://github.com/opentofu/rfcs)

---

##  Final Words

OpenTofu is the future of open and community-led Infrastructure as Code. It's an excellent alternative to Terraform, especially for learners, educators, and enterprise users who want full control, transparency, and collaboration.

