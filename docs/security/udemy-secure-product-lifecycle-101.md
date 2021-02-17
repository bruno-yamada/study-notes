# Security Product Lifecycle 101
> https://mckinsey.udemy.com/course/secure-product-lifecycle-101/

Common product lifecycle:
`design` > `implementation` > `testing` > `maintenance`

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
- XXE (XML External Entities)
- when XML is accepted without verification, can result in attacks through the XML parser
- opt to use json instead, or the latest version of XML parser

## 5. Broken Access Control
- When users can access data they are not authorized to
- e.g.
  - users accessing another user's data by modifying the URL (/user/1/sensitive.txt > /user/2/sensitive.txt)
- avoid by denying access to resources by default
- log user attempts

## 6. Security misconfiguration
- Leaving default accounts or passwords
- lack of hardening
- unecesary features enabled
- not turning on security features

## 7. Cross-site scripting (XSS)
- common in 2/3 of web applications
- when a hacker injects website with malicious scripts
- e.g.
  - a website allows a user to openly host uncheck html or js
  - a hacker uploads js scripts to exploit another user visiting the site
  - a user visit the site, it loads the malicious script and the script can steal user credentials, session, etc
- to avoid:
  - use Content Security Policy
  - context-sensitive encoding
  - escape untrusted HTTP data
  - use a trusted framework with features built-in

## 8. Insecure deserialization
- When a resource upload being insecurely deserialize results in an attack
- try to avoid by only allowing primitive data types

## 9. Using components with known vulnerabilities
- Common due to out-of-date or unsupported software
- to avoid:
  - remove unused dependencies
  - use only verified maintained software
  - patch management process
    - continuosly monitoring

## 10. Insufficient logging and monitoring
- if not enought logging or monitoring is put in place, an attack can go unnoticed, resulting in no incident response
- to avoid:
  - implement audit trails
    - log logins attempts and sensitive transactions
  - real time monitoring and alerting
  - understandable error messages
  - investigate suspicious activity

---

# Tools and Techniques

## Product risk profiles
To evaluate the risk level of a product

Each product must have the impact of it being compromised estimated

helps:
- Understand risk applied to a product
- Understand impact to an organization
- Compare risk between products
- Allocate resources properly

Tools:
- questionnaire to be answered by the product to classify product (e.g. low, meidum, high)

## Security requirements
- To capture all security requirements for a given product, given its CIA requirements, risk profile, etc
- it is __not to capture the mechanisms__, but only the objective
requirements e.g.
- All data must be encrypted at rest
- All user activity must be logged

mechanisms are how the product aims to achieve the above

helps:
- avoid last minute issues, by pushing requirements early in the development cycle
- allows alignment with business needs for security

Allocate time to indentify and document, use a framework can be easier
they must be: well defined, specific, measurable and well-documented

## Threat modeling
- To identify, measure and respond to a threat
- Brainstorm possible threads with all team-members
helps:
- reduces vulnerabilities in products due to involvement of all team-members, thus covering more possible threats

Known open thread model:
- Microsoft STRIDE

## Architecture assessment
Processes that ensure an application's infrastructure meet security requirements
- Architecture Validation
  - verify architecture used and ensure appropriate controls are applied throughout
- Architecture Mitigation
  - to review the architecture in order to identify misconfigurations, insecure assumptions and missing security components

helps:
- identify insecure choices
- requires proper architect documentation
- verify security approaches taken

It should be a continual process as architecture is changed regularly

Must be carried out by people with proper knowledge

## Encryption in transit and at rest
Data is:
- at-rest: stored data, data warehouses, databases, documents
- in-use: data in non-persistent state, e.g. cache
- in-transit: data being transmitted or in flow

Encryting data at-rest protects against physical and logical threats, should the store be compromised, the data is encrypted

Encryting data in-transit, to avoid man-in-the-middle attacks or sniffing

AES (Advanced Encryption Standard) - Common standard for encryption at-rest
TLS (Transport Layer Security) - Common standard for encryption in-transit

## Security logging and monitoring
Complimentary to one another

To know what happened, when, in what way, by which users and systems

To record, detect and respond
- must collect enough information to detect suspicious behaviour
- must monitor and respond to threats, e.g. alerting
- analyze the data to avoid false positives, incomplete data, missing or duplicated data

Logs not scanned or alerts without responses might as well not exist

## Penetration testing
Simulated attack on a system (with permission)

Referred to as: pentesting and ethical hacking

- Whitebox
  - __full information__ is given about the system
- Blackbox
  - __no information__ is given about the system
- Graybox
  - __partial information__ is given about the system

Helps:
- identify overlooked vulnerabilities
- act as validation of implemented security

It is common to define "rules of engagement":
- legal and regulatory rules
- technical boundaries
- which area to test or avoid
- which type of testing should be conducted
- timing and duration

Findings through the pentesting must be reviewed, a vulnerability can be accepted or fixed and another pentest can occur
 
## Source code management (SCM)
To track changes and updates to code repositories

Helps:
- retain history of changes(audit trail)
- prevent conflict between developers
- allows code reviews

The SOM it self should be properly hardened, managed and secured

- never include secret or credentials in SCM
- configure proper access to repositories and SCM integrations
- audit code imported in the tool

## CI/CD tools
Continuous Integration + Continous Delivery|Deployment

Facilitates ongoing automation and regular updates, by levering automation

Helps:
- automate testing, scanning, reviews
- increase speed and adoption of security due to automation

Automated checks and tools should also be continuosly reviewed 

## Secret scanning tools
To scan a code base to identify leaks of credentials, keys, passwords, tokens, etc

Better to be done pre-commit or in a way it can be entirely removed from SCM

Actions:
- calibrate the scanning tool to avoid false positives
- use the tool regularly and remove identified secrets
- know that only the scanning tool is not enough

## Software composition analysis (SCA)
To track and scan a product's components and dependencies
- Flags security issues brought by dependencies
- Highlight known vulnerabilities to the development team
- Can suggest autofix by suggesting patching of a dependency
- Can be integrated throughout the entire product lifecycle

## Static application security testing (SAST)

## Dynamic application security testing (DAST)

## Interactive application security testing (IAST)

## Section 3 and course summary

---
Risk Calculation and Impact
How to mitigate and control risk
Data governance and privacy
