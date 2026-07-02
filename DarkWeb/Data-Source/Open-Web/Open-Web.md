---
Title: Open Web
---

:::warning
THIS DOCUMENTATION IS STILL IN DEVELOPMENT AND SUBJECT TO CHANGE.
:::

## Open Web Monitoring

Handling Open Web in Flare.io

## Overview

The Open Web category includes data collected on various sources on the Internet, also known as "clear web".
For most Open Web sources, any Identifiers you create are used to run periodic searches. The Identifier data is disclosed to third-parties, but these are reliable platforms that support our collection, such as Shodan or GitHub.

## Key Features of Flare Open Web Monitoring

Flare's Open Web Monitoring provides visibility into multiple online sources to detect risks and protect organizations from cyber threats. The key monitored areas include:

## 1. Paste Sites Monitoring

Paste sites like Pastebin and similar platforms are commonly used by cybercriminals to share leaked data, credentials, and compromised information.

### How Flare Monitors Paste Sites

- Uses API integrations to track new public pastes.
- Crawls and indexes pastes for relevant keywords and identifiers.
- Alerts organizations when sensitive data is found.

## 2. Web Account Monitoring

Detects unauthorized mentions or impersonation of an organization across various online platforms.

### Key Benefits

- Identifies accounts using an organization’s name or brand.
- Helps prevent phishing, impersonation, or fraudulent use.
- Uses OSINT techniques similar to Sherlock for broader coverage.

## 3. Source Code Repository Monitoring

Publicly available source code repositories, like GitHub, often contain exposed secrets and sensitive company data.

### Monitoring Capabilities

- Scans repositories for API keys, credentials, and proprietary code.
- Monitors Stack Overflow for mentions of confidential topics.
- Notifies security teams when sensitive data is publicly exposed.

## 4. Google Dorking & Search Monitoring

Google dorks are advanced search queries used to find sensitive information indexed by search engines.

### Approach

- Automates Google dorking techniques to discover exposed assets.
- Provides a searchable archive of discovered information.
- Helps identify misconfigurations and publicly accessible sensitive files.

## 5. Host and Network Exposure Monitoring

Ensures that an organization’s IT infrastructure is not inadvertently exposed online.

### Features

- Uses Shodan integration to scan open ports and services.
- Monitors SSL certificate exposure and domain misconfigurations.
- Identifies potential risks associated with public-facing servers.

---

Understanding and monitoring illicit networks are vital for organizations aiming to protect their assets, reputation, and stakeholders from cyber threats originating from these hidden parts of the internet.

For more details, visit the official documentation: [Flare.io Open-Web](https://docs.flare.io/open-web).
