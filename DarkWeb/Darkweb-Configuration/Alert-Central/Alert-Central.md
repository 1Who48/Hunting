---
Title: Alert Central
---

:::warning
THIS DOCUMENTATION IS STILL IN DEVELOPMENT AND SUBJECT TO CHANGE.
:::

## Document Classification

This document is only for internal use of Wortell Managed Detection and Response.

## **Flare Alert Central Configuration Manual**

## **1. What is Alert Central?**

Alert Central is Flare's unified alert management system designed to simplify how you manage alerts across multiple identifiers and channels. It consolidates all alert configurations into a single interface, enhancing efficiency, flexibility, and scalability.

## **2. Creating and Managing Channels**

### **Creating a Channel**

1. Navigate to the **Channels** tab.
2. Click on **Create Channel**.
3. Fill out the required fields:
   - **Channel Name**: Provide a unique and descriptive name.
   - **Type**: Select the communication channel (Email, Slack, Jira, Teams, Discord, Webhook).
   - **Tags**: Assign tags for better organization.
4. Click on **Test Channel** to confirm the connection.

### **Channel Status**

- **Active**: The channel is operational.
- **Not Tested**: The channel has not been tested yet.
- **Inactive**: The channel could not establish a connection.

## **3. Creating and Managing Alerts**

### **Creating an Alert**

1. Navigate to the **Alerts** tab.
2. Click on **Create Alert**.
3. Fill out the required fields:
   - **Alert Name**: Provide a unique and descriptive name.
   - **Identifier Scope**: Specify the identifier, group, or tenant.
   - **Categories**: Choose the appropriate category.
   - **Severity Filters**: Set the severity level.
   - **Time Settings**: Set up the time parameters (As Soon As Possible, Daily, Weekly, Monthly).
   - **Alert Channel**: Select the communication channel(s).

### **Managing Alerts**

- View and manage configured alerts and channels.
- Edit or delete alerts based on your role within the organization.

## **4. Best Practices**

- **Use Descriptive Names**: Clearly name your alerts and channels.
- **Test Your Alerts**: Regularly test configurations to ensure proper functionality.

## **5. FAQs**

- **Access**: Permissions depend on your organization administrator.
- **Permissions**: Editor permissions are required to create and manage alerts.
- **Receiving Alerts**: Users with lower permissions can still receive alerts.
- **Migration**: Existing alert settings will be migrated to the new system.
- **Integrations**: Verify integrations in the documentation.
- **Multiple Emails**: Attach multiple emails to a single channel.

For more details, visit the official documentation: [Flare.io Identifiers-Configuration](https://docs.flare.io/identifiers).
