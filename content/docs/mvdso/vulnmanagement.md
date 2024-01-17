---
title: "Vulnerability and Dependency Management"
description: ""
lead: ""
date: 2022-12-03T12:48:57-0800
lastmod: 2022-12-03T12:48:57-0800
draft: false
images: []
menu:
  docs:
    parent: "defenseindepth"
weight: 240
toc: true
---

Vulnerability and package management entail identifying, assessing, and mitigating against vulnerabilities in your organizations systems and applications. Vulnerability identification and remediation can often help further efforts of [cattle vs. pets](https://www.hava.io/blog/cattle-vs-pets-devops-explained) within your infrastructure as it favors repeatability and iteration.

It is a thankless endeavor that takes years of maturity to actively manage. As well as limited open source options including [Greenebone OpenVas](https://www.openvas.org/) and [Wazuh](https://documentation.wazuh.com/current/user-manual/capabilities/vulnerability-detection/how-it-works.html). Often times organizations go with paid products such as Nessus, AWS Inspector, Qualys, and Rapid7. This is often recommended for server management as the market has consolidated to a point where maturity is key. As organizations move towards containers, open source projects are thriving.


## Container Scanning

As we shift focus to containerized architectures, the base operating system has a significantly reduced attack surface compared to the applications running upon them. Which is why shifting vulnerability scanning to containers is so crucial as you adapt your program. Scanning containers for issues both locally, part of the software development lifecycle, and running in production has never been a more available process for everyone involved. Here are some examples of some tooling you can leverage when scanning your containers for vulnerabilities. 

- [Clair](https://github.com/quay/clair)
- [Trivy](https://github.com/aquasecurity/trivy)
- [Docker Scan](https://docs.docker.com/engine/scan/)

## Successful Program

It is crucial in your vulnerability management program to establish thresholds, prioritize fixes when impactful, track progress of fixes, and communicate with stakeholders within your program. Here are some tips for when you are establishing a program and ensuring it is successful in the long run. 

- Involve the relevant stakeholders
- Establish clear goals and objectives
- Develop a plan and established SLAs
- Regularly review and update the program
- Automate as much as possible
- Conduct regular training with engineering

## Dependency Management

At one point in your career you may have used `pip` for Python, Maven for Java, `npm` for JavaScript, and `go mod` for Go. Performing an inventory, checking for vulnerabilities, and checking licenses can be a difficult task across an organization. Here are some examples of tools that can help you with that endeavor. 

- [Dependabot](https://github.com/features/security)
- [Snyk](https://snyk.io/)
- [OWASP dependency-track](https://github.com/DependencyTrack/dependency-track)
- [Bomber](https://github.com/devops-kung-fu/bomber)
