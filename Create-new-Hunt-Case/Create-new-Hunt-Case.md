---
title: Create new Hunt Case
---

## Document Classification

This document is only for internal use of Wortell Managed Detection and Response.

# Creating a New Hunting Template

> How to add a new KQL detection to the `HuntingTemplates` table so it runs in the
> automated nightly hunts and on-demand manual hunts.

This guide is a companion to [N8N-Hunting-Workflows.md](N8N-Hunting-Workflows.md).

---

## 1. Where templates live

All detections are rows in the **`HuntingTemplates`** data table
(N8N → **Hunting** project → **Data tables** → **HuntingTemplates**).

To add one: open the table and click **Add Row**, then fill the columns below.
A new template becomes part of the customer-wide hunt automatically — the scheduled
workflow reads every row where `active = true` for its data environment.

---

## 2. Choose the data environment first

The **`dataEnvironment`** column decides *which engine* runs the query and *which tables*
your KQL can reference. Pick this **before** writing the query, because the table schema
differs between the two.

| `dataEnvironment` | Runs against | Picked up by | Typical tables |
|-------------------|--------------|--------------|----------------|
| `graph` | **Microsoft Defender XDR** advanced hunting (Microsoft Graph security API) | `Get Graph Templates` → Graph pipeline | `DeviceProcessEvents`, `DeviceNetworkEvents`, `DeviceFileEvents`, `DeviceRegistryEvents`, `EmailEvents`, `IdentityLogonEvents`, `AlertEvidence`, `CloudAppEvents` |
| `sentinel` | **Microsoft Sentinel** / Log Analytics workspace | `Get Sentinel Templates` → Sentinel pipeline | `SecurityEvent`, `SigninLogs`, `AuditLogs`, `CommonSecurityLog`, `Syslog`, `AzureActivity`, `DeviceEvents`* |

> \* If a customer's Sentinel workspace has the **Defender XDR connector** enabled, the
> `Device*` / `Email*` tables may also be available there. **Do not assume it** — confirm
> the table exists in the target Sentinel workspaces before relying on it.

**Rule of thumb:**
- Endpoint / email / identity telemetry from Defender → `graph`.
- Workspace log sources, Azure activity, sign-ins, custom connectors, or anything you'd
  query in the Sentinel **Logs** blade → `sentinel`.

> One template = one data environment. If you need the same detection on both, create
> **two rows** (e.g. `WSH-0101` graph and `WSH-0102` sentinel) with the query adapted to
> each schema.

---

## 3. Fill in the columns

| Column | Required | What to enter |
|--------|----------|---------------|
| `id` | auto | Leave to the table (sequential). |
| `version` | ✅ | Start at `1`. Bump when you change the query logic later. |
| `name` | ✅ | `WSH-#### <Short descriptive title>` — next free number, zero-padded (e.g. `WSH-0102 Suspicious OAuth consent grant`). |
| `description` | ✅ | What it detects, why it matters, and any expected false positives. One or two sentences. |
| `productFamily` | ✅ | `Device Protect`, `Office Protect`, or the relevant family. |
| `tags` | ✅ | Comma-separated coverage, e.g. `Microsoft Defender for Endpoint, Microsoft 365 Defender`. |
| `dataEnvironment` | ✅ | `graph` **or** `sentinel` (see section 2). |
| `mitreTechniques` | ✅ | ATT&CK technique IDs, comma-separated, e.g. `T1566, T1204`. |
| `mitreTactics` | ✅ | ATT&CK tactic IDs, comma-separated, e.g. `TA0001, TA0002`. |
| `query` | ✅ | The KQL body (see section 4). |
| `active` | ✅ | `false` while developing/testing → set `true` to go live. |
| `createdAt` / `updatedAt` | auto | Set by the table. |

**Naming:** `WSH` = Wortell Security Hunting. Always take the **next sequential number** —
check the highest existing `WSH-####` first so you don't collide.

