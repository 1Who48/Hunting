---
title: Investigation Queries
---

## Document Classification

This document is only for internal use of Wortell Managed Detection and Response.

## Introduction

These KQL queries support forensic investigations focused on a user or device. They help security analysts quickly pivot across telemetry sources to collect evidence, validate alerts, and build timelines of activity.

By using these queries, you can:

* Reconstruct device history (processes, logons, network connections, file activity).
* Investigate user actions (logins, suspicious processes, email usage, lateral movement).
* Correlate alerts with supporting evidence.

## Example Investigation Paths

### Device Compromise Investigation

* Alerts on device → SecurityAlert
* Suspicious processes → DeviceProcessEvents
* Outbound traffic → DeviceNetworkEvents
* Persistence attempts → DeviceRegistryEvents
* Dropped/created files → DeviceFileEvents

### User Compromise Investigation

* Recent logons → DeviceLogonEvents
* Lateral movement attempts → DeviceLogonEvents (remote logons)
* Suspicious processes under user context → DeviceProcessEvents
* Malicious email delivery → EmailEvents
* Cloud app abuse → CloudAppEvents
