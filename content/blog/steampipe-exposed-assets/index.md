---
title: "Discover Public Assets with Steampipe and Nuclei"
description: "How to use steampipe and nuclei to discover public assets."
date: 2024-02-02T12:07:14-0600
lastmod: 2024-02-02T12:07:14-0600
draft: false
weight: 10
categories: ["scanning"]
tags: ["nuclei", "steampipe", "exposure", "sql"]
contributors: ["Jonathan Walker"]
pinned: false
homepage: false
---

There are no shortage of publicly known breaches due to accidentally exposed assets within cloud environments. Few exposures ever make it to the news cycle and occur frequently within the industry due to improper training, lack of infrastructure as code reviews, and misuse of priviledges. 

- [Amazon Prime Video Elasticsearch Exposure](https://techcrunch.com/2022/10/27/amazon-prime-video-server-exposed/)
- [Adobe left 7.5 million Creative Cloud user records exposed online](https://www.zdnet.com/article/adobe-left-7-5-million-creative-cloud-user-records-exposed-online/)
- [Exposed Jenkins instance leads to exposure of No-Fly list](https://maia.crimew.gay/posts/how-to-hack-an-airline/)

When was the last time you assessed your attack surface? Do you get alerted? How often are those alerts triaged to their full extent? While CSPM tool offerings provide attack surface capabilities, one should never shy away from manual assessments on a regular cadence. Here is a quick guide on how to perform a quick attack surface assessment of AWS EC2 using [steampipe](https://steampipe.io/) and [nuclei](https://github.com/projectdiscovery/nuclei).

## Installation

In order to get started, you need to first install steampipe and nuclei. This should help you retrieve a list of public facing assets and scan them. 

### Steampipe

Steampipe is a tool that allows you to query your cloud resources through SQL. We are going to be using steampipe to get a list of assets to scan. Feel free to go to [Steampipe's installation guide](https://steampipe.io/downloads) for more information.

{{< tabs "create-new-site" >}}
{{< tab "macOS" >}}

```bash
brew install turbot/tap/steampipe
```

{{< /tab >}}
{{< tab "Linux" >}}

```bash
sudo /bin/sh -c "$(curl -fsSL https://steampipe.io/install/steampipe.sh)"
```

{{< /tab >}}
{{< tab "Windows" >}}

```bash
# WSL Shell
sudo /bin/sh -c "$(curl -fsSL https://steampipe.io/install/steampipe.sh)"
```

{{< /tab >}}
{{< /tabs >}}

#### Plugins

Steampipe relies on plugins in order to perform SQL queries against your providers. Steampipe supports a wide variety of services such as AWS, GCP, Azure, Kubernetes, and so much more. We will just be covering the basics here but do not shy away from the documentation. 

```bash
# AWS
steampipe plugin install aws

# GCP
steampipe plugin install gcp

#Azure
steampipe plugin install azure
```


### Nuclei

{{< tabs "create-new-site" >}}
{{< tab "Go" >}}

```bash
go install -v github.com/projectdiscovery/nuclei/v3/cmd/nuclei@latest
```

{{< /tab >}}
{{< tab "macOS" >}}

```bash
brew install nuclei
```

{{< /tab >}}
{{< tab "Docker" >}}

```bash
docker pull projectdiscovery/nuclei:latest
```

{{< /tab >}}
{{< /tabs >}}


## Obtaining Public Compute Infrastructure

```sql
select
  instance_id,
  public_ip_address
from
  aws_ec2_instance
where
  public_ip_address is not null;
```

```sql
select
  name,
  dns_name,
  type
from
  aws_elbv2_load_balancer
where
  scheme = 'internet-facing';
```

```sql
select
  load_balancer_name,
  dns_name
from
  aws_elb_load_balancer
where
  scheme = 'internet-facing';
```


```bash
# Query to get EC2 instance public IPs
steampipe query "select instance_id as id, public_ip_address as address from aws_ec2_instance where public_ip_address is not null;" --output csv > ec2_ips.csv

# Query to get Load Balancer DNS names
steampipe query "select name as id, dns_name as address from aws_elbv2_load_balancer where scheme = 'internet-facing' UNION select load_balancer_name as id, dns_name as address from aws_elb_load_balancer where scheme = 'internet-facing';" --output csv > elb_dns.csv

# Combine the outputs
echo "id,address" > combined_public_resources.csv
tail -n +2 -q ec2_ips.csv elb_dns.csv >> combined_public_resources.csv

# Cleanup
rm ec2_ips.csv elb_dns.csv

# Display the result
cat combined_public_resources.csv
```

## Conclusion

[multi-region connections](https://hub.steampipe.io/plugins/turbot/aws#multi-region-connections) and [multi-account connections](https://hub.steampipe.io/plugins/turbot/aws#multi-account-connections)

```bash
nuclei -u $TARGET -t network/detection
```

## Ports


```
httpx -p 80,443,8080,8443,8000,8008,8081,81,8888,8001,8082,7080,8444,8983,9999 -l targets.txt -title

    __    __  __       _  __
   / /_  / /_/ /_____ | |/ /
  / __ \/ __/ __/ __ \|   /
 / / / / /_/ /_/ /_/ /   |
/_/ /_/\__/\__/ .___/_/|_|
             /_/

		projectdiscovery.io

[INF] Current httpx version v1.3.9 (latest)
http://44.201.243.167:8080 [Dashboard [Jenkins]]
```
