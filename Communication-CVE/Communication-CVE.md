---
title: Critical CVE Notifications
---

## Document Classification

This document is only for internal use of Wortell Managed Detection and Response.

## Critical CVE Notifications

To ensure effective collaboration between the Managed Service team and the Secure team, and to safeguard our clients’ environments, we follow the procedure below for identifying and communicating critical security vulnerabilities (CVEs):

**Purpose**:
To provide timely and consistent communication to internal teams and customers regarding critical vulnerabilities that could impact our core systems and client environments.

**Guidelines**:

1: CVE Monitoring

* The Secure team continuously monitors newly published CVEs and assesses their relevance and potential impact.

2: Scope – Core Business Components

* The following are considered core business components:
* Financial systems
* Microsoft products (e.g., Windows, Office, Exchange)
* Citrix
* SAP
* Linux distributions
* Other business-critical infrastructure components, defined by the MDR Hunting Team.

3: Communication Threshold
Any CVE with a CVSS score of 9.5 or higher affecting the components listed above must be communicated immediately.

4: Communication Process

* The Secure team will notify the Managed Service team via the internal communication channel as soon as a relevant CVE is identified.
* Additionally, this information will be published on the customer portal so clients are promptly informed about potential risks and recommended mitigation actions.

5: Objective of This Communication

* Proactively identify and mitigate security risks
* Provide transparent and timely information to customers
* Enable efficient collaboration between internal teams

## E-mail communication

Notify the Principal Engineers Services, Stephanie Antoniuk, and Maurice Blok about critical application vulnerabilities.

* Subject: [Critical CVE Notification] – [Application Name] Vulnerability Identified
* To: `<principalengineers@wortell.nl>`; `<compliance@wortell.nl>`; `<Maurice.Blok@wortell.nl>`

## Template example

Title:
[Application Name] – Critical Vulnerability Identified (CVSS [Score])

Description:
A new vulnerability has been identified in [application/component name], which is classified as a critical security risk. This vulnerability impacts one of our core business components and may pose a significant threat if not addressed promptly.

Details:

CVE ID: [CVE-XXXX-XXXX]

Severity: Critical (CVSS [Score])

Affected Systems: [List affected platforms, e.g., Microsoft Exchange, SAP, Citrix, Linux, etc.]

Summary: [Brief 2-3 sentence description of the vulnerability and potential impact]

Recommended Actions:

Immediate: [e.g., Apply the latest security patch provided by the vendor.]

Short-Term: [e.g., Monitor affected systems for unusual activity and review access logs.]

Long-Term: [e.g., Consider additional hardening measures and update internal documentation.]

If available: KLQ Detection.

Note: Only onboarded objects can be identified, monitored, or hunted in relation to this CVE or vulnerable application.
