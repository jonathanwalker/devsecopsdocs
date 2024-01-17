---
title: "AWS Config"
description: "Leveraging AWS Config for Effective DevSecOps Self-Assessments."
lead: ""
date: 2024-01-16T15:56:35-0600
lastmod: 2024-01-16T15:56:35-0600
draft: false
images: []
weight: 322
---

AWS Config Advanced Queries is a powerful feature that enables users to gain deeper insights into their AWS resource configurations and relationships. It leverages the AWS Config resource inventory and allows queries using SQL-like syntax, providing a nuanced view of your cloud environment.

### AWS Config Advanced Queries

To utilize AWS Config Advanced Queries, ensure that AWS Config is enabled and recording in your account. You can refer to the [AWS Config documentation](https://docs.aws.amazon.com/config/latest/developerguide/WhatIsConfig.html) for setup instructions.

#### Usage

With Advanced Queries, you can run SQL-like queries on your AWS resource inventory. For instance, to retrieve a list of publicly accessible S3 buckets, you might use:

```sql
SELECT
  resourceId,
  resourceName,
  configuration.publicAccessBlockConfiguration.blockPublicAcls as blockPublicAcls
FROM
  AWS::S3::Bucket
WHERE
  configuration.publicAccessBlockConfiguration.blockPublicAcls = false;
```

This query will return S3 buckets with their public access block configurations.

#### Findings

The output of this query will be presented in a structured format, making it easier to review and act upon:

```sql
 Bucket ID   | Bucket Name | Block Public ACLs
-------------|-------------|-------------------
 bucket-1234 | mybucket1   | false
 bucket-5678 | mybucket2   | false
 ...
```

### Compliance and Governance Checks

Advanced Queries are particularly useful for compliance and governance. They can help ensure adherence to policies and standards across your AWS infrastructure.

#### Usage

To check for non-compliant EC2 instances against specific security groups, you could run:

```sql
SELECT
  resourceId,
  resourceName,
  configuration.securityGroups
FROM
  AWS::EC2::Instance
WHERE
  NOT configuration.securityGroups CONTAINS 'sg-123abc';
```

This query identifies EC2 instances not associated with a specified security group.
