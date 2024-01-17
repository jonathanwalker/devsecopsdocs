---
title: "Rego"
description: "Policy language used by Open Policy Agent(OPA) to define policies against different formats such as json, yaml, hcl, etc."
lead: ""
date: 2020-10-06T08:49:15+00:00
lastmod: 2020-10-06T08:49:15+00:00
draft: false
images: []
menu:
  docs:
    parent: "iac"
weight: 311
---

Rego is a policy language used by Open Policy Agent(OPA) to define policies against different data formats such as json, yaml, hcl, etc. An ideal tool for evaluating structured data you will encounter often as an engineer. The declarative language allows individuals to implement security, compliance, governance, and best practices evaluations all in one language. Rego is often used in many static code analysis tools for that reason. In this exercise you will learn how to put this into practice.

## Learning Rego

Learning rego can be a daunting task but a vital one to mature your understanding of static code analysis within the infrastructure security space. The best resource to learn rego in my opinion is [OPA Policy Authoring](https://academy.styra.com/courses/opa-rego) by Styra. 

## Usage

To get started in understanding how to use rego, let's take a look at a very simple [example](https://play.openpolicyagent.org/p/yV2XphNbYb) on the [Rego Playground](https://play.openpolicyagent.org). Below contains the json we want to evaluate in which we want to test if the input type is `user` and the name is `admin`.

```json
{
  "type": "user",
  "name": "admin"
}
```

The policy below is what we are using to test the above json to see if the input type is `user` and the input name is `admin`. It will default to `false` if either or is not the case and `true` if both are true. The `input` variable accesses the json document.

```json
package example

default allow = false

allow {
  input.type = "user"
  input.name = "admin"
}
```

## Utilities

There are a few utilities to query data using rego and in the below examples we will leverage [Open Policy Agent](https://www.openpolicyagent.org/) and [Conftest](https://www.conftest.dev/)

### Open Policy Agent

Now lets take what we have learned from above and use rego. To install OPA be sure to visit the [get started](https://www.openpolicyagent.org/docs/v0.11.0/get-started/) guide on the Open Policy Agent documentation or use `brew install opa`. Save the above files as `policy.rego`, `data.json`, and then run the command `opa eval -d policy.rego -i data.json "data"`

```bash
$ opa eval -d policy.rego -i data.json "data"
{
  "result": [
    {
      "expressions": [
        {
          "value": {
            "example": {
              "allow": true
            }
          },
          "text": "data",
          "location": {
            "row": 1,
            "col": 1
          }
        }
      ]
    }
  ]
}

$ opa eval -d policy.rego -i data.json "data.example.allow" | jq '.result[].expressions[].value'
true

$
```

### Conftest

Conftest has a wide variety of [examples](https://www.conftest.dev/examples/) in their [repository](https://github.com/open-policy-agent/conftest/tree/master/examples) which I would strongly recomment reviewing and learning from. Lets start with a simple example to fail if your deployment does not have an owner label. 

#### Structured Data

To give you an idea of what you can query when it comes to rego with structured data here are some examples of what you can query using the rego language. 

- Docker compose
- Kubernetes
- Kustomize
- Dockerfile
- HCL
- JSON
- INI
- YAML
- XML


#### Deployment

Save this file as `deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
  # labels:
  #   owner: test
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-container
        image: my-image
```

Save this file as `policy.rego`

```json
package main

deny[msg] {
	input.kind = "Deployment"
	not input.metadata.labels["owner"]
	msg = "You must have app label"
}
```

Then run the command `conftest test deployment.yaml --policy policy.rego` to start evaluating your deployment file. 

```bash
$ conftest test deployment.yaml --policy policy.rego
FAIL - deployment.yaml - main - You must have app label

1 test, 0 passed, 0 warnings, 1 failure, 0 exceptions
```

Now uncomment lines 5-6 adding the owner label to your deployment.

```bash
$ conftest test deployment.yaml --policy policy.rego

1 test, 1 passed, 0 warnings, 0 failures, 0 exceptions
```

#### Utilization

You might be thinking, well this is great but how would I put this into action? You can leverage a [GitHub Action](https://github.com/YubicoLabs/action-conftest) or within [Atlantis](https://www.runatlantis.io/docs/policy-checking.html).

https://github.com/YubicoLabs/action-conftest
