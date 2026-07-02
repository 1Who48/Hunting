---
Title: Integrating-Microsoft-Entra-ID
---

:::warning
THIS DOCUMENTATION IS STILL IN DEVELOPMENT AND SUBJECT TO CHANGE.
:::

## Document Classification

This document is only for internal use of Wortell Managed Detection and Response.

## **Integrating-Microsoft-Entra-ID**

**Overview**

Flare connects to Microsoft Entra ID through an app registration within your Azure tenant. This integration allows you to:

- Check if credentials flagged by Flare as potentially compromised are still active in Entra ID
- Provide direct links to corresponding users for remediation
- Enrich user identity profiles
- Support credential validation

To set this up, you’ll need certain permissions and configuration steps on both the Microsoft side and the Flare side.

## Prerequisites

- Access to the Azure Portal with sufficient privileges to create app re-gistrations, grant permissions, manage secrets.
- Flare account with access to Tenants configuration.
- Knowledge of your organization's policy regarding app secrets (expiration, naming, security).

## Microsoft Entra ID Setup

These steps are done in the Azure / Microsoft Entra ID environment.

1. App Registration

- Navigate to Azure Portal → Microsoft Entra ID → App registrations.
- Click New registration.
- Provide a descriptive name (e.g. flare entra id connector).
- Select Single-tenant (restrict access to accounts within your organization).
- Click Register.
- Copy and record the Application (Client) ID.
- Copy and record the Directory (Tenant) ID.

2. API Permissions

- In the app registration’s left-hand sidebar, go to API permissions.
- Click Add a permission.
- Choose Microsoft Graph as the API.
- Choose Application permissions (not delegated).
- Select Directory.Read.All (allows reading basic directory information across the tenant).
- Grant admin consent if required.

3. Certificates & Secrets

- Navigate to Certificates & secrets under the app registration.
- Under Client secrets, click New client secret.
- Give it a clear descriptive name (to indicate its purpose).
- Set the expiration period as per your internal security policy.
- Click Add.
- Copy the secret value (this will only be shown once).
- Store it securely.

## Flare Configuration

Once the Azure / Entra ID side is ready, configure Flare to use the credentials and IDs.

1. In Flare, go to the left sidebar → Configure → Tenants.
2. Choose the tenant for which you want to set up Microsoft Entra ID integration.
3. Click Entra ID configuration (often a button or tab in the top right):

- Enter the Application (Client) ID from Azure.
- Enter the Client Secret value.
- Enter the Directory (Tenant) ID.

4. Click Save to persist the configuration.

## Usage

After setup, Flare can use the integration for credential validation and remediation.

- Go to the Credentials Browser in Flare.
- Select the tenant feed (important: validation/remediation features are available per tenant).
- Choose a credential from the list → open its detail view.
- Click Validate.
  - The system will check whether the credential is still active in Microsoft Entra ID.
  - It will display the validation status (active / inactive etc).

Once validated, you can take remediation actions as needed (e.g. disable account, rotate credential).

For more details, visit the official documentation: [Flare.io microsoft-entra-id](https://docs.flare.io/microsoft-entra-id).
