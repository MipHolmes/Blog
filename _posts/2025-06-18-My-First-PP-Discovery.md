---
title: "My First Prototype Pollution Discovery"
date: 2025-06-18T18:44Z
categories:
  - blog
tags:
  - client-side exploit
  - javascript
---

In my very first blog post, I'll be providing an overview of a finding during a recent offensive engagement. 

When my team performs the latter services, the scope typically consists of vuln assessment (VA), internal and external network, social engineering, and the basis of this post, web. 

The client utilizes a Y2K-era website: basic HTML, significantly unmaintained frameworks, and of course a monolithic architecture. I figured this would be a goldmine for classic web app vulns—SQLi, XSS, you name it. I was wrong. Days went by and I had no successful exploits. From this point, I took the "low-hanging fruit" route and analyzed several CVEs that a ZAP scan flagged.

Among these was a prototype pollution (PP) vulnerability due to an outdated jQuery library. 

## <ins>What is prototype pollution?</ins>

Prototype pollution (PP) is a vulnerability when an attacker injects arbitrary data into a JavaScript object prototype. This generally occurs when JavaScript functions fail to sanitize object keys before merging them with another object, resulting in a modified prototype that the object inherits from. Consequently, this can lead to authentication bypasses, DoS, and logic bombs (all in an effectively chained attack, likely with XSS). We need to know what objects, properties, and prototypes are to better understand this vuln and its implications. Casually speaking, an object is an item you create in JavaScript, e.g., Shirt, which contains any number of *properties*. Properties are attributes the object has, e.g, size, material, color. Prototypes are special toolkits that objects inherit their properties and methods from (object.prototype). 

There are three factors essential for this vulnerability to be exploited: A user-controllable input, a sink, and a weak gadget.
 
・User-controllable input: URL, text forms, JSON input, etc.

・Sinks: These are functions or DOM elements that allow you to inject or "merge" malicious properties, i.e., where the pollution occurs. For example:

```javascript
if (user.isAdmin) {
  showAdminPanel();
}
```

We can make ourselves admins, allowing us to see the admin panel:

```javascript
{ "__proto__": { "isAdmin": true } }
```
・Weak Gadgets: Pieces of application code (or third-party libraries) that use polluted properties in dangerous ways, usually without verifying where the property came from (own vs inherited).


## <ins>CVE-2019-11358: Vulnerable jQuery Merge</ins>

The vuln centers on jQuery's .extend() method. When used in this manner:

```javascript
$.extend(true, {}, userInput);
```
Thus, userInput is JSON-parsed from a user-controllable resource such as a request body. This is because in jQuery versions <3.4.0, extend() did not block PP via the __ proto __ key.

## <ins>My Proof-of-Concept</ins>

So I know my pollution vector is jQuery.extend(true, {}, ...), and my sink is located in the source code, but where can users control input in the application? How do I identify a weak gadget? I tried the URL, login and 'contact us' forms, but no success. Identifying a gadget eventually faded from the picture because of being timeboxed (gotta love compliance-driven pen testing). So I cut corners and decided to inject input into the developer console for a basic demonstration of polluting the application's prototype. 

```javascript
$.extend(true, {}, JSON.parse('{ "__proto__": { "attacker": "yes" } }' )); 
console.log({}.attacker);
```

Evaluates

```javascript
>>> yes
```

The output served as confirmation that the prototype of all plain objects was polluted. I also received a runtime error — Uncaught TypeError: cannot use 'in' operator to search for "set" in "yes." The runtime error was a subtle clue, indicating that internal code paths were interacting with the polluted prototype. If time allowed, this would be the thread to pull on to locate an actual gadget.

Let's understand the payload I used:

1) JSON.parse('{ "__proto__": { "attacker": "yes" } }')

   - Parses a JSON object with a __ proto __ key:

{
  __proto__: {
    attacker: "yes"
  }
}

2) $.extend(true, {}, ...)
   - $.extend() is a jQuery deep-merge function.
   - With true, it performs a deep merge, recursively copying properties.
   - The {} target is merged with the parsed object.

3) What actually happens:
   - The prototype of all objects is polluted by assigning to __proto__, which is an accessor property that allows you to access or modify the prototype of an object, which determines its inheritance chain.
   - {}.attacker → inherited from Object.prototype.

4) console.log({}.attacker)
   - Logs "yes" — indicating the prototype pollution succeeded.



PP is like pouring a can of food coloring into a pond — a seemingly minor action with wide-reaching, hard-to-contain consequences. A polluted prototype silently alters the behavior of objects app-wide, often in ways developers don’t anticipate.
