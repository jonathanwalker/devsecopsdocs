---
title: "Signing Git Commits"
description: "How to sign git commits."
lead: ""
date: 2024-01-19T16:30:26-0600
lastmod: 2024-01-19T16:30:26-0600
draft: false
images: []
weight: 331
---

Signing commits is an important step to creating immutable proof of authorship within git. Doing so can improve your audit trail, mitigate risks associated to account takeovers, and protect against insider threats. Please see our blog post on [Seal Your Code through Git Commit Signing](/blog/seal-your-code-through-git-commit-signing/) for more detailed information. 


## Quick Start

A quick start to getting started to signing your commits.

{{< callout context="note" title="Note" icon="info-circle" >}} Be sure to install [GPG Suite](https://gpgtools.org/) before starting. {{< /callout >}}

```bash
# Pick your key properties and make sure you set the email the same as your git config `git config --get user.email`
gpg --full-generate-key
# grab key id after forward slash `rsa3072/C000AA111111B222`
gpg --list-secret-keys --keyid-format=long
# Insert it replacing the example keyid here
gpg --armor --export C000AA111111B222
# Add your public key to github in settings -> SSH and GPG Keys
git config --global user.signingkey C000AA111111B222
# Sign your first commit
git commit -S -m "first signed commit"
# verify your key and set all future commits to be signed
git config commit.gpgsign true
```

## Sources

For further reading and sources. 

- [Signing commits](https://docs.github.com/en/authentication/managing-commit-signature-verification/signing-commits)
- [Generating a new GPG key](https://docs.github.com/en/authentication/managing-commit-signature-verification/generating-a-new-gpg-key)
