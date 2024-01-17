---
title: "Patching"
description: ""
lead: ""
date: 2022-12-03T12:48:57-0800
lastmod: 2022-12-03T12:48:57-0800
draft: false
images: []
menu:
  docs:
    parent: "vulnerability"
weight: 250
toc: true
---

In the past patching used to involve a long drawn out process in which you would schedule a system for maintenance, perform updates, reboot, and hope everything was operational when complete. There were tools that helped you avoid reboots, automate updating system packages, and schedule periods of maintenance during this time but as the industry has adapted; So should your patching process. You should be treating your infrastructure as replaceable components that can be rebuilt and deployed at a moments notice. This involves regularly maintaining your machine and container images. 

## Tooling

Fostering repeatable, resilient, iterative, and standardizing infrastructure should be a top priority to ensure your patching process is as seamless as can be. Making sure you have built out the process to build machine and container images is crucial and integrating into these can be of significant benefit. Focus on maturing out these tools and having regular replacement of your underlying infrastructure to ensure you are agile enough to perform patching processes. 

- Packer
- EC2 Image Builder
- Docker
- Vagrant

### Automation

When leveraging these tools you can also insert best practices within the build process, encompass vulnerability scans within the build process, benchmark tests, and add additional security tooling to assist in your endeavors. 

- [Lynis](https://github.com/CISOfy/lynis)
- [EC2 Image Builder stig](https://docs.aws.amazon.com/imagebuilder/latest/userguide/toe-stig.html#linux-os-stig)
