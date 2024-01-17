---
title: "Defense in Depth"
description: ""
lead: ""
date: 2022-12-11T16:12:19-0800
lastmod: 2022-12-11T16:12:19-0800
draft: false
images: []
menu:
  docs:
    parent: "logging"
weight: 210
toc: true
---

Defense in Depth is a security strategy that employs multiple layers of security controls within your systems, networks, and applications. It's often a crucial step in helping mature your organizations practices. Zero trust is a thought process making the assumption that your environment already has an attacker within your network and it is your responsibility to take every step you can in order to slow them down. When you think of building out your network think of ways in which you can implement the following. 

- Firewalls
- Access Control
- Encryption
- Intrusion detection
- Vulnerability management
- Security awareness training

## Cloud

Cloud providers often provide you with the necessary tooling to implement defense in depth.

### Amazon Web Services

There are many ways within AWS to achieve defense in depth and it's crucial to utilize each in order to achieve your goals.

#### Methodologies

- Separate different workloads across multiple accounts(Production, Development, SDLC, Security, etc.)
- Implement Service Control Policies(SCPs) to eliminate high risk actions
- Setup guardrails through IAM
- Limit permissions on resource based policies
- Encrypt data through explicit allows with key policies
- Inventory your assets
- Add protective mechanisms to your networks 

#### Tooling

- AWS Organizations
- Identity and Access Management(IAM)
- Security Groups
- Network ACLs
- AWS Inspector
- AWS WAF
- AWS Key Management Service (KMS)
- AWS Config
- AWS Shield
- AWS Network Firewall
- AWS Macie
- AWS Security Hub
