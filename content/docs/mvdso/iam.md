---
title: "Identity and Access Management"
description: "Usage of single sign on, credentials, u2f, etc.."
lead: ""
date: 2022-12-03T12:48:57-0800
lastmod: 2022-12-03T12:48:57-0800
draft: false
images: []
menu:
  docs:
    parent: "defenseindepth"
weight: 220
toc: true
---

There is not shortage of breaches as a result of hard coded credentials in git leveraged for exfiltrating data from an organization. As a DevSecOps engineer, it is your responsibility to empower engineers to make the right decisions in avoiding such scenarios. 

- Leverage Single Sign On(SSO) when human interaction is expected
- Create service accounts per application
- Utilize Principal of Least Priviledge(PoLP) often
- Avoid static credentials where possible
- Encrypt secrets if static credentials are required
- Defense in Depth for static credentials

## Static Credentials

You should avoid static credentials as much as possible in favor of short lived sessions. Though often you will find yourselves in situations where that is not possible such as database credentials, vendor tokens, api keys, etc. 

### Encryption

When you need static credentials and require them to be inside of version control systems or cloud providers, it is best to encrypt them prior to doing so. One of the best tools to do so is [Mozilla Sops](https://github.com/mozilla/sops). While encrypting secrets locally is an excellent tool at your disposal, you should really be storing encrypted secrets in tools that can allow you to audit retrieval and manage them such as vault, aws secrets manager, etc. 

```
brew install sops
echo "secret: secretvalue"
sops --kms "arn:aws:kms:us-east-1:000000000000:key/fe86dd69-4132-404c-ab86-4269956b4500" secret.yaml
sops -d secret.yaml.enc
```

### Detection

Finding secrets within your git repository can be a time consuming task especially if there was not already a culture of security. Here are some tools you can leverage. 

- [TruffleHog](https://github.com/trufflesecurity/trufflehog)
- [Yelp detect-secrets](https://github.com/Yelp/detect-secrets)
- [Git secrets](https://github.com/awslabs/git-secrets)
- [Gitrob(Archived)](https://github.com/michenriksen/gitrob)

Paid alternatives that provide a more user friendly way of doing so. 

- [GitHub Secret Scanning](https://docs.github.com/en/code-security/secret-scanning/about-secret-scanning)
- [Gitleaks](https://github.com/zricethezav/gitleaks)
- [GitGuardian](https://www.gitguardian.com/)

#### TruffleHog

Leveraging TruffleHog can be an excellent first step, especially after their rewrite allowing for higher signal findings. Below are some examples as to how you can do so. 

```
trufflehog github --repo=https://github.com/trufflesecurity/trufflehog.git
```

```
trufflehog github --org=trufflesecurity
```

## OIDC

OpenID Connect(OIDC) is an authentication protocol that is built on top of the OAuth 2.0 framework. OIDC is quickly becomming the standard when it comes to authenticating users and applications through an identity provider. This can be through Okta, AWS, GitHub, GCP, Facebook, Google, etc. Throughout this section we will break down OIDC as well as the practical use cases for leveraging OIDC. 

### Practical Uses

There is not shortage of practical uses for OIDC but a great example of doing so is creating authentication proxies for your internal services within your organization. This can allow you to setup single sign on to your application without any additional functionality. Another mechanism which is commonly leveraged is IAM roles for Kubernetes service accounts which is the AWS recommended way to retrieve IAM STS tokens for your pods.

- [Authenticating users through AWS Application Load Balancers](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/listener-authenticate-users.html)
- [AWS IAM roles for Kubernetes service accounts](https://docs.aws.amazon.com/eks/latest/userguide/iam-roles-for-service-accounts.html)
- [GitHub OIDC authentication for AWS IAM Roles](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-amazon-web-services)
- [Authenticating users in Jenkins](https://plugins.jenkins.io/oic-auth/)


#### Flow

1. Application is assigned a unique client ID and secret
2. Application sends a request to the identity provider to with the scope and url
3. Identity provider validates the request and sends back the OIDC token
4. Application leverages the OIDC token to perform actions
5. Token expires and process repeats itself

#### Token

Below contains an example JWT token which can be base64 decoded into the following information. The token is delimited by `.` into three segments. The header, payload, and signature. 

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

- The alg claim in the header specifies that the token was signed using the `HS256` (HMAC with SHA-256) algorithm
- The sub claim in the payload specifies the user's unique identifier `1234567890`
- The name claim in the payload specifies the user's name `John Doe`
- The iat claim in the payload specifies the time at which the token was issued `1516239022`
