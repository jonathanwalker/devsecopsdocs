---
title: "Infrastructure as Code"
description: "How to empower engineers to assess the security of their infrastructure."
lead: ""
date: 2022-12-03T12:48:57-0800
lastmod: 2022-12-03T12:48:57-0800
draft: false
images: []
menu:
  docs:
    parent: "assessments"
weight: 140
---

Empowering different teams to iteratively improve upon infrastructure through code means security can be embedded in decision making through automation leveraging static code analysis tools. There are many options when it comes to Infrastructure as Code such as CloudFormation, Terraform, Pulumi, Terraform SDK, Heat, etc. There are also similarly as many tools to perform security static code analysis on the infrastructure it will create prior to applying. Leveraging these tools can empower the engineers who create them to ensure their work is resilient, efficient, and most importantly secure. These often catch mistakes before they happen so implementing these can help shift left. 

## Purpose

Infrastructure as Code(IaC) enables teams to iteratively improve upon infrastructure, collaborate with multiple engineers, avoids repeating yourself(DRY), and allows controls to be put in place to adhere to the organizations standards. Newcomers to the cloud will often wonder why should they spend more time and effort on creating infrastructure as code when they can instead click a button. The reason is always maintainability. When that individual leaves all of their work leaves with them and no iterative improvements can be made on top of that work. While one engineer can do outstanding work, a team can make the company thrive. 

### Success of Others

There is nothing more satisfying then creating re-usable code within Infrastructure as Code that allows others to build off of your successes. As you build them out, you create reusable components you can lift and shift to any scenario that needs it. Saving hours of time and effort. 

### Best Practices

It is common practice within many fields to build upon the success of existing ideas and concepts. In which engineers will often take examples from others and hopefully repeat the same triumphs that engineer made. Allowing your infrastructure to be created in a repository means individuals can mimic work that has already been done with success and empowers them to mimic quality work that has already been performed. Having examples in your repository is a great way for individuals to get started who work best off of existing examples. Which is your oppurtunity to insert best practices. 

## Options

There are plenty of options when it comes to Infrastructure as Code and picking one is often dependant on the skills you have within the organization. It is up to you as to which technology you wish to adopt. There are also advantages and disadvantages to each approach, so be sure to pick wisely. 

- [Terraform](https://www.terraform.io/)
- [AWS CloudFormation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html)
- [Crossplane](https://www.crossplane.io/)
- [CDK for Terraform](https://developer.hashicorp.com/terraform/cdktf)
- [Azure Resource Manager](https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/overview)
- [Google Cloud Deployment Manager](https://cloud.google.com/deployment-manager/docs)
- [Pulumi](https://www.pulumi.com/)
- [Heat](https://wiki.openstack.org/wiki/Heat)
- [Ansible](https://www.ansible.com/)

### DevOps Tooling

Helpful tools that assist you in managing infrastructure as code, keeping it DRY(Don't Repeat Yourself), and deploying.

- [Terragrunt](https://terragrunt.gruntwork.io/)
- [Terratest](https://terratest.gruntwork.io/)
- [Terraform Cloud](https://cloud.hashicorp.com/products/terraform)
- [Atlantis](https://www.runatlantis.io/)
- [CloudFormation Linter](https://github.com/aws-cloudformation/cfn-lint)
- [Terraform Linter](https://github.com/terraform-linters/tflint)

### Security Tooling

A list of tooling that goes into how to scan your infrastructure as code for security misconfigurations.

- [TFSec](https://github.com/tfsec/tfsec)
- [Regula](https://github.com/fugue/regula)
- [Kics](https://github.com/corneliusweig/kics)
- [CloudFormation Guard](https://github.com/aws-cloudformation/cloudformation-guard)
- [CFN_NAG](https://github.com/stelligent/cfn_nag)
- [Pulumi CrossGuard](https://www.pulumi.com/docs/guides/crossguard/overview/)
