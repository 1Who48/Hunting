---
Title: Azure Sentinel Integration
---

:::warning
THIS DOCUMENTATION IS STILL IN DEVELOPMENT AND SUBJECT TO CHANGE.
:::

## Document Classification

This document is only for internal use of Wortell Managed Detection and Response.

## Azure-Sentinel-Integration

## Flare Azure Sentinel Integration Documentation

### Overview

Flare allows you to send alerts directly to your Azure Sentinel instance. This integration helps you monitor and respond to cybersecurity threats efficiently.

### Prerequisites

- Azure Sentinel Workspace with read/write permissions.
- Keys created in Azure Key Vault.

### Configuration Steps

1. **Go to Alert Central**
   - Navigate to the Alert Central section in your Flare dashboard.

2. **Create a Channel**
   - Click on `Create Channel`.

3. **Enter Channel Details**
   - Enter a name for the channel.
   - Select `Azure Sentinel` as the type.

4. **Fill in Azure Workspace Information**
   - Go to Azure Sentinel -> Settings -> Workspace Settings -> Agents Management.
   - Open the dropdown for Log Analytics agent instructions to find your Workspace ID and Primary Key.
   - Enter these details in the Flare configuration.

5. **Test Channel**
   - Confirm the values are correct by using the `Test Channel` button.
   - You should receive a test log inside your Workspace Log Analytics within a minute.

6. **Create Alert Channel**
   - Once the test is successful, create the alert channel.

### Expected Output

You should receive all the data Flare sends directly in your Workspace's Log Analytics table named `Firework_CL`.

### Additional Features

- **Workbooks**: Aggregate data received in various dashboards.
- **Playbooks**: Automate incident responses and email notifications when leaked credentials are found.

For more detailed instructions including configuration video, you can follow the step-by-step guide provided by Flare: [Flare.io Azure-Sentinel-Integration](https://docs.flare.io/azure-sentinel-integration).