---

## 4. Writing the query

### Conventions (both environments)

- **Always bound the time window.** Use a leading `let` or an explicit `where`:
  ```kql
  let Timeframe = 1d;
  SomeTable
  | where Timestamp > ago(Timeframe)
  ```
  Keep it to **1 day** unless the detection genuinely needs more — the nightly hunt runs
  every 24h, so a wider window means duplicate hits.
- **Project only the useful columns** (`| project …`) so CSV output and `DailyHunts`
  storage stay lean. Always include something that identifies the entity (device, user,
  account) and a timestamp.
- **Make it tenant-agnostic.** Do not hard-code a tenant ID, device name, or domain — the
  query runs against *every* customer. Use `externaldata`/`datatable` for IOC lists if
  needed (as several existing WSH templates do).
- **Validate it runs clean** in the real portal before activating (section 5).

### Graph (Defender XDR) example — `dataEnvironment = graph`

```kql
// WSH-#### Suspicious MSBuild LOLBin execution
let Timeframe = 1d;
DeviceProcessEvents
| where Timestamp > ago(Timeframe)
| where FileName =~ "MSBuild.exe"
| where ProcessCommandLine has_any ("http", "DownloadString", "FromBase64String")
| project Timestamp, DeviceName, AccountName, FileName,
          ProcessCommandLine, InitiatingProcessFileName, ReportId
| order by Timestamp desc
```

### Sentinel example — `dataEnvironment = sentinel`

```kql
// WSH-#### Anomalous Azure AD sign-in from new country
let Timeframe = 1d;
SigninLogs
| where TimeGenerated > ago(Timeframe)
| where ResultType == 0
| extend Country = tostring(LocationDetails.countryOrRegion)
| summarize Logons = count(), Apps = make_set(AppDisplayName)
        by UserPrincipalName, Country, IPAddress, bin(TimeGenerated, 1h)
| where Country !in ("NL", "BE", "DE")   // adjust per baseline, keep tenant-agnostic
| project TimeGenerated, UserPrincipalName, Country, IPAddress, Logons, Apps
```

> Note the column-name differences: Defender tables use `Timestamp`; Sentinel/Log Analytics
> tables typically use `TimeGenerated`. This is the most common porting mistake.

---

## 5. Test before going live

1. Set `active = false` on the new row while you iterate.
2. **Run the query manually in the native portal** against a representative tenant:
   - `graph` → Defender portal → **Advanced hunting**.
   - `sentinel` → Sentinel → **Logs**.
   Confirm it parses, returns sensible results, and isn't excessively noisy.
3. **Dry-run through N8N** with a manual workflow:
   - `MAN - Hunting - RunQueryForSingleTenant` to test against one customer, or
   - `MAN - Hunting - RunQueryByFilter` to test across a filtered set.
   Review the CSV output.
4. When happy, set **`active = true`**. It joins the next **02:00** nightly hunt for its
   data environment.

---

## 6. Editing or retiring a template

- **Tuning an existing query:** edit the `query`, then **increment `version`**. The
  `updatedAt` timestamp updates automatically.
- **Temporarily disable:** set `active = false` — keeps the history, stops it running.
- **Retire permanently:** set `active = false` (preferred over deleting, so the `WSH-####`
  number and history are preserved).

---

## 7. Quick checklist

- [ ] Next free `WSH-####` number chosen
- [ ] `dataEnvironment` set (`graph` **or** `sentinel`) and query uses the right tables/columns
- [ ] Time window bounded (`ago(1d)` unless justified)
- [ ] No hard-coded tenant/device/domain — query is tenant-agnostic
- [ ] `productFamily`, `tags`, `mitreTechniques`, `mitreTactics` filled
- [ ] `description` explains detection + false-positive expectations
- [ ] Tested in the native portal **and** via a manual N8N run
- [ ] `active = true` only after validation
