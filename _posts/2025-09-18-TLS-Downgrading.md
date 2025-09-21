---
title: "TLS Downgrading"
date: 2025-09-18T23:34:00
categories:
  - blog
---




### Background

TLS (Transport Layer Security), the successor to SSL, is a protocol that should be implemented in your environment, wherever applicable. Fundamentally, it is the "protector" of information between HTTP traffic. 

### PoC

Using the powerful (but noisy) attack surface discovery tool, Nuclei, I run an extremely simple script to extract all resources associated with a domain:

```
nuclei -target [domain]
```

While parsing the output, I notice that at least 5 different endpoints are using a TLS version below the current, 1.3. Using OpenSSL, I can verify this finding with the following command, which will show the negotiated protocol version and cipher suite:

<img width="775" height="579" alt="Screenshot 2025-09-20 at 8 11 09 PM" src="https://github.com/user-attachments/assets/d97ceaa0-abf3-4445-9560-14576ec5ee1a" />


### Impact & Recommendations

Deprecated TLS versions in use in production environments can leave resources vulnerable to well-known downgrading attacks such as BEAST, POODLE, and Lucky13. Leaving a connection, intended to be private, can lead to data theft. Moreover, the use of outdated TLS and weak cipher suites is a significant security risk and violates modern compliance requirements.

To mitigate this, you should immediately restrict TLS versions to 1.2 and above. Additionally, remove deprecated cipher suites from the server configuration. To ensure ongoing security, regularly test exposed services using industry-standard tools like testssl.sh, nmap --script ssl-enum-ciphers, or sslyze to confirm that your environment remains secure.

### Fin
