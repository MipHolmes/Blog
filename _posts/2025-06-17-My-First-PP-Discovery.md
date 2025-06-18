---
title: "My First Prototype Pollution Discovery"
date: 2025-06-17T18:44Z
categories:
  - blog
tags:
  - client-side exploit
  - web app security
---

In my very first blog post I'll be providing an overview of a finding during a recent offensive engagement. 

When my performs the latter services, the scope typically consists of vuln assessment (VA), internal and external network, social engineering, and the basis of this post, web. 

The client utliizes a Y2K-era website: basic HTML, significantly unmaintained frameworks, and of course a monolithic architecture. I figured this would be a goldmine for classic web app vulns—SQLi, XSS, you name it. I was wrong. Days went by and I had no successful exploits. From this point, I took the "low-hanging fruit" route and analyzed several CVEs that a ZAP scan flagged.

Among one of these were a prototype pollution (PP) vulnerability due to an outdated jQuery library. 

But what is prototype pollution?


```javascript
$.extend(true, {}, JSON.parse('{ "__proto__": { "attacker": "yes" } }' )); 
console.log({}.attacker);
```
