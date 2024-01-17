---
title: "Supply Chain"
description: ""
lead: ""
date: 2022-12-03T12:48:57-0800
lastmod: 2022-12-03T12:48:57-0800
draft: false
images: []
menu:
  docs:
    parent: "patching"
weight: 260
toc: true
---

Reducing supply chain risks is becoming more crucial than ever as risks continue to grow. As open source projects thrive, the locations in which you receive your software can be at significant risk dependant on the security of the supply chain. These should apply to your machine images, docker images, and dependencies.  

## Machine Images

When building out your machine image pipeline, you should see if you can perform the following. This will help you limit risks in your supply chain when building out machine images. 

- Use trusted base images
- [Verify the integrity of the AMI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-identity-documents.html)
- Ensure you are leveraging TLS when retrieving packages
- Regularly update your AMIs
- Check signatures and checksums on packages when available
- Mirror packages when possible on your own infrastructure

## Docker Images

The same can be true when building out docker images and similar processes should follow.

- Use official images
- Maintain a list of internally approved images
- Verify signatures and checksums of images
- Sign images built in your pipeline
- Mirror packages that are required as part of the build
- Regularly update your images
