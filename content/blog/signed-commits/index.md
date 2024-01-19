---
title: "Seal Your Code through Git Commit Signing"
description: "How to sign your git commits."
date: 2024-01-19T12:23:06-0600
lastmod: 2024-01-19T12:23:06-0600
draft: false
weight: 10
categories: ["sdlc"]
tags: ["git", "sign", "signing", "gpg", "github", "commits"]
contributors: ["Jonathan Walker"]
pinned: false
homepage: false
---

All it takes is five minutes out of your day to get started using signed commits. Why not now? The following article goes over how to sign your commits, how to enforce signed commits, and everything along the way to help you with that journey. Here are some benefits to signing your commits:

- Signed commits verify that it comes from a trusted source/identified developer
- Create an auditable trail of who did what
- Safeguard against account takeovers of a developers account
- Enriches a security minded culture
- Allows enforcement of signed commits furthering trust the review/merge process
- Protects against insider threats mitigating impersonation risks
- Immutable proof of authorship

While this blog post focuses on GitHub, alternatives will have similar steps assuming they support enforcing/viewing signed commits. See GitLabs article on [Signing Commits in GitLab](https://docs.gitlab.com/ee/user/project/repository/signed_commits/gpg.html) if you leverage GitLab. 

## Quick Start

These steps go over all that is required to start signing your commits for those who have more important things to do, further more detailed instructions are below. 

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

### How to Sign Commits

Step by step guide on how to sign your commits that you should follow and help you navigate the steps along the way.

- Generate the key with `gpg --full-generate-key`
    - Press `Enter` to accept the default `(1) RSA and RSA (default)`
    - Press `Enter` to accept the default `What keysize do you want? (3072)`
    - Enter `5y` to specify five year length or press `Enter` to accept no expiration
    - Enter `Real name`
    - Get your `git config --get user.email` or else commits will be `Unverified`
        - Enter `Email address`
        - Get email from `git config --get user.email`
    - Enter `Comment`
    - Enter `O` for Okay
    - Set your password
- `gpg --list-secret-keys --keyid-format=long`
    - Copy the Key ID `rsa3072/C000AA111111B222` after the `/` and below the `---` which in this example is `C000AA111111B222`
- Export the public key
    - `gpg --armor --export C000AA111111B222`
- Add the public key to your github account
    - Select your profile picture
    - Click `Settings`
    - Click `SSH and GPG Keys`
    - Click `New GPG Key`
    - Name your key however you like
    - Paste the public key
    - Click `Add GPG Key`
- Set your git config signing key
    - `git config --global user.signingkey C000AA111111B222`
- Commit your first signed commit
    - `git commit -S -m "first signed commit"`
- Set all future commits to be signed
    - `git config commit.gpgsign true`

## Enforcing Signed Commits

{{< callout context="danger" title="Danger" icon="alert-octagon" >}}
Enforcing security processes typically take months of preparation and communication. Proceed with caution.

- Clear communication with users
- Setup monitoring controls first to gauge compliance
- Announce it organization/team wide
{{< /callout >}}

## GitLab

At this time enforcing signed commits is not possible as far as I am aware. See [Add option to only allow GPG signed merge requests](https://gitlab.com/gitlab-org/gitlab/-/issues/3737). The web UI supports viewing [signed commit messages](https://docs.gitlab.com/ee/user/project/repository/signed_commits/). There is a workaround by using a CI job in `.gitlab-ci.yml` below. 

```yml
verify_signed_commits:
  script:
    - |
      NOT_SIGNED=$(git log --pretty="format:%H %G?" $CI_COMMIT_BEFORE_SHA..$CI_COMMIT_SHA | grep ' N')
      if [ -n "$NOT_SIGNED" ]; then
        echo "The following commits are not signed:"
        echo "$NOT_SIGNED"
        exit 1
      fi
  only:
    - main
    - merge_requests
```

## GitHub Repository

To enforce this on a single branch within your repository, perform the following. Please note this is a breaking change. 

- Click on Settings in the repository menu
- Select `Branches` under code and automation
- Under `Branch protection rules` click on `Add rule` or edit an existing rule
- Select `Require signed commits`
- Save Changes

### GitHub Organization

You must have GitHub enterprise in order to do so and this can cause commits to break if all contributors do not currently have this configured.

- Click on Settings in the organization menu
- Go to `Repository` -> `Rulesets` under Code, planning, and automation
- Set enforcement status to active
- Add default to target 
- Require signed commits

## Platform Support 

During my research of adoption, using signed commits is not well established at this time. While the feature of signing commits is inherently a part of Git itself, it is not specific to any web-based version control interface. The ability of platforms like GitHub and GitLab to support and enforce signed commits is based on how they integrate this Git feature into their respective web interfaces and additional tooling. 

| Platform        | Supports Commit Signing | Enforce Signed Commits | Notes |
|-----------------|-------------------------|------------------------|-------|
| GitHub          | Yes                     | Yes                    | GitHub allows users to sign commits with GPG or S/MIME and offers branch protection rules to enforce signed commits. |
| GitLab          | Yes                     | No (Workarounds exist) | GitLab supports GPG-signed commits, but direct enforcement must be implemented via CI/CD pipelines or server-side hooks. |
| Bitbucket       | Yes                     | No                     | Bitbucket supports GPG-signed commits but does not have a built-in feature to enforce them. |
| Azure DevOps    | Yes                     | No                     | Azure DevOps supports commit signing, but there is no built-in enforcement mechanism. |
| AWS CodeCommit  | No                      | No                     | As of the last update, AWS CodeCommit did not support commit signing. |
| SourceForge     | Yes                     | No                     | SourceForge supports commit signing but does not have a feature to enforce signed commits. |

## Sources

For further reading and sources. 

- [Signing commits](https://docs.github.com/en/authentication/managing-commit-signature-verification/signing-commits)
- [Generating a new GPG key](https://docs.github.com/en/authentication/managing-commit-signature-verification/generating-a-new-gpg-key)
- [About Commit Email Addresses](https://docs.github.com/en/account-and-profile/setting-up-and-managing-your-personal-account-on-github/managing-email-preferences/setting-your-commit-email-address#about-commit-email-addresses)
