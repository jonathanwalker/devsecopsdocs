---
title: "Nuclei"
description: "Guides to help walk you through practices within the industry."
date: 2024-01-16T14:46:52-0600
lastmod: 2024-01-16T14:46:52-0600
draft: false
images: []
weight: 310
toc: true
---

Nuclei is a powerful, community-driven vulnerability scanning tool that simplifies vulnerability scanning for impactful findings. It allows security practitioners and developers to quickly identify vulnerabilities across different platforms and technologies. Please read our [blog](/blog/nuclear-pond/) on how one could potentially scale nuclei and some more use cases.

### Nuclei

Before using Nuclei, ensure you have downloaded and installed it. You can find the installation instructions and source code in the [Nuclei GitHub repository](https://github.com/projectdiscovery/nuclei).

#### Installation

{{< tabs "create-new-site" >}}
{{< tab "Go" >}}

```bash
go install -v github.com/projectdiscovery/nuclei/v3/cmd/nuclei@latest
```

{{< /tab >}}
{{< tab "macOS" >}}

```bash
brew install nuclei
```

{{< /tab >}}
{{< tab "Docker" >}}

```bash
docker pull projectdiscovery/nuclei:latest
```

{{< /tab >}}
{{< /tabs >}}

#### Usage

Nuclei is used via the command line and works with templates that define specific security checks. To scan a target, use a command like:

```bash
nuclei -u https://devsecopsdocs.com -t http/technologies
```

This command checks the target `https://example.com` against all the CVE (Common Vulnerabilities and Exposures) templates in Nuclei's template directory.

#### Findings

Nuclei presents its findings in a straightforward format, detailing the vulnerability type, the matched template, and the affected URL:

```bash
[2024-01-20 09:30:00] [cve-2023-XXXXX] [http] [critical] https://example.com/vulnerable-path
```

##### Example

```bash
$ nuclei -u https://devsecopsdocs.com -t http/technologies        

                     __     _
   ____  __  _______/ /__  (_)
  / __ \/ / / / ___/ / _ \/ /
 / / / / /_/ / /__/ /  __/ /
/_/ /_/\__,_/\___/_/\___/_/   v3.1.5

                projectdiscovery.io

[INF] Current nuclei version: v3.1.5 (latest)
[INF] Current nuclei-templates version: v9.7.4 (latest)
[WRN] Scan results upload to cloud is disabled.
[INF] New templates added in latest release: 6
[INF] Templates loaded for current scan: 604
[INF] Executing 603 signed templates from projectdiscovery/nuclei-templates
[WRN] Executing 1 unsigned templates. Use with caution.
[INF] Targets loaded for current scan: 1
[INF] Templates clustered: 224 (Reduced 215 Requests)
[fingerprinthub-web-fingerprints:openfire] [http] [info] https://devsecopsdocs.com
[aws-detect:aws-cloudfront] [http] [info] https://devsecopsdocs.com
[aws-detect:aws-kms] [http] [info] https://devsecopsdocs.com
[aws-bucket-service] [http] [info] https://devsecopsdocs.com
[aws-cloudfront-service] [http] [info] https://devsecopsdocs.com
[s3-detect] [http] [info] https://devsecopsdocs.com/%c0
[waf-detect:modsecurity] [http] [info] https://devsecopsdocs.com/
[waf-detect:cloudfront] [http] [info] https://devsecopsdocs.com/
```

### Template Customization

One of the strengths of Nuclei is its customizable template system, allowing users to define or modify checks for a wide range of scenarios.

#### Usage

Creating a custom template involves defining the request and the condition for a match. For example, a basic template to check for a version in a webpage could be:

```yaml
id: custom-check

info:
  name: Check Webpage for Version
  author: yourname
  severity: info

requests:
  - method: GET
    path:
      - "{{BaseURL}}/path-to-check"

    matchers:
      - type: word
        words:
          - "Version 1.0.0"
        part: body
```

This template checks if "Version 1.0.0" is present in the response body of the specified path.

#### Findings

If the condition is met, Nuclei will report the match:

```bash
[2024-01-20 09:45:00] [custom-check] [http] [info] https://example.com/path-to-check
```

Nuclei stands out as a versatile and efficient tool for vulnerability scanning. Its template-driven approach provides flexibility, allowing both rapid scanning with community templates and tailored checks with custom templates.
