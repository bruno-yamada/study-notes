# Security Product Lifecycle 101
> https://mckinsey.udemy.com/course/secure-product-lifecycle-101/

# Foundations of Security
- C.I.A 
  - Confidentiality 
    - information access only given if authorized
	- control who can access
  - Integrity 
    - information cannot be modified or destroyed improperly
	- trusted to be accurate and authentic
	- control who can change
  - Availability
    - Information is reliably accessed and can used when needed
	- ensure it is available when needed
C.I.A. can be implemented at differente levels for each of its concepts, based on the solution needs

# Security Culture & Shift Left
Security can often be de-prioritezed, due to deadlines and development workflows, often being pushed to last stage

There is a common chart from IBM that states that software defects are more and more costly the further it is in the software development lifecycle (design, implementation, testing, maintenance)

__Shift left__ is a fundamental concept in DevSecOps, it is to push security requirements and testing earlier(left) in the product lifecycle as to reduce its cost, getting then fixed as early as possible

# Implementing security, risk calculation and impact

Its aim is to protect __assets__ from __threats__ that take advantage of __vulnerabilities__
- asset: documents, sensitive files, systems
- threat: things that can cause damage to assets
- vulnerabilities: things that can be exploited

__Risk__ is the likelyhood of a vulnerability being exploited by a hacker(threat), damaging an asset

In risk management in can commonly be assessed as `risk = likelyhood x impact`, so you can try to prioritize risks by score, and try and focus on reducing likelyhood or impact based on the risk

Possible actions for responding to a risk:
- remove the risk
- reduce likelyhood
- reduce impact
- share responsability (insurance, or use a provider)
- accept and monitor (if no other option)

# Data Governance
To protect data so it is available, usable, consistent and secure

# Data Privacy
Whether data about people is being collected, used, shared and removed the correct way

Differs based on the type of data being collected, thus it is useful to classify the type of data being collected to assess the right actions

It is important to find the proper law being applied to your data

Common law standards for data privacy and governance:
- GDPR (General Data Protection Regulation)
- CCPA (California Consumer Privacy Act)
- LGPD (brazillian law: Lei Geral de Proteção de Dados Pessoais)

---
> known security model: OWASP SAMM
# OWASP SAMM highlighed Principles
## least privilege
An user or process is given the lowest level of access for the shortest amount of time that is necessary
- vulnerabilities in one service is less likely to affect another service
- reduces attack surface
- reduces damaged caused if accident occurs
- easier to test
process:
- understand and identify required permissions
- start with minimum and add access as required
- routinely re-assess privileges 

## defense in depth
The layering of multiple defenses and controls across a system, also referred to as `layered security`
- It prevents having a single point of failure
- identify multiple layers of controls
- choose controls athat are not similar and operate independently
So you add multiple security measures, so that if one fails you are still somewhat protected, e.g.:
- add control mechanisms (authorization)
- add encryption at rest
- add logging and monitoring for suspicious behaviour

## fail secure / fail safe
for a system to remain in a safe state after failure

Going back to our trinity CIA (Confidentiality, Integrity, Availability), if a product loses confidentiality(fails), it does not lose integrity or availability(fails safe)

Example:
- When an ATM fails, it shuts down, it does not spills money freely

## complete mediation
All access to an object must be verified

It is useful because:
- ensure systems avoid making assumptions about authorization
- forces the use of mechanisms to verify all requests
- only immediate requests are impacted if compromised

Must be considered at the product design stage

All requests must be verified, if the requester has acccess to the requested resource, without exception

## session management
Concerrns securing a system's communication channels

It is the practice of properly managing keys, passwords, secrets and accounts across all process that makes use of it

It is useful because:
- incorrect management can lead to stolen keys, password, secrets and accounts

Should be considered during the design phase

eg.:
- storage of session ID in a secure cookie
- JSON web tokens

## open design
The security of a system should not rely on its design being a secret
- the opposite is to rely on "security through obscurity"

> "Design systems under the assumption that the enemy will immediately gain full familiarity with them" - Shannon's Maxim

Benefits:
- gain confidence of the security in the system (anyone can check)
- more opportunities to identifiy vulnerabilities
- shift the priority to actually implementing security rather than increasing obscurity

Implementation is easy:
- Do not rely on a system's design secrecy for security

## psychological acceptability
Making it as easy as possible for a user to act securely

Secure mechanisms cannot get in the way of the user, as they might be inclined to avoid the mechanism entirely

Security systems should be:
- transparent and open
- present as little disruption as possible
- user-friendly

It envolves trade-offs between `security controls` and `user experience`

# OWASP Top 10
> OWASP = Open Web Application Security Project

__OWASP Top 10__:
- lists the 10 most critical security risks in web applications
- created by professionals
- aimed at developers
- requested/required by stakeholders

## 1. Injection
Untrusted data is injected through a request or command, granting access to unauthorized assets

Common in systems using SQL, LDAP, OS Commands, XML Parsers, Expression languages, XPath, NoSQL, ORM Queries

Prevent:
- use secure APIs
- server side input validation
- special character escaping

## 2. Broken Authentication
Incorrecly implemented session or user authentication mechanisms

E.g.:
- attacker trying to login using leaked, compromised credentials

Prevent:
- MFA
- Maximum password attempts
- Authentication lockout

## 3. Sensitive data exposure
Commonly simply not encrypting sensitive data

Prevent
- data classification
- do not store that you do not need
- robust encryption at rest
- salted hashed stored passwords

## 4. XML external entities

## 5. Broken Access Control

## 6. Security misconfiguration
## 7. Cross-site scripting (XSS)
## 8. Insecure deserialization
## 9. Using components with known vulnerabilities
## 10. Insufficient logging and monitoring

---

Risk Calculation and Impact
How to mitigate and control risk

Data governance and privacy
