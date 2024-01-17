---
title: "Self Assessments"
description: "Minimum Viable DevSecOps 1.2 Self Assessments."
lead: ""
date: 2020-10-06T08:48:57+00:00
lastmod: 2020-10-06T08:48:57+00:00
draft: false
images: []
menu:
  docs:
    parent: "communication"
weight: 130
toc: true
---

Self Assessments are a great way of not only improving your own security posture, they can also enable engineers to assess their own work in order to build security within the product. This often involves systematic and comprehensive review held against a given standard. This section will help you create an environment of self assessments within your organization. 

## Documentation

Best practices guidelines are great for you to document what processes you expect individuals to follow when carrying out a given objective. These are a great point of reference and help increase velocity amongst the organization by codifying guidelines. Engineers might questions about how to navigate internally and the more documentation you have as a security team; The more likely it is for them to stumble across security focused guidelines when performing their function. In which you can influence how they would interact, increase velocity through already approved methods, and help avoid some common mistakes individuals can make. Do you have the below guidance materials?

- Internal tooling
- Customer facing sites
- Domains for internal/external uses
- Subdomains
- AWS/Azure/GCP
- Docker
- Kubernetes
- Git
- GitHub/GitLab

## Shift Left

Shift Left is a common term leveraged when you implement activities and tasks earlier in the development cycle. This can involve integration, unit, security, and performance testing during the process of development. This is one of the core fundamentals of DevSecOps and should be at the core of what you try to achieve. Below are some good examples of how you can start to shift left in your processes. 

- [JUnit](https://github.com/junit-team/junit5)
- [Mocha](https://github.com/mochajs/mocha)
- [JMeter](https://jmeter.apache.org/)
- [SonarQube](https://github.com/SonarSource/sonarqube)
- [GoSec](https://github.com/securego/gosec)
- [Bandit](https://github.com/PyCQA/bandit)
- [TFSec](https://github.com/aquasecurity/tfsec)
- [Kics](https://kics.io/)

### Actions

I would strongly recommend the use of automation to perform assessments before code is merged into your main branch. This can be done through GitHub/GitLab Actions, Git pre-commit hooks, and through your software development lifecycle automations. If performed locally or through proposals through your vcs platform choice, it can help speed up velocity in making quality and secure decisions earlier on in the development cycle. 

Advise is best served as early as possible in order to have the greatest impact and reward individuals for doing the right thing. Fixing an issue through a visual and engaging way can go a long way in helping you fix issues before they come to fruition. 

#### Third Party Actions

Below are some good of how you can achieve some of the above through GitHub actions. 

- [sonarqube](https://github.com/marketplace/actions/sonarqube-scan)
- [tfsec](https://github.com/marketplace/actions/tfsec-action)
- [kics](https://github.com/marketplace/actions/kics-github-action)
- [jmeter](https://github.com/marketplace/actions/apache-jmeter)
