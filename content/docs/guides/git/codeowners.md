---
title: "GitHub Codeowners"
description: "How to restrict access to modifying files in github."
lead: ""
date: 2024-01-19T16:30:26-0600
lastmod: 2024-01-19T16:30:26-0600
draft: false
images: []
weight: 331
---

GitHub has a concept called GitHub Codeowners that allows you to restrict whom has access to modify certain files within a repository. This can be useful when you have a highly valuable repository, high impact parts of your code, or have a set few individuals who would be best to perform certain code reviews. Setting up codeowners is a vital part of maturing your services to improve reviews and ensure appropriate approvals are made before merging. Use of Codeowners is generally something that developers support to help control ownership within their repositories, so promoting it's use is most likely all that's necassary outside of sensitive repositories. 

## CODEOWNERS

In order to get started with codeowners, you need to be familiar with the codeowners file. This file is located in `.github/CODEOWNERS` and must be created if you are just getting started.

- Comments
- Target file/folder
- Owners

### Example

Here is an example of what a codeowners file may look like.

```
# This is a comment
*.js @octocat
```

### Targets

- `*` includes everything in the repo
- `*.js` targets all JS files
- `/pkg/cmd/**` would target the pkg folder at the root of the repository and **any of it's subdirectories** within cmd
- `/docs/**/*.md` includes any markdown files within any of it's subdirectories
- `docs/*` will target any directory within the repository with the name of docs but **not any further nested files**
- `/docs/*` includes any files within the root docs directory within the repository
- `/*.*` includes all files in the root directory

### Owners

- `@octocat` restricts it to the octocat username
- `octo@example.com` restricts it to the user whom verified this email
- `@octo-org/octocats` restricts it to the octocats team in the octo-org

## Important Notes

- CODEOWNERS file is parsed from top to the bottom and **last match wins**
- Global requirements should be set at the top of the file
- You can test locally using [codeowners parser](https://github.com/sbdchd/codeowners)
