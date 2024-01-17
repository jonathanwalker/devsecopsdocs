---
title: "Azure Resource Graph"
description: "Leveraging Azure Resource Graph for Effective DevSecOps Self-Assessments."
lead: ""
date: 2024-01-16T15:56:35-0600
lastmod: 2024-01-16T15:56:35-0600
draft: false
images: []
weight: 323
---

Azure Resource Graph is a powerful service within Microsoft Azure that enables users to explore, query, and analyze their Azure resources at scale. This tool is instrumental for efficient cloud management, offering deep insights into resource configurations and relationships.

### Azure Resource Graph

Before using Azure Resource Graph, ensure you have the necessary permissions within your Azure subscription. For guidance on getting started, refer to the [Azure Resource Graph documentation](https://docs.microsoft.com/en-us/azure/governance/resource-graph/).

#### Usage

Azure Resource Graph uses Kusto Query Language (KQL) for querying resources. For example, to list all virtual machines in your subscription, you would use:

```kql
Resources
| where type == 'microsoft.compute/virtualmachines'
| project name, location, resourceGroup
```

This query lists the names, locations, and resource groups of all virtual machines.

#### Findings

The results are displayed in a tabular format, making it easy to review the data:

```kql
 Name          | Location   | Resource Group
---------------|------------|----------------
 VM1           | eastus     | RG1
 VM2           | westeurope | RG2
 ...
```

### Compliance and Security Audits

Azure Resource Graph is particularly useful for compliance checks and security audits, enabling you to query and analyze resource properties and relationships quickly.

#### Usage

To identify network security groups with overly permissive rules, you could run:

```kql
SecurityResources
| where type == 'microsoft.network/networksecuritygroups'
| extend rules = parse_json(properties.securityRules)
| mv-expand rules
| where rules.properties.access == 'Allow' and rules.properties.direction == 'Inbound'
| project name, rules.name, rules.properties.priority, rules.properties.sourceAddressPrefix, rules.properties.destinationPortRange
```

This query helps in identifying network security group rules that might be too permissive.

#### Findings

The output will provide a detailed view of each rule that matches the criteria:

```kql
 NSG Name | Rule Name | Priority | Source Address Prefix | Destination Port Range
----------|-----------|----------|-----------------------|------------------------
 NSG1     | rule1     | 100      | *                     | 80
 NSG2     | rule2     | 200      | 0.0.0.0/0             | 443
 ...
```

Azure Resource Graph is a vital tool for Azure administrators and security professionals. It offers a comprehensive way to visualize and analyze resource properties and relationships, facilitating better governance, risk management, and resource optimization across your Azure environment.
