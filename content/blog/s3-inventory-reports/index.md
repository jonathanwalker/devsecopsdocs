---
title: "Data Lake Scavenger Hunt"
description: "This blog post goes over how to leverage S3 inventory reports to identify files that are no longer being used."
date: 2022-12-26T21:35:43-0800
lastmod: 2022-12-26T21:35:43-0800
draft: false
weight: 10
images: [ "s3-inventory-diagram.png" ]
categories: ["S3"]
tags: ["security", "s3", "inventory", "aws", "reports", "athena", "directory", "size"]
contributors: ["Jonathan Walker"]
pinned: false
homepage: false
---

Have you ever tried figuring out how large a given folder is in S3 that contains millions of objects? Have you ever wondered what percentage of your objects are encrypted? Determined if there are any objects with public object ACLs? Unless you have decades to wait for the completion of an aws cli command, S3 inventory reports is the answer to the questions you may have had.  

This blog post will go over how to setup S3 inventory reports, query that data in parquet, and help you identify where unnecessary data might be stored. This goes beyond the [AWS documentation regarding querying your S3 inventory with Amazon Athena](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-inventory-athena-query.html) with examples, infrastructure, and a deep dive into the setup. 

## What are S3 Inventory Reports?

Amazon S3 Inventory is a feature of Amazon S3 that enables users to generate reports about the objects stored in their S3 bucket. These reports provide information about the objects such as object key, size, ETag, version, storage class, and metadata. S3 Inventory reports can be used for a variety of purposes, such as tracking changes to objects, identifying stale objects that can be deleted, and auditing the access permissions of objects.

There are three types of S3 Inventory reports output formats: CSV, Apache optimized row columnar (ORC), and Apache Parquet. CSV reports are simple, comma-separated value files that can be opened in a text editor or spreadsheet application. Parquet and ORC are a columnar storage format that is optimized for big data analytics and is supported by a number of data processing systems such as Athena. S3 Inventory reports can be generated on a daily or weekly basis and delivered to an s3 bucket of your choosing. 

S3 Inventory can be configured through the Amazon S3 Management Console, AWS CLI, or the Amazon S3 API. Users can specify the bucket to be inventoried, the destination for the reports, and the frequency of the report generation. Users can also specify optional filters to include or exclude objects based on their key prefix or tag. In addition, users can enable data integrity validation to ensure the accuracy of the report by including the ETag and version ID of each object in the report.

## Why use S3 Inventory Reports?

Inventory reporting can help you ask a wide variety of questions about the contents of your S3 bucket especially when that bucket is extremely large. Do you have a multi-petabyte datalake bucket? Logging bucket with millions of objects? Inventory reporting can give you an idea as to what lies within them without hitting API limits and waiting for an extended period of time. Below are some of the questions they can help answer.

- How old are objects within my bucket?
- Size of objects within the bucket?
- Size of keys/folders within the bucket?
- What objects are encrypted?
- What storage tiers do my objects live in?
- How much data will be deleted when implementing a lifecycle policy?

## What can inventory reports help you achieve?

Why should you setup S3 inventory reporting? Often times when buckets go over millions of objects, getting an idea of where your data is can become challenging. Here are some items you might want to achieve which inventory reporting can help with. 

- Data that has been forgotten about that should be deleted
- Identify large directories
- Optimize file sizes per object
- Track objects and sizes over time

## What is in Inventory Reports?

The following below contains all the data points available in S3 inventory reports.

- Bucket
- Key
- Version
- Is Latest
- Deletion Markers
- Size
- Last Modified Date
- e_tag
- Storage Class
- Multipart Upload Flag
- Replication Status
- Encryption Status
- Object Lock Retain Until Date
- Object Lock Mode
- Object Lock Legal Hold Status
- Intelligent Tiering Access Tier
- Bucket Key Status
- Checksum Algorithm

## Querying the Data

This section will go over how to make this data valuable to you and how to retrieve certain information about the bucket through [AWS Athena](https://aws.amazon.com/athena/). Below are some assumptions we are making about your setup and involves how it was setup through our [terraform module](https://github.com/DevSecOpsDocs/terraform-s3-inventory). We will do a deep dive into the infrastructure shortly after. 

```
Bucket: s3://example-bucket
Database: bucket_inventory
Table: example_bucket
```

### Date

Depending on the time of day, the last inventory report was most likely obtained the previous day. In which you need to specify the `dt` relative to yesterday. You can do so by performing the following within your where clause. 

```sql
dt = date_format(
  current_timestamp - INTERVAL '1` DAY,
  '%Y-%m-%d-00-00'
)
```

### Query Objects

To give you an example as to what the data looks like, this query allows you to get the first 10 rows in the table. 

```sql
select
  *
from
  bucket_inventory.example_bucket
limit
  10
```

### Size of the Bucket

To calculate the total size of the bucket you can perform this query.

```sql
select
  sum(size) as bytes
from
  bucket_inventory.example_bucket
where
  dt = date_format(
    current_timestamp - INTERVAL '1' DAY,
    '%Y-%m-%d-00-00'
  )
```

### Calculate Folder/Key Size

To calculate the size for a "folder", S3 does not have the concept of a 'Directory' or a 'Folder' but common key paths, you can perform this query. In this example we will try figure out how large cloudtrail logs are per region with the keys `s3://example-bucket/CloudTrail/0000000000/us-east-1`.

