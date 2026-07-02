---
Title: Microsoft Entra ID Integration
---

:::warning
THIS DOCUMENTATION IS STILL IN DEVELOPMENT AND SUBJECT TO CHANGE.
:::

## Document Classification

This document is only for internal use of Wortell Managed Detection and Response.

## Microsoft Entra ID Integration

This document provides step-by-step instructions to configure the integration between Flare and Microsoft Entra ID (Azure AD), so that Flare can validate credentials flagged as potentially compromised within your Entra ID tenant.

## Overview

Flare integrates with Microsoft Entra ID via a registered application in your Azure/Entra tenant.
Once configured:

* Flare can check whether credentials flagged as potentially compromised are still active in your Entra ID environment.
* If a credential is active, Flare provides a direct link to the corresponding user in Entra ID so that remediation can be taken.

At minimum, the integration requires Microsoft Graph API application permissions to read directory information (specifically, Directory.Read.All) so that user and directory data can be retrieved for enrichment and validation.

## Required Permissions & Prerequisites

Before you begin, ensure:

* You have sufficient privileges in your Azure/Entra tenant (e.g. Global Administrator or equivalent) to create application registrations, grant permissions, and manage enterprise applications.

* The app registration will require application-level Microsoft Graph permissions (not delegated).

* The permission Directory.Read.All must be granted and admin consented, so the app can read directory data.
docs.flare.io

* A client secret needs to be created in the app registration, and you must safely store the secret value (it won’t be shown again).

* You will need three key values from the Microsoft side:
    1. Client (application) ID
    2. Tenant (directory) ID
    3. Client secret

## Microsoft Side Configuration

3.1 App Registration

1. In the Azure / Microsoft Entra portal, go to Azure Active Directory > App registrations.
2. Click “New registration”.
3. Enter a descriptive name (e.g. Flare Entra ID Connector).
4. For Supported account types, choose “Single tenant” (so it’s restricted to your organization).
5. Click Register.
6. After registration, copy:
    * Application (client) ID
    * Directory (tenant) ID

These values will be used in Flare’s configuration. Treat them as sensitive.

3.2 API Permissions & Admin Consent

1. In the app registration’s left menu, click API permissions.
2. Click Add a permission.
3. Select Microsoft Graph → Application permissions.
4. Add the permission Directory.Read.All.
5.If required, click Grant admin consent to approve this permission for your tenant.
    * Confirm that the consent is applied.

3.3 Client Secret

1. Inside your app registration, go to Certificates & secrets.
2. Under Client secrets, click New client secret.
3. Provide a descriptive name (e.g. Flare Connector Secret).
4. Choose an expiration period consistent with your security policies.
5. Click Add.
6. Copy and save the secret value (this is shown only once).

You will need this for the “Entra ID client secret” in Flare configuration.

3.4 Enterprise Application & Granting Consent

1. In Azure / Entra, navigate to Enterprise applications.
2. Find the application you just registered in the list.
3. In the application’s Permissions (or Security → Permissions) section, click Grant admin consent (or “Grant consent”).
4. Review the required permissions and accept.

## Flare Side Configuration

1. In the Flare portal, go to Configure → Integrations (in the left sidebar).
2. Click Add integration in the top right.
3. Select Microsoft Entra ID as the integration type.
4. Fill in the following fields using values obtained earlier:
    * Entra ID Client ID → the Application (client) ID
    * Entra ID Client Secret → the client secret value
    * Entra ID Tenant ID → the Directory (tenant) ID
5. Click Test integration to verify that Flare can connect using the provided credentials.
6. If the test succeeds, click Save to finalize the configuration.

## Usage: Validation & Remediation

1. In Flare, open the Credentials Browser and ensure the tenant feed is selected (this integration works on a per-tenant basis).
2. Choose a credential entry from the list to view its details.
3. Click Validate to trigger a check: Flare will determine whether the credential is still active in Entra ID.
4. The validation result will show the status (active/inactive).
5. If active, Flare will provide a direct link to the corresponding Entra ID user so that you can take remediation actions.

## Security Tip

Regularly rotate the client secret according to your organizational policy. After updating the secret in Azure, update it in the Flare configuration immediately and test connectivity.

More information with screens can be found on: [https://docs.flare.io/microsoft-entra-id](https://docs.flare.io/microsoft-entra-id)
