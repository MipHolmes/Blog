---
title: "Web App Pentesting and Bug Bounty Aren't the Same"
date: "2026-04-24T03:05Z"
categories: 
  - blog
tags:
  - appsec
--- 

After a year and a half of performing external network and black-box web app penetration tests, I've noticed a stark difference in the scopes for the latter two and bug bounty hunting. 

## The Difference

Think about the difference between internal network penetration tests and red team engagements. The two are distinct but often used interchangeably. Pentests assess known vulnerabilities, are usually compliance-driven, and have more stakeholder oversight. Red team engagements assess overall readiness, are broader in scope, and emphasize a full attack chain. Web app pentesting and bug bounty have a similar dynamic.

Web app pentesting can either be black-box (no credentials or source code provided), white-box (both provided), or gray-box, somewhere in between. Regardless of testing method, certain findings are standard deliverables: missing security headers, outdated or known-vulnerable JavaScript libraries, overly verbose error messages, and so on. Bug bounty is a different story. Programs are rigid about scope, and many of the findings that would appear in a professional pentest report are explicitly out of scope or classified as informational. Domino's responsible disclosure program is a good example of this in practice: [dominos.responsibledisclosure.com/hc/en-us/articles/115004505854-Scope-and-ROE](url).

What bug bounty programs actually pay out on are vulnerabilities with direct, demonstrable impact: broken object-level authorization, broken access control, checkout and billing manipulation, and sensitive information disclosure. They're looking for the heavy-hitters, the findings that prove you've meaningfully compromised a function the business depends on.

## What Does this Mean?

I've noticed the rigidity in Bug Bounty scopes, but never quite put two and two together. Nonetheless, it's made me realize that about half of the pentest findings in my day job would not be paid out if they were in bug bounties. Nothing to stress about, it just gives me the ambition to level up my skills. 


<img width="700" height="428" alt="image" src="https://github.com/user-attachments/assets/415b3310-7e14-4eb9-a473-7bcd71722ac2" />

> When you realize that you spent too much time on an informational vulnerability
