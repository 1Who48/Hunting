---
title: Detection - attack npm Packages
---

:::warning
THIS DOCUMENTATION IS STILL IN DEVELOPMENT AND SUBJECT TO CHANGE.
:::

## Detection: Attack npm Packages Defender Portal

## Purpose

The query is designed to detect files on endpoints that match known malicious NPM package hashes (SHA1). It helps identify potential compromises by comparing endpoint activity with an external threat intelligence feed.

## Data Source

- External CSV file hosted on GitHub containing NPM package IoCs (PackageName, Version, SHA1).
- DeviceFileEvents table, which records file activity on Windows devices.

## Scope Windows

- The query focuses on Windows endpoints where file events are logged. It checks for any file whose SHA1 hash matches known IoCs.

---

## How It Works

- Import IoCs: Loads SHA1 hashes from an external CSV file.
- Clean the data: Removes empty values, trims whitespace, and converts hashes to lowercase.
- Deduplicate: Keeps only unique SHA1 values.
- Match against endpoint logs: Compares the cleaned IoC list with SHA1 values in DeviceFileEvents.
- Return context: Displays timestamp, device name, file details, and initiating process information.
- Order results: Sorts matches by most recent first.

---

## What It Detects

- Files on Windows endpoints whose SHA1 hash matches one of the known IoCs from the external NPM package list.
- Potentially malicious or compromised packages (or files derived from them) that may have been executed, stored, or placed on devices.
- The context (file path, process that initiated it, etc.) allows for investigation of how a malicious file appeared or executed.

---

## Why Events Might Not Appear

- The malicious file was never present on any endpoint being monitored (or only on endpoints not included in the DeviceFileEvents table).
- File hashing was not done or not recorded (SHA1 might be absent, incomplete, or not collected).
- The hash in the CSV IoC list might be incorrect, truncated, or formatted differently (e.g. uppercase vs lowercase) — normalization may fail if formatting is off.
- The external CSV did not include all malicious IoCs; new/unknown malicious files would not be detected.
- Logging might be disabled, or the device is offline, or lacking required permissions.
- Time window: events may have occurred outside of the retention period or before log ingestion.

---

### KQL

```kql
let NPMIoCs =
externaldata(PackageName:string, Version:string, SHA1:string)
[h'https://raw.githubusercontent.com/SlimKQL/Hunting-Queries-Detection-Rules/refs/heads/main/IOC/Shai-hulud.csv']
with (format='csv', has_header_row=true, ignoreFirstRecord=true);
let IocSha1 = NPMIoCs
| where isnotempty(SHA1)
| extend SHA1 = tolower(trim(' ', SHA1))
| distinct SHA1;
DeviceFileEvents
| extend SHA1 = tolower(SHA1)
| where SHA1 in~ (IocSha1)
| project Timestamp, DeviceName, FileName, FolderPath, InitiatingProcessFileName, InitiatingProcessCommandLine, SHA1
| order by Timestamp desc
```
