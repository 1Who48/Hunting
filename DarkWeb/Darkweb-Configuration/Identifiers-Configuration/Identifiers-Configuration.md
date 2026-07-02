---
Title: Identifiers Configuration
---

:::warning
THIS DOCUMENTATION IS STILL IN DEVELOPMENT AND SUBJECT TO CHANGE.
:::

## Document Classification

This document is only for internal use of Wortell Managed Detection and Response.

## **Identifiers Configuration Manual**

Identifiers configuration are always inline with the customer what they want to be monitored. Everty object is a identifier in Flare.
We as wortell always take the domain and the Azure AD tenent for monitoring, the rest of the Identifiers is inline with the customers.

## **1. Select Identifier Type**

Flare supports various identifier types to search for specific activities:

- **Domain**: Searches for activities related to a domain name.
  - Examples: `foo.com`, `bar.foo.com`, `foo.com.au`
  - Icons: Resolves (DNS translates to IP), Reachable (connection established with IP)

- **Name**: Searches for people-related activities using first and last names.
  - Optional: Strict mode for exact matches

- **Email**: Searches for activities related to email addresses.
  - Examples: Leaked passwords, mentions on clear and dark web

- **Keyword**: Searches for activities related to specific keywords.
  - Examples: Organization's name, brand names

- **Azure Tenant**: Searches for events containing mentions of an Azure Tenant.
  - Useful for detecting secrets in configuration files

- **BIN**: Searches for activities related to credit card numbers.
  - Examples: `52588`, `52588[0-1]`

- **IP Address**: Searches for activities related to IP addresses.
  - Examples: `172.1.1.35`, `172.1.1.0/24`

- **Query**: Uses Lucene query syntax for custom searches.
  - Examples: Regex patterns, field filters

- **GitHub Repository**: Monitors activities related to GitHub repositories.
  - Specify Repository Owner and/or Repository Name

- **Username**: Searches for mentions of an individual's username.
  - Examples: Clear web and dark web mentions

- **Password**: Monitors mentions of a known password.
  - Examples: Leaked passwords discussions

## **2. Select Categories**

Limit the identifier to specific categories to avoid false positives:

- **Domain**: Web Accounts disabled by default
- **Name**: Look-alike Domains, Leaked Credentials, Hosts, Web Accounts disabled by default
- **Email**: Look-alike Domains, Hosts, Web Accounts disabled by default
- **Keyword**: Look-alike Domains, Leaked Credentials, Hosts, Web Accounts disabled by default
- **IP Address**: Look-alike Domains, Leaked Credentials, Web Accounts disabled by default
- **Query**: Look-alike Domains, Leaked Credentials, Web Accounts disabled by default
- **Password**: Look-alike Domains, Hosts, Web Accounts disabled by default
- **BIN**: Look-alike Domains, Leaked Credentials, Hosts, Web Accounts disabled by default
- **Username**: All categories except Web Accounts disabled by default
- **GitHub Repository**: Web Accounts disabled by default

## **3. Select Severity**

Choose the minimal alert severity to filter out less critical events:

- Refer to the documentation for details on severity scoring.

## **4. Ignore Terms**

Filter out events containing specific terms to reduce noise:

- Example: Exclude ad-block lists on GitHub by specifying document titles or repository names.

## Inportend note Ignore Terms

- We as Wortell always included the *.domain from the customer, else all subdomains will discover meaning that the identifiers will fill-up.

## **5. Add to Group**

Organize identifiers into groups for better management:

- Create a group via the Identifiers tab -> click on Create Group -> name the group -> add identifiers.

## **6. Alerts**

Set up alerts directly in Alert Central when adding or modifying an identifier:

- Refer to the documentation for more details on alerts.

For more details, visit the official documentation: [Flare.io Identifiers-Configuration](https://docs.flare.io/identifiers).
