---
title: "Nuclear Pond"
description: "Perform internet wide scans for far less than a cup of coffee."
date: 2023-01-03T10:33:11-0800
lastmod: 2023-01-03T10:33:11-0800
draft: false
weight: 10
categories: ["scanning"]
tags: ["nuclei", "s3", "athena", "lambda", "scanning", "sql"]
contributors: ["Jonathan Walker"]
pinned: false
homepage: false
---

When new security research is out, the spin up time to scour assets for vulnerabilities can be a long and tedious task. Spending time learning about the latest findings, how to exploit them, and what conditions are required in order to exploit them. How can you stay on top of it all when it is a constant battle repeating itself? 

That is the exact problem projects like [Nuclei](https://github.com/projectdiscovery/nuclei) are made for; To help researchers identify known issues through a powerful templating language to ensure you do not miss out. It can also help you identify issues such as known subdomain takeovers, exposed panels, network services, misconfigurations, exposed files, and the list goes on. 

It does an outstanding job when you have a limited number of assets and are performing scans individually. Though the moment you have hundreds or thousands of assets, scaling vertically is no longer an option. 

Performing scanning at scale is exactly what [Nuclear Pond](https://github.com/DevSecOpsDocs/nuclearpond) is meant to achieve. You can launch thousands of scans without having to worry about cost, waiting for extended periods of time, and customize how many scans you want to perform in parallel for far less than a cup of coffee. Once the scans are complete you can choose to upload them to S3 for querying with Athena or just view the output as if it were running on your own machine. These scans can launch hundreds of instances of Nuclei all at the same time on the cheapest compute available on AWS with Lambda. 

![Nuclear Pond](infrastructure.png "Nuclear Pond Infrastructure.")

Scans can help you visualize your attack surface and vulnerabilities such as:

- Identify assets vulnerable to subdomain takeovers
- Provide a comprehensive identification on exposed assets such as Jenkins, Elasticsearch, Grafana, and many more
- Locate exposed files, logs, configurations, backups, etc.
- Misconfigurations that can lead to information disclosure
- Network services such as ssh, ftp, telnet, mysql, etc.
- Known vulnerabilities in exposed assets
- Published CVEs that can be easily exploited

### What is Nuclei

Nuclei is a tool that allows you to create configuration files to validate a wide variety of security issues. Contributing to [Nuclei](https://github.com/projectdiscovery/nuclei/pull/3088) and [Nuclei Templates](https://github.com/projectdiscovery/nuclei-templates/pull/6440) is a breeze. When running nuclei, you can specify what security issues you are trying to identify and launch a scan against your hosts. This can often be slow on your local machine even with rate limits and concurrency settings configured. While they have done an outstanding job creating a fast scanner, doing so at scale can be difficult. Lets take a deep dive into each component of Nuclei. 

### Nuclei Templates

[ProjectDiscovery](https://projectdiscovery.io) maintains the repository [nuclei-templates](https://github.com/projectdiscovery/nuclei-templates) which contains various templates for the nuclei scanner provided by them and the community. Contributions are welcome and straight forward to add to based on previous examples and you can reference my [pull request](https://github.com/projectdiscovery/nuclei-templates/pull/6440) to get a sense of just how easy it is. 

### Nuclei Usage

This is to help you understand what scans you can perform with Nuclei and the options available to you. 

- Filter by tags `-tags takeover` allows you to identify known takeovers
- Filter by templates directory `-t dns` executes all templates under dns directory
- Filter by specific template `-t dns/detect-dangling-cname.yaml`
- Exclude by tags `-etags cve` excludes searching for vulnerabilities

#### Detecting Network Services

Here is an example in which we want to enumerate some protocols running on [scanme.nmap.org](http://scanme.nmap.org) with the [network detection templates](https://github.com/projectdiscovery/nuclei-templates/tree/main/network/detection). This will help us identify network services running on the specified host. This allows us to currently check over 40 different network based services such as ssh, smtp, mysql, mongodb, telnet, etc. 

```log
$ nuclei -u scanme.nmap.org -t network/detection

                     __     _
   ____  __  _______/ /__  (_)
  / __ \/ / / / ___/ / _ \/ /
 / / / / /_/ / /__/ /  __/ /
/_/ /_/\__,_/\___/_/\___/_/   v2.8.3

		projectdiscovery.io

[INF] Using Nuclei Engine 2.8.3 (latest)
[INF] Using Nuclei Templates 9.3.2 (latest)
[INF] Templates added in last update: 57
[INF] Templates loaded for scan: 42
[INF] Targets loaded for scan: 1
[openssh-detect] [network] [info] scanme.nmap.org:22 [SSH-2.0-OpenSSH_6.6.1p1 Ubuntu-2ubuntu2.13]
```

#### Detecting Takeovers

Takeovers can be a common occurrence when you manage thousands of zones within your infrastructure and mistakes certainly occur in which deprecating assets may not complete in the correct order or completely. This can lead to dangling assets that can be taken over by an attacker. The repository [Can I take over XYZ](https://github.com/EdOverflow/can-i-take-over-xyz) is an excellent resource if you want to learn what the current landscape looks like at this time. 

Nuclei currently has over 70 different templates to detect if you are currently vulnerable to a takeover and here is an example as to how check to see if a domain is vulnerable. 

```log
$ nuclei -u https://jsdkjfskjsfdkdjfds.s3.amazonaws.com/ -tags takeover

                     __     _
   ____  __  _______/ /__  (_)
  / __ \/ / / / ___/ / _ \/ /
 / / / / /_/ / /__/ /  __/ /
/_/ /_/\__,_/\___/_/\___/_/   v2.8.3

		projectdiscovery.io

[INF] Using Nuclei Engine 2.8.3 (latest)
[INF] Using Nuclei Templates 9.3.3 (latest)
[INF] Templates added in last update: 238
[INF] Templates loaded for scan: 74
[INF] Targets loaded for scan: 1
[INF] Templates clustered: 69 (Reduced 68 HTTP Requests)
[aws-bucket-takeover] [http] [high] https://jsdkjfskjsfdkdjfds.s3.amazonaws.com/
```

## Nuclear Pond

Think of Nuclear Pond as just a way for you to run Nuclei in the cloud. You can use it just as you would on your local machine but run them in parallel and with however many hosts you want to specify. All you need to think of is the nuclei command line flags you wish to pass to it. 

### Setup & Installation

To install Nuclear Pond, you need to configure the backend [terraform module](https://github.com/DevSecOpsDocs/terraform-nuclear-pond). You can do this by running `terraform apply` or leveraging [terragrunt](https://terragrunt.gruntwork.io/). After your backend infrastructure has been created, it's time for you to install `nuclearpond`.

```bash
$ go install github.com/DevSecOpsDocs/nuclearpond@latest
```

#### Command line flags

The most important flags are as follows. These are flags for `nuclearpond run` which must proceed each of the following.

- `-b` which commands how many hosts per invocation(number of hosts / batches = nuclei lambda invocations)
- `-o` flag allows you to specify outputs such as `cmd` for the output of Nuclei and `s3` for data lake analysis of findings
- `-c` flag allows you to specify how many threads to invoke lambda (1 is the default but >10 is recommended at scale)
- `-a $(echo -ne "-t dns" | base64)` flag allows you to pass `-t dns` to Nuclei
- `-f` is your backend function name and `-r` is your region you have deployed the function to
- The environment variables below can replace `-f` and `-r`
  - `export AWS_REGION=us-east-1`
  - `export AWS_LAMBDA_FUNCTION_NAME=test-nuclei-runner-function` 

### Architecture

The backend configures a Lambda function which includes the Nuclei binary within a [layer](https://github.com/DevSecOpsDocs/terraform-nuclear-pond/blob/main/main.tf#L32-L39) which is located in `/opt/nuclei`. The function accepts an [event](https://github.com/DevSecOpsDocs/terraform-nuclear-pond/blob/main/src/main.go#L21-L25) with your targets, arguments, and output type. Nuclear Pond allows you to invoke this lambda function by taking your targets, arguments, and output in parallel by splitting up your targets into batches. 

- Maximum execution time of fifteen minutes which if combined with `-rl` and `-c` Nuclei flags should not be an issue
- You should avoid using the `-o file.txt` as that file remains in lambda
- Since the input includes [raw flags being sent to exec](https://github.com/DevSecOpsDocs/terraform-nuclear-pond/blob/main/src/main.go#L71) it is vulnerable to RCE
- Be careful making modifications to the infrastructure, such as adding network interfaces, as it could increase risk

### Basic Scan

While I strongly recommend against not including filters for your scan and running it against all templates, it can be done within a couple of minutes with `-rl 1000 -c 50` which can potentially bring down your target. So use caution and always make sure you have permission to do so. This tool is primarily built for a targeted approach. 

```
$ nuclearpond run -t devsecopsdocs.com -a $(echo -ne "-rl 1000 -c 50 -silent" | base64) -o cmd
  _   _                  _                           ____                        _
 | \ | |  _   _    ___  | |   ___    __ _   _ __    |  _ \    ___    _ __     __| |
 |  \| | | | | |  / __| | |  / _ \  / _` | | '__|   | |_) |  / _ \  | '_ \   / _` |
 | |\  | | |_| | | (__  | | |  __/ | (_| | | |      |  __/  | (_) | | | | | | (_| |
 |_| \_|  \__,_|  \___| |_|  \___|  \__,_| |_|      |_|      \___/  |_| |_|  \__,_|

                                                                  devsecopsdocs.com

2023/01/03 10:15:07 Running nuclei against the target devsecopsdocs.com
2023/01/03 10:15:07 Running with 1 threads
2023/01/03 10:17:02 Scan complete with output:

[aws-cloudfront-service] [http] [info] https://devsecopsdocs.com
[aws-bucket-service] [http] [info] https://devsecopsdocs.com
[xss-deprecated-header] [http] [info] https://devsecopsdocs.com [1; mode=block]
[robots-txt-endpoint] [http] [info] https://devsecopsdocs.com/robots.txt
[nameserver-fingerprint] [dns] [info] devsecopsdocs.com [ns-1309.awsdns-35.org.,ns-1822.awsdns-35.co.uk.,ns-487.awsdns-60.com.,ns-579.awsdns-08.net.]
[s3-detect] [http] [info] https://devsecopsdocs.com/%c0
[tls-version] [ssl] [info] devsecopsdocs.com [tls13]

2023/01/03 10:17:02 Completed all parallel operations in 1m54.972950992s , best of luck!
```

### Data Lake Output

This output is recommended when leveraging Nuclear Pond as once the script invokes, all of the work is handed off to the cloud for you to analyze another time. This output is known as `s3` and you can output it by specifying `-o s3`. You can also specify `-l targets.txt` and `-b 10` to invoke the lambda functions in batches of 10 targets per execution.

```
$ nuclearpond run -t devsecopsdocs.com -a $(echo -ne "-t dns -silent" | base64) -o s3

  _   _                  _                           ____                        _
 | \ | |  _   _    ___  | |   ___    __ _   _ __    |  _ \    ___    _ __     __| |
 |  \| | | | | |  / __| | |  / _ \  / _` | | '__|   | |_) |  / _ \  | '_ \   / _` |
 | |\  | | |_| | | (__  | | |  __/ | (_| | | |      |  __/  | (_) | | | | | | (_| |
 |_| \_|  \__,_|  \___| |_|  \___|  \__,_| |_|      |_|      \___/  |_| |_|  \__,_|

                                                                  devsecopsdocs.com

2023/01/03 10:22:12 Running nuclei against the target devsecopsdocs.com
2023/01/03 10:22:12 Running with 1 threads
2023/01/03 10:22:13 Saved results in s3://test-nuclei-runner-artifacts/findings/2023/01/03/18/nuclei-findings-cd17c344-ec06-48da-96d6-728debf01c57.json
2023/01/03 10:22:13 Completed all parallel operations in 1.165510457s , best of luck!
```

### Scanning at Scale

Now lets run Nuclear Pond as it was intended to do, at scale on a significant amount of targets. Here I have around 500k targets, decided to batch 2k targets per execution with `-b 2000`, and run 200 individual threads locally with `-c 200` to run lambda functions asynchronously. 

[![XKCD](https://imgs.xkcd.com/comics/log_scale.png#center "XKCD image Log Scale.")](https://xkcd.com/1162/)

```log
$ nuclearpond run -l ~/Desktop/500k.txt -a $(echo -ne "-t dns/mx-fingerprint.yaml -rl 1000 -c 50 -silent" | base64) -o s3 -b 2000 -c 200
  _   _                  _                           ____                        _
 | \ | |  _   _    ___  | |   ___    __ _   _ __    |  _ \    ___    _ __     __| |
 |  \| | | | | |  / __| | |  / _ \  / _` | | '__|   | |_) |  / _ \  | '_ \   / _` |
 | |\  | | |_| | | (__  | | |  __/ | (_| | | |      |  __/  | (_) | | | | | | (_| |
 |_| \_|  \__,_|  \___| |_|  \___|  \__,_| |_|      |_|      \___/  |_| |_|  \__,_|

                                                                  devsecopsdocs.com

2023/01/03 10:25:51 Running nuclear pond against 580880 targets
2023/01/03 10:25:51 Splitting targets into 291 individual executions
2023/01/03 10:25:51 Running with 200 threads
2023/01/03 10:25:58 Saved results in s3://test-nuclei-runner-artifacts/findings/2023/01/03/18/nuclei-findings-dd51d033-40fa-46ba-80a1-6b95803aed18.json
...
2023/01/03 10:26:27 Saved results in s3://test-nuclei-runner-artifacts/findings/2023/01/03/18/nuclei-findings-0745ab34-1c2e-451b-8736-8aea93a5ae41.json
2023/01/03 10:26:27 Completed all parallel operations in 36.215408938s , best of luck!
```

### Retrieving Findings

To explore your findings in Athena all you need to do is perform the following query! The database and the table should already be available to you. You may also have to configure query results if you have not done so already. Once you are comfortable with querying Athena, it would be best to move over to help you visualize your results such as grafana. 

```sql
select
  *
from
  nuclei_db.findings_db
limit 10;
```

![Athena](athena.png "Athena query results.")

### Advance Query

In order to get down into queries a little deeper, I thought I would give you a quick example. In the select statement we drill down into `info` column, `"matched-at"` column must be in double quotes due to `-` character, and you are searching only for high and critical findings generated by Nuclei.

```sql
SELECT
  info.name,
  host,
  type,
  info.severity,
  "matched-at",
  info.description,
  template,
  dt
FROM 
  "nuclei_db"."findings_db"
where 
  host like '%devsecopsdocs.com'
  and info.severity in ('high','critical')
```

## Infrastructure

The backend infrastructure, all within [terraform module](https://github.com/DevSecOpsDocs/terraform-nuclear-pond). I would strongly recommend reading the readme associated to it as it will have some important notes. 

- Lambda function
- S3 bucket
  - Stores nuclei binary
  - Stores configuration files
  - Stores findings
- Glue Database and Table
  - Allows you to query the findings in S3
- IAM Role for Lambda Function

## Conclusion

What are you waiting for? Get started today. Contributions are welcome and looking forward to seeing issues created!

- [Nuclear Pond](https://github.com/DevSecOpsDocs/nuclearpond)
- [Nuclear Pond Backend Terraform Module](https://github.com/DevSecOpsDocs/terraform-nuclear-pond)
