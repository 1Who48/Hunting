---
Title: Leaked Credentials
---

:::warning
THIS DOCUMENTATION IS STILL IN DEVELOPMENT AND SUBJECT TO CHANGE.
:::

## Leaked Credentials

Handling Leaked Credentials in Flare.io

## Overview

This document provides guidelines on identifying, responding to, and mitigating leaked credentials using Flare.io's leaked credentials detection feature. Leaked credentials can lead to security breaches, and it is crucial to act swiftly to prevent unauthorized access.

## Accessing Leaked Credentials Data

Flare.io continuously monitors for credential leaks across various sources. To access the leaked credentials report:

1. Log into your Flare.io account.
2. Navigate to the Leaked Credentials section in the Threat Intelligence dashboard.
3. Review the list of detected credentials, including the exposed username/email and associated risk level.

## Steps to Respond to Leaked Credentials

Step 1: Verify the Leak

* Confirm that the leaked credentials belong to your organization.
* Check if the credentials are still active.
* Identify the source of the leak if possible.

Step 2: Take Immediate Action

1. Reset Affected Passwords
    * Force a password reset for impacted accounts.
    * Ensure the new password follows strong security guidelines.
2. Enable Multi-Factor Authentication (MFA)
    * If not already enabled, enforce MFA to add an extra layer of security.
3. Revoke Existing Sessions
    * Log out all active sessions using the compromised credentials.

Step 3: Investigate the Incident

* Identify how the credentials were leaked (e.g., phishing, data breach, misconfiguration).
* Check logs for any unauthorized access attempts. Check the Security portal or Sentinel from the customer.
* Notify affected users and provide security awareness training if necessary.

Step 4: Strengthen Security Measures Advice and Tips to the customer

* Implement password managers to prevent reuse of compromised passwords.
* Regularly rotate credentials, especially for privileged accounts.
* Conduct periodic security audits and threat assessments.

## Conclusion

Leaked credentials pose a significant security risk, but with proactive monitoring and quick response actions, organizations can minimize potential damages. Flare.io provides the necessary tools to detect and respond to credential leaks effectively.

For more details, visit the official documentation: [Flare.io Leaked Credentials](https://docs.flare.io/leaked-credentials).
