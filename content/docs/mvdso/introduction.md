---
title: "Overview"
description: "Minimum Viable DevSecOps is what is necassary to have a successful DevSecOps team."
date: 2020-11-16T13:59:39+01:00
lastmod: 2020-11-16T13:59:39+01:00
draft: false
images: []
menu:
  docs:
    parent: "capability"
weight: 110
toc: true
---

This page should be a reference for individuals wishing to build out a team, mature a team, or validate what controls you currently implement within your organization to empower others to build security into the product. Heavily inspired by <a href="https://mvsp.dev/mvsp.en/index.html">Minimum Viable Secure Product</a>, this documentation will include not only the topics required for a minimum viable DevSecOps team but also include documentation on approaches you can take to secure your environment. 

[![XKCD](https://imgs.xkcd.com/comics/standards.png#center "XKCD image about how standards proliferate through adding more standards.")](https://xkcd.com/927/)

## Minimum Viable DevSecOps

This should be treated as a minimalist checklist for you to create a minimum viable DevSecOps function within your organization and how you can build security into your product.

### 1.x Business Controls

Business controls are measures that a company puts in place to ensure that operations are conducted in a manner that is consistent with its policies, goals, and regulatory requirements. These business controls are intended to help the organization protect its assets and ensure the integrity, confidentiality, and availability of its systems and data.

| Item             | Description                                                                                                     |
|------------------|-----------------------------------------------------------------------------------------------------------------|
| Communication    | Channels are established for engineers to reach out for security expertise                                      |
| Assessments      | Ability for engineers to assess their own work based on security guidance                                       |
| Design Reviews   | An established process for engineers to ask for guidance on projects                                            |
| Training         | Training must be engaging, educational, and beneficial to the organization                                      |
| Compliance       | Understanding business obligations and translating them to actionable items                                     |
| Logging          | Centralized location to store security logs                                                                     |
| Defense in Depth | Isolation of high risk applications, isolated development environment, and use of production data is restricted |


### 2.x Design controls

Design controls help to ensure that the development, operations, and security aspects of an organization's systems are properly integrated and working together to effectively secure the organization's assets.

| Topic                                   | Description                                                                                                        |
| --------------------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| Identity and Access Management          | Single Sign-On, avoiding static credentials, fido u2f, password policies, secrets, etc.                            |
| Best Practices                          | Best practices are documented and adhered to such as HTTPs only, security headers, benchmarks, documentation, etc. |
| Dependency and Vulnerability Management | Frameworks are leveraged, scans are performed, libraries sanitizing inputs, and escaping outputs                   |
| Patching                                | Processes and procedures for patching are clear and easily reproducible                                            |
| Supply Chain                            | Reduced supply chain risks through mirroring packages/images and using trusted sources                             |
| Alerting                                | Established mechanism to alert on high impact findings                                                             |
| Metrics                                 | Ability to demonstrate the impact of your work through metrics                                                     |
| Encryption                              | Encryption standards are configured, principal of least privilege, and keys are rotated                            |
| Threat Modeling                         | Standardized approach for modeling threats                                                                         |

### 3.x Implementation controls

Effective implementation controls that demonstrate value add for security in an iterative process.

| Topic                  | Description                                                                                             |
| ---------------------- | ------------------------------------------------------------------------------------------------------- |
| Asset inventories      | Centralized location to search infrastructure inventory                                                 |
| Data flows             | Secure transport of data and defined handling processes                                                 |
| Capability Models      | Documenting capabilities on the team to identify improvements                                           |
| Established SLAs       | Time to fix based on priorities and impact are established                                              |
| SDLC                   | Incremental and iterative approach for the products functionality                                       |
| Infrastructure as Code | Iterative infrastructure as code processes                                                              |
| Guardrails             | Provide engineers the ability to operate with relative freedom without significant risk                 |
| Static Analysis        | Tooling provided to empower engineering to make the right decisions                                     |
| Dynamic Analysis       | Perform dynamic analysis on infrastructure and code on a regular basis                                  |

### 4.x Operational Controls

These are operational controls in place that should be part of the business processes. 

| Topic                         | Description                                                                                             |
| ----------------------------- | ------------------------------------------------------------------------------------------------------- |
| Principal of Least Privilege  | Production and sensitive data is restricted                                                             |
| Vendors                       | Established vendor integration requirements and reviews are performed                                   |
| Backups and Disaster Recovery | Procedures for backups, disaster recovery, and documentation for BCPDR                                  |
