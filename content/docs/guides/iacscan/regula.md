---
title: "Regula"
description: "Minimum Viable DevSecOps 1.2 Self Assessments."
lead: "Self assessments performed by development, security, and operations teams provides them with the knowledge they need to improve."
date: 2020-10-06T08:48:57+00:00
lastmod: 2020-10-06T08:48:57+00:00
draft: false
images: []
menu:
  docs:
    parent: "tfsec"
weight: 312
toc: true
---

[Regula](https://github.com/fugue/regula) is an open-source static code analysis tool for Terraform maintained by Fugue. It checks for misconfigurations terraform, cloudformation, and kubernetes files. Which can be useful when you wish to test a variety of different infrastructure as code files for misconfigurations. 

## Installation

To install regula you can refer to their [installation documentation](https://github.com/fugue/regula#installation) as well as their [release page](https://github.com/fugue/regula/releases).

{{< tabs "create-new-site" >}}
{{< tab "macOS" >}}

```bash
brew tap fugue/regula
brew install regula
```

{{< /tab >}}
{{< tab "Linux" >}}

```bash
# Visit releases page
# https://github.com/fugue/regula/releases
sudo mv regula /usr/local/bin
```

{{< /tab >}}
{{< tab "Windows" >}}

```bash
# Visit releases page
# https://github.com/fugue/regula/releases
md C:\regula\bin
move regula.exe C:\regula\bin
setx PATH "%PATH%;C:\regula\bin"
```

{{< /tab >}}
{{< /tabs >}}


## Usage

Getting started with regula is as simple as `regula run .` to scan the current directory recursively. Regula will immediately begin to scan your IaC and identify the locations of those misconfigurations.

### Findings

### DynamoDB Encryption

```bash
FG_R00069: DynamoDB tables should be encrypted with AWS or customer managed KMS keys [Medium]
           https://docs.fugue.co/FG_R00069.html

  [1]: aws_dynamodb_table.dynamodb_table
       in remotestate/main.tf:38:1
```

#### CloudFront Geo-Restrictions

```bash
FG_R00018: CloudFront distributions should have geo-restrictions specified [Medium]
           https://docs.fugue.co/FG_R00018.html

  [1]: aws_cloudfront_distribution.distribution
       in s3_static_site/main.tf:5:1
```
