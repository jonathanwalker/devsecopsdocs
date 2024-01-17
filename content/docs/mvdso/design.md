---
title: "Design Reviews"
description: ""
lead: ""
date: 2022-12-03T12:48:57-0800
lastmod: 2022-12-03T12:48:57-0800
draft: false
images: []
menu:
  docs:
    parent: "communication"
weight: 160
toc: true
---

Design Reviews should be a useful tool in a security teams tool belt to include security into the conversations. Do you currently have a defined product lifecycle? Are you included within those decisions? If not you should do your best to ensure that security is part of the process and be fully integrated into the product lifecycle. This can help you identify risky changes that will be made to your product and help design security into the organization. In order to be included in security relavant decisions within the organizations product is to catch risky changes that the organization might be making. Here are just a few points you should be looking out for. 

- Changes to AAA(authentication, authorization, or auditing) within your product
- Internal tooling handling user data
- Compliance related flows
  - Download my Data
  - Account deletion jobs
  - User data deletion lifecycle
  - Anonymization jobs
  - Employee user data permissions
- New endpoints
- New infrastructure
  - Externally facing infrastructure
- New API routes
  - Major changes in the application
  - New functionality within the application
  - Media upload features
- Working with vendors
  - Third party SDKs
  - User data flows
- Utilizing third party vendors

## Self Assessments

In the previous section we went over self assessments and probably one of the most important things you can do is ensure that you have guidance surrounding these so engineers can assess security issues at a glance. These can also help foster communication when individuals may have questions about a given control. If you empower engineering with the knowledge you possess through documenting self assessment criteria, the more you can spend your time on higher risk components within the organization. 

## Company Specs

To be embedded within the organization you must adopt your organizations processes. Often times organizations will have specs documented and processes to follow when embarking on a task. Are you including security within them? Documentation? Contacts?

- Documentation
- Ticketing system
- Template document
- Processes

