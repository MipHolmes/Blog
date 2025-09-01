---
title: "My First DEFCON Experience"
date: 2025-07-21T03:08Z
categories:
  - blog
tags:
  - Red Team
  - Appsec
  - Cloud
  - Social Engineering
---

DEF CON 33 was the first one I've attended; This was also my first time visiting Las Vegas. I enjoy exploring new places, and I love attending security conferences. 

This post won't focus predominantly on the vacation aspects of this experience or miscellaneous events during the conference, but rather will be an attempt to share the three most impactful sessions I attended.  


## We Know What You Did (in Azure) Last Summer

This was the very first talk that I attended, and needless to say, I'm glad I did so. Many organizations rely on Azure as their IT environments are deployed using the service, either hybrid or fully cloud-hosted. As a pen tester (and IT consultant and auditor), I've encountered Azure, though I have yet to simulate a critical exploit such as data exfil or priv esc to a global administrator from an offensive perspective. Like most CSPs, Azure offers a fair share of services; Testers will often find themselves sifting through "noise" when conducting Azure pentests as a result of this. 

Many Azure resources have subdomain names that can be enumerated. Their naming conventions can be attributed through DNS hostname enumeration, though this does not always prove the exact ownership of these resources. If a tester can properly enumerate a target's environment whether it's through certificate transparency reports, bruteforcing with wordlists to find public DNS records, or with open source solutions like Google Cache or VirusTotal, they may uncover Azure Tenant IDs. The tenant ID is a unique string that 

Consequently, the NetSPI team has developed a Python tool to enumerate and attribute Azure resources. "For every resource that it finds attribution for, the tool stores a timestamped record in a local sqlite database (azure_tenants.db). The tool also allows for CSV, JSON, and HTML output of the results."




## Hacking the First Amendment: A press photographer's perspective on Red Team scenarios

This comedic talk brought my attention to the various vulnerabilities that exist within large events and how simple it is to exploit them.

## Breaking the CI/CD Chain: Security Risks in GitHub Actions

Software Supply Chain Security has been a topic of focus since 2022 and won't be dissipating anytime soon, especially given CVE-2025-30066. 

Hardcoded Secrets & Poor Secret Handling:
3rd Party Actions:
Code Injection:
Misuse of pull_request_target: 


The speaker goes on to mention a solution that's been developed 

## Honorable Mentions

- Metasploit’s Latest Attack Capability and Workflow Improvements
- K8sploitation: Hacking Kubernetes the Fun Way
