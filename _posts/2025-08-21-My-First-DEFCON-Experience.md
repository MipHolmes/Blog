---
title: "My First DEFCON Experience"
date: 2025-07-21T03:08Z
categories:
  - blog
tags:
  - Appsec
  - Cloud
  - Red Team
  - Social Engineering
---

DEF CON 33 was the first one I've attended; This was also my first time visiting Las Vegas. I enjoy exploring new places, especially those far from where I reside and I love attending security conferences. It truly was an unforgettable experience. 

This post won't focus predominantly on the vacation aspects of this experience or miscellaneous events during the conference, but rather will be an attempt to share the three most impactful sessions I attended.  


## We Know What You Did (in Azure) Last Summer

**Problem**: Enumerating Azure resources and attributing them to organizations is noisy and difficult.

This was the very first talk that I attended, and needless to say, I'm glad I did so. Many organizations rely on Azure as their IT environments are deployed using the service, either hybrid or fully cloud-hosted. As a consultant, I've encountered Azure, though I have yet to simulate a critical exploit such as data exfil or priv esc to a global administrator from an offensive perspective. Like most CSPs, Azure offers a fair share of services; Testers will often find themselves sifting through "noise" when conducting Azure pentests as a result of this. 

Many Azure resources have subdomain names that can be enumerated. Their naming conventions can be attributed through DNS hostname enumeration, though this does not always prove the exact ownership of these resources. If a tester can properly enumerate a target's environment, whether it's through certificate transparency reports, bruteforcing with wordlists to find public DNS records, or with open source solutions like Google Cache or VirusTotal, they may uncover Azure Tenant IDs. The tenant ID is a unique string that refers to a specific organization and is what the authentication framework, OpenIDConnect, refers to.

```https://login.microsoftonline.com/{tenant-id}/.well-known/openid-configuration```

 We, as attackers, have no certainty as to who to attribute particular Azure resources to, with every resource having a different enumeration technique (e.g., subdomain enumeration for Key Vaults, SharePoint, and Storage Accounts, or directory enumeration for Azure DevOps) and attribution methods through HTTP headers (WWW-Authenticate for Key Vault and Storage Accounts, x-report-to for SharePoint). Specially crafted HTTP responses will reveal the tenant ID in the response, but from an attacker's perspective, we have no way to attribute this value to an organization.

 Fear not, this request parameter returns the organization associated with the supplied tenantID when queried in Microsoft Graph:

 ```https.graph.microsoft.com**/v1.0/tenantRelationships/findTenantInformationByTenantId(tenantId='<value>)**```
 
Consequently, the NetSPI team has developed a Python tool to enumerate and attribute Azure resources at scale. "For every resource that it finds attribution for, the tool stores a timestamped record in a local SQLite database (azure_tenants.db). The tool also allows for CSV, JSON, and HTML output of the results." The HTML output option provides a neat interface that can serve as a helpful discovery and attribution tool for engagements.

**Takeaway**: With tools like NetSPI’s Python script, attribution can be automated at scale. Defenders should monitor for abnormal enumeration patterns and review exposure of tenant information.

## Hacking the First Amendment: A press photographer's perspective on Red Team scenarios

**Problem**: Large event security relies heavily on perceived trust rather than robust validation.

This comedic talk brought my attention to the various vulnerabilities that exist within large events and how simple it is to exploit them. Despite being low-tech, I still walked away with useful physical and human pentesting techniques I wasn't formerly aware of, in addition to a unique perspective on interpreting identity and access management concepts.

Mansoor Ahmad draws on previous experience working as a press photographer, detailing how to bypass security in media sections of crowded events. 

OSINT is the first and most crucial step in achieving this exploit. Red teamers should start with social media analysis on their target: attendees with special badges (our "authentication credential") often inadvertently expose the convention of these passes. With good photoshop skills and a font identifier such as Font Squirrel (https://www.fontsquirrel.com/matcherator), you can spoof the badge, completing half the battle.

The 2nd half of this attack chain involves blending in with other presspeople or those with privileged access and convincing security that you're one of them. Additional considerations for the former are what the specific venue is, who the audience is, and your purpose of being there. Knowing these will allow you to better emulate privileged access individuals. Convincing perimeter security will involve mastering the consensus, trust, and familiarity. Ahmad mentions that marketing professionals in entertainment often don't pass on free promo, and security personnel have two concerns, which are basic authentication (checking the ID) and crowd safety. Given this, if you can confidently act as if you're supposed to be somewhere, getting in is attainable. 

**Takeaway**: Physical security and IAM principles overlap. Social engineering simulations should extend to event security and VIP access areas.

## Breaking the CI/CD Chain: Security Risks in GitHub Actions

**Problem**: GitHub Actions workflows expand supply chain attack surfaces.

Software Supply Chain Security has been a focus topic since 2022 and won't dissipate anytime soon, especially given [CVE-2025-30066]((https://www.cve.org/CVERecord?id=CVE-2025-30066)). This CVE hit the popular tj-actions/changed-files GitHub Action earlier this year. This Action is widely used across thousands of repos to detect modified files in PRs, but a flaw in how it handled untrusted input opened the door to arbitrary command execution inside workflows. In the wrong hands, that meant attackers could potentially steal secrets, tamper with builds, or even escalate privileges depending on how the workflow was configured.

The maintainers have since patched the issue, but the incident was a reminder of just how fragile CI/CD supply chains can be. Something as seemingly benign as a helper Action can ripple into a critical vulnerability if permissions and trust boundaries aren’t carefully managed. It set the stage perfectly for the “Breaking the CI/CD Chain” talk, which dug deeper into the broader risks of GitHub Actions.

Hardcoded Secrets & Poor Secret Handling: Common developer pitfall; This accounts for most CI/CD exploits considering divulged secets is synonomous with environment takeover.

3rd Party Actions: This involves either a dependency error or compromised 3rd party actions in production. Lapses in actions verification can lead to this.

Code Injection: Untrusted user input directly interpolated into shell commands.

Misuse of pull_request_target: This privileged event is invoked from specific workflows which then give write access to the repository. I imagine threat actors have a field day with this misconfiguration.

The speaker mentions a solution that her firm developed: ActChain. ActChain serves as a GitHub Actions Security Scanner which can spot vulns, misconfigs, compliance violations, and other issues within GitHub Actions workflows. 

**Takeaway**: Adopt tools like ActChain, enforce signed actions, restrict workflow permissions, and run automated scans during CI/CD reviews.

## Honorable Mentions

- Metasploit’s Latest Attack Capability and Workflow Improvements
- K8sploitation: Hacking Kubernetes the Fun Way
