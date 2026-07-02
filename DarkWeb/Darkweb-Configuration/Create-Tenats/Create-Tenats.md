---
Title: Create Tenats
---

:::warning
THIS DOCUMENTATION IS STILL IN DEVELOPMENT AND SUBJECT TO CHANGE.
:::

## Document Classification

This document is only for internal use of Wortell Managed Detection and Response.

## Create-Tenats

Managing Tenants in Flare.io

## Overview

This document provides a detailed guide on managing tenants in Flare.io. Tenants in Flare.io represent independent environments or organizational units within the platform, allowing administrators to manage resources, users, and security settings effectively.

## Accessing the Tenants Management Panel

1. Log into your Flare.io account.
2. Navigate to the Admin Panel.
3. Click on Tenants to view and manage tenant configurations.

## Creating a New Tenant

1. In the Tenants section, click Create New Tenant.
2. Provide the required details:
    * Tenant Name: A unique identifier for the tenant.
    * Description: Optional details about the tenant.
    * Region: Select the appropriate data storage region.
3. Configure access settings and permissions as needed.
4. Click Create to finalize the setup.

## Managing Tenant Users

1. Select the tenant you wish to manage.
2. Navigate to the Users tab.
3. Click Add User, then enter the user’s details:
    * Email Address
    * Role (Admin, Editor, Viewer, etc.)
    * Permissions
4. Click Invite to send an invitation to the user.
5. To modify or remove users, select the user and adjust their settings accordingly.

## Configuring Tenant Settings

* Security Policies: Enforce MFA, password policies, and access control rules.
* Integration Settings: Connect external tools such as SIEM or cloud monitoring services.
* Data Retention Policies: Define how long logs and reports are stored.

## Switching Between Tenants

1. Click on your profile icon in the top-right corner.
2. Select Switch Tenant.
3. Choose the desired tenant from the list.

## Deleting a Tenant

1. Navigate to the Tenants section.
2. Select the tenant you wish to remove.
3. Click Delete Tenant and confirm the action.
    * Note: This action is irreversible and will remove all associated data.

## Best Practices for Tenant Management

* Regularly review user access to ensure compliance with security policies.
* Use separate tenants for testing, development, and production environments.
* Enable logging and monitoring to track tenant activity.

## Conclusion

Flare.io’s tenant management feature provides a flexible way to handle multiple environments securely. By properly configuring and maintaining tenants, organizations can ensure better control, security, and operational efficiency.

For more details, visit the official documentation: [Flare.io Tenants](https://docs.flare.io/tenants).
