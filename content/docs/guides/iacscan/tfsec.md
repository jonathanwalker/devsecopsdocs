---
title: "Trivy & TFSec"
description: "Minimum Viable DevSecOps 1.2 Self Assessments."
lead: "Self assessments performed by development, security, and operations teams provides them with the knowledge they need to improve."
date: 2020-10-06T08:48:57+00:00
lastmod: 2020-10-06T08:48:57+00:00
draft: false
images: []
menu:
  docs:
    parent: "rego"
weight: 313
toc: true
---

The [trivy](https://github.com/aquasecurity/trivy), formerly [tfsec](https://github.com/aquasecurity/tfsec), utility is an open source static code analysis tool for Terraform maintained by Aqua Security. It checks for misconfigurations in most major cloud providers, hundreds of built in rules, and is built on top of rego policies. 

### Trivy

To install Trivy you can refer to their [installation documentation](https://github.com/aquasecurity/trivy#quick-start) as well as manually install the [releases](https://github.com/aquasecurity/trivy/releases/tag/v0.48.3).

#### Usage

To get started with Trivy all you need to do is run `trivy config .` in your current directory. It will recursively check your current directory for any terraform files and recursively review them for security findings. 

##### Findings

```bash
LOW: Bucket has logging disabled
═════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════
Ensures S3 bucket logging is enabled for S3 buckets

See https://avd.aquasec.com/misconfig/avd-aws-0089
─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
 remotestate/main.tf:2-9
─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
   2 ┌ resource "aws_s3_bucket" "s3_bucket" {
   3 │     bucket = var.bucket_name
   4 │     lifecycle {
   5 │         prevent_destroy = true
   6 │     }
   7 │
   8 │     tags = var.tags
   9 └ }
─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
```

### TFSec

To install tfsec you can refer to their [installation documentation](https://github.com/aquasecurity/tfsec#installation) as well as manually install the [releases](https://github.com/aquasecurity/tfsec/releases). Please note that tfsec has now migrated to Trivy.

#### Usage

Getting started with tfsec is as simple as `tfsec .` to scan the current directory recursively. TFSec will immediately begin scanning your IaC and giving you recommendations for securing your infrastructure. 

#### Findings

```bash
Result #1 HIGH Table encryption is not enabled.
───────────────────────────────────────────────────────────────────────────────────────────────────────────────────
  remotestate/main.tf:38-55
───────────────────────────────────────────────────────────────────────────────────────────────────────────────────
   38  ┌ resource "aws_dynamodb_table" "dynamodb_table" {
   39  │     name           = var.table_name
   40  │     billing_mode   = "PAY_PER_REQUEST"
   41  │     hash_key       = "LockID"
   42  │     read_capacity  = 0
   43  │     write_capacity = 0
   44  │
   45  │     attribute {
   46  └         name = "LockID"
   ..
───────────────────────────────────────────────────────────────────────────────────────────────────────────────────
          ID aws-dynamodb-enable-at-rest-encryption
      Impact Data can be freely read if compromised
  Resolution Enable encryption at rest for DAX Cluster

  More Information
  - https://aquasecurity.github.io/tfsec/latest/checks/aws/dynamodb/enable-at-rest-encryption/
  - https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/dax_cluster#server_side_encryption
───────────────────────────────────────────────────────────────────────────────────────────────────────────────────
```
