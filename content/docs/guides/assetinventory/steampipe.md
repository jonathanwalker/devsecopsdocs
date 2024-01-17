---
title: "Steampipe"
description: "Leveraging Steampipe for Effective DevSecOps Self-Assessments."
lead: ""
date: 2024-01-16T15:56:35-0600
lastmod: 2024-01-16T15:56:35-0600
draft: false
images: []
weight: 321
---

[Steampipe](https://steampipe.io/), an innovative open-source tool, allows DevSecOps teams to query cloud services and resources using SQL. This tool is a game-changer for managing cloud compliance, security, and configuration. It supports numerous cloud providers and services, offering flexibility and depth in assessments.

### Steampipe

To get started with Steampipe, you can refer to their [installation documentation](https://steampipe.io/downloads). Once installed, you can set up various [plugins](https://hub.steampipe.io/plugins) to connect with different cloud providers and services.

#### Usage

Begin using Steampipe by writing SQL queries to inspect your cloud resources. For example, to check AWS EC2 instances, you can execute:

```sql
select
  instance_id,
  instance_state,
  instance_type
from
  aws_ec2_instance;
```

This query will list all EC2 instances with their states and types, providing a clear overview of your AWS infrastructure.

#### Findings

Steampipe outputs the results in a clear, tabular format. For example, if you query AWS S3 buckets for public access policies, it will display:

```bash
 Bucket Name | Publicly Accessible | Policy Status
-------------|---------------------|---------------
 mybucket1   | true                | [JSON Policy]
 mybucket2   | false               | [JSON Policy]
 ...
```

This format makes it easy to identify and address security concerns quickly.

### Cloud Compliance Checks

Steampipe excels in compliance checks. With its [compliance mod feature](https://hub.steampipe.io/mods), you can assess your cloud infrastructure against various standards like CIS, NIST, or custom benchmarks.

#### Usage

Run compliance checks by executing specific mods:

```bash
steampipe check aws_compliance.benchmark.cis_v1_2_0
```

This command will perform a series of checks against the CIS AWS Foundations Benchmark.

#### Findings

The results will be presented in a comprehensive report, detailing compliance status for each control, including passed, failed, and skipped checks.

```bash
Control ID   | Description                   | Status
-------------|-------------------------------|-------
 aws_cis_1_1 | Ensure MFA is enabled for ... | Passed
 aws_cis_1_2 | Ensure CloudTrail is enabled  | Failed
 ...
```

With its SQL-powered interface and extensive plugin support, Steampipe is an invaluable tool for DevSecOps teams aiming to enhance their security and compliance posture in the cloud.