```sql
with dir as (
  select
    bucket,
    split_part(key, '/', 2) as account_id,  --- 2 is the account number
    split_part(key, '/', 3) as region_name, --- 3 is the region which we want to identify
    sum(size) as total_size
  from
     bucket_inventory.example_bucket
  where
    dt =  date_format(
      current_timestamp - INTERVAL '1' DAY,
      '%Y-%m-%d-00-00'
    )
  group by
    1,
    2,
    3
)
select
  region_name,
  total_size
from
  dir
where
  account_id = '0000000000'
order by
  total_size desc
```

### Calculate Lifecycle Removal

This query retrieves the sum of bytes to be removed for files older than 90 days within the key/directory `directory/test/%`.

```sql
select
  sum(size) as bytes
from
  (
    select
      key,
      size,
      from_unixtime(last_modified_date / 1000) as last_modified
    from
      bucket_inventory.example_bucket
    where
      dt =  date_format(
        current_timestamp - INTERVAL '1' DAY,
        '%Y-%m-%d-00-00'
      )
      and (
        key like 'directory/test/%'
      )
  )
where
  last_modified < date(current_timestamp - INTERVAL '90' DAY)
```

## Deployment Deep Dive

{{< callout context="caution" title="Caution" icon="alert-triangle" >}}
Be sure to wait a full day for inventory reports to get delivered!
{{< /callout >}}

To setup S3 inventory reports on your S3 bucket, you can leverage our [terraform module on GitHub](https://github.com/DevSecOpsDocs/terraform-s3-inventory). Below is how to do so through terragrunt. 

```
terraform {
  source = "git::git@github.com:DevSecOpsDocs/terraform-s3-inventory.git"
}

inputs = {
  report_bucket	= "company-s3-inventory-reports"

  inventory_expiration_days	= 30

  s3_inventory_configuration = {
    terraform_state = {
      bucket = "example-bucket"
      included_object_versions = "Current"
      optional_fields = [
        "Size",
        "LastModifiedDate",
        "StorageClass",
        "ETag",
        "IsMultipartUploaded",
        "IntelligentTieringAccessTier",
        "EncryptionStatus"
      ]
      frequency = "Daily"
      format = "Parquet"
    }
  }

  tags = {
    "Name"  = "company-s3-inventory-reports"
    "Owner" = "security"
  }
}
```

### Terraform: Inventory Reports

Quickly diving into the infrastructure, this creates the inventory configuration for every bucket you have specified in your `s3_inventory_configuration` variable with their respective configuration.

```
resource "aws_s3_bucket_inventory" "inventory" {
  for_each = var.s3_inventory_configuration

  name   = "${each.value["bucket"]}-inventory"
  bucket = each.value["bucket"]

  included_object_versions = each.value["included_object_versions"]
  optional_fields          = each.value["optional_fields"]
  schedule {
    frequency = each.value["frequency"]
  }

  destination {
    bucket {
      bucket_arn = aws_s3_bucket.inventory_bucket.arn
      format = each.value["format"]
    }
  }
}
```

### Terraform: Glue Table and Database

This is the infrastructure necessary to query your data with Athena. 

```
resource "aws_glue_catalog_database" "database" {
  name        = "s3_inventory"
  description = "Database for storing S3 inventory reports"
}

resource "aws_glue_catalog_table" "table" {
  for_each = var.s3_inventory_configuration

  name = replace(each.value["bucket"], "-", "_")
  database_name = aws_glue_catalog_database.database.name
  table_type = "EXTERNAL_TABLE"

  storage_descriptor {
    location = "s3://${aws_s3_bucket.inventory_bucket.id}/${each.value["bucket"]}/${each.value["bucket"]}/hive"

    input_format  = "org.apache.hadoop.hive.ql.io.SymlinkTextInputFormat"
    output_format = "org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat"

    columns {
      name = "bucket"
      type = "string"
    }
    columns {
      name = "key"
      type = "string"
    }
    columns {
      name = "version_id"
      type = "string"
    }
    columns {
      name = "is_latest"
      type = "boolean"
    }
    columns {
      name = "is_delete_marker"
      type = "boolean"
    }
    columns {
      name = "size"
      type = "bigint"
    }
    columns {
      name = "last_modified_date"
      type = "bigint"
    }
    columns {
      name = "e_tag"
      type = "string"
    }
    columns {
      name = "storage_class"
      type = "string"
    }
    columns {
      name = "is_multipart_uploaded"
      type = "boolean"
    }
    columns {
      name = "replication_status"
      type = "string"
    }
    columns {
      name = "encryption_status"
      type = "string"
    }
    columns {
      name = "object_lock_retention_until_date"
      type = "bigint"
    }
    columns {
      name = "object_lock_mode"
      type = "string"
    }
    columns {
      name = "object_lock_legal_hold_status"
      type = "string"
    }
    columns {
      name = "intelligent_tiering_access_tier"
      type = "string"
    }
    columns {
      name = "bucket_key_status"
      type = "string"
    }
    columns {
      name = "checksum_algorithm"
      type = "string"
    }

    ser_de_info {
      name = "org.apache.hadoop.hive.ql.io.parquet.serde.ParquetHiveSerDe"
      parameters = {
        "serialization.format" = "\t"
      }
    }
  }

  # Projection configuration
  parameters = {
    "EXTERNAL"                = "TRUE"
    "projection.enabled"      = "true"
    "projection.dt.type"      = "date"
    "projection.dt.format"    = "yyyy-MM-dd-HH-mm"
    "projection.dt.interval"  = "1"
    "projection.dt.range"     = "2022-01-01-00-00,NOW"

    "projection.dt.interval.unit" = "DAYS"
  }

  partition_keys {
    name = "dt"
    type = "string"
  }
}
```

## Conclusion

Hopefully this helped you deploy s3 inventory reports, query that data with Athena, and ask questions about your objects you may have not known how to do so before. 
