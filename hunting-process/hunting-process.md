---
title: Hunting Process
---

## Document Classification

This document is only for internal use of Wortell Managed Detection and Response.

# N8N Threat Hunting Platform — Documentation

> Automated and on-demand KQL threat hunting across all managed customer tenants,
> covering **Microsoft Defender XDR (Graph)** and **Microsoft Sentinel**.

---

## 1. Overview

This N8N platform runs a central library of **KQL hunting queries (detections)** against
**every managed customer tenant**, on two Microsoft security data sources:

| Data environment | Source | Microsoft security portal |
|------------------|--------|---------------------------|
| `graph`          | Microsoft Defender XDR advanced hunting (via Graph API) | Defender portal |
| `sentinel`       | Microsoft Sentinel / Log Analytics | Sentinel / Azure portal |

There are two ways hunts run:

- **Scheduled (CRON)** — every night at **02:00**, *all active templates* run against
  *all customer tenants* on both Sentinel and Graph. Results are logged to a run table.
- **Manual (MAN)** — analysts trigger ad-hoc hunts on demand: filtered across all tenants,
  or targeted at a single tenant. Output is exported to CSV.

A separate workflow re-runs any hunts that **failed** during the nightly scheduled run.

```
                       ┌─────────────────────────────┐
                       │   HuntingTemplates table     │
                       │   (101 KQL detections)       │
                       └──────────────┬──────────────┘
                                      │
              ┌───────────────────────┼───────────────────────┐
              ▼                       ▼                        ▼
   ┌─────────────────┐     ┌──────────────────┐     ┌────────────────────┐
   │ CRON (02:00)    │     │ MAN (on-demand)  │     │ Retry Failed Hunts │
   │ RunDailyHunts   │     │ RunQueryByFilter │     │ RetryFailedHuntsV3 │
   │ Graph + Sentinel│     │ RunQueryForSingle│     │                    │
   └────────┬────────┘     └────────┬─────────┘     └─────────┬──────────┘
            │                       │                         │
            ▼                       ▼                         │
   ┌─────────────────┐     ┌──────────────────┐               │
   │ DailyHunts table│◄────┤  CSV export      │               │
   │ (run log)       │     └──────────────────┘               │
   └────────┬────────┘                                        │
            └──────────────── reads failed rows ──────────────┘
```

---

## 2. Shared building blocks (reusable sub-workflows)

These sub-workflows are called by almost every hunting workflow. Keeping them separate
means tenant discovery and query execution logic lives in **one place**.

| Sub-workflow | Workflow ID | Purpose |
|--------------|-------------|---------|
| **Find Tenants** / **Find Hunting Tenants** | `JQrCUvUd4EezKz…` | Returns the list of customer tenants in scope for hunting. |
| **Run Query For Tenants** | `v632v2VOPRgAP9…` | Executes a given KQL query across the provided tenant(s) against the chosen data environment (Graph or Sentinel) and returns results. |

Supporting nodes used around these blocks:

- **Aggregate Tenant IDs** — collapses the tenant list into the input format `Run Query For Tenants` expects.
- **Build Query Input** — assembles the query payload (KQL + data environment + parameters) for execution.
- **Enrich Results** — adds context to raw query hits (template metadata, tenant, MITRE mapping, etc.).
- **Convert to File** — exports results to **CSV** for analyst review (manual flows).

---

## 3. Data tables

### 3.1 `HuntingTemplates` (detection library — 101 rows)

The single source of truth for every hunting query. Each row is one detection.

| Column | Description | Example |
|--------|-------------|---------|
| `id` | Sequential row id | `1` |
| `version` | Template version | `1` |
| `name` | Detection name (prefixed `WSH-####`) | `WSH-0001 MSBuild.exe as a LOLBin` |
| `description` | What the query detects and why | *"This query detects malicious use of MSBuild…"* |
| `productFamily` | Product grouping | `Device Protect`, `Office Protect` |
| `tags` | Free-text tags / product coverage | `Microsoft Defender for Endpoint, …` |
| `dataEnvironment` | Which engine runs it | `graph` or `sentinel` |
| `mitreTechniques` | MITRE ATT&CK technique IDs | `T1127`, `T1204` |
| `mitreTactics` | MITRE ATT&CK tactic IDs | `TA0005`, `TA0002` |
| `query` | The KQL query body | `DeviceProcessEvents | where Timestamp …` |
| `active` | Whether it runs in scheduled hunts | ✅ / ❌ |
| `createdAt` / `updatedAt` | Timestamps | `2026-05-28T08:46:08…` |

**Naming convention:** `WSH-####` (Wortell Security Hunting) — zero-padded, sequential.

**`active` flag:** only `active = true` templates are picked up by the nightly scheduled
hunts. Toggle this to enable/disable a detection without deleting it.

### 3.2 `DailyHunts` (run log)

Written by the scheduled workflows. One row per template-per-tenant execution, recording
the outcome (**Success** / **Error**). This table is what powers the **retry** workflow and
gives operational visibility into nightly hunt health.

---

## 4. Scheduled hunting — `CRON - Hunting - RunDailyHunts` / `…V3`

| Workflow | Status | Notes |
|----------|--------|-------|
| **CRON - Hunting - RunDailyHuntsV3** | ✅ Published (active) | Current version |
| CRON - Hunting - RunDailyHunts | Legacy | Original (v1) — superseded by V3 |

**Trigger:** Schedule, **daily at 02:00**.
**Scope:** *all active templates* × *all customer tenants*.

The workflow runs **two parallel pipelines** — one for **Graph (Defender XDR)** and one for
**Sentinel** — so each data environment hunts independently.

### Pipeline steps (Graph and Sentinel are identical in shape)

```
Schedule Trigger
   → Edit Configuration            (set run parameters: lookback, batching, etc.)
   → Find Hunting Tenants          (sub-workflow JQrCUvUd…)
   → Aggregate Tenant IDs
   → Get <Graph|Sentinel> Templates (pull active templates for this dataEnvironment)
   → Build Query Input
   → Loop Templates                (iterate over each template)   ◄── Graph pipeline
        → Run Query For Tenants     (sub-workflow v632v2…)
        → Enrich Results
        → Switch Status            (route on Success / Error)
             ├─ Success → Build DailyHunts Row → Insert DailyHunt
             └─ Error   → Build DailyHunts Row → Insert DailyHunt
        → Throttle Wait            (pause between batches to respect API limits)
   (loop back until all templates processed)
```

Key behaviours:

- **`Switch Status` (mode: Rules)** classifies each execution as Success or Error and
  records it in `DailyHunts` either way — so failures are captured, not lost.
- **`Throttle Wait`** (Graph pipeline) paces execution to stay within Microsoft Graph /
  advanced-hunting API rate limits across many tenants.
- Every result (hit or run status) is written to **`DailyHunts`** via `Insert DailyHunt`.

---

## 5. Manual hunting (`MAN - …`)

On-demand workflows triggered by an analyst with **"Execute workflow"**.

### 5.1 `MAN - Hunting - RunQueryByFilter`

Run a query across **all (or a filtered set of) tenants** — e.g. filter by product family,
tag, or data environment.

```
When clicking Execute workflow
   → Edit Inputs              (analyst sets the query / filter)
   → Find Tenants             (sub-workflow JQrCUvUd…)
   → Aggregate Tenant IDs
   → Build Query Input
   → Run Query For Tenants    (sub-workflow v632v2…)
   → Convert to File          (export results to CSV)
```

Use when you want to hunt **broadly** — across the whole customer base or a slice of it —
and get a single CSV back.

### 5.2 `MAN - Hunting - RunQueryForSingleTenant`

Same flow, but targeted at **one specific tenant**. Use for tenant-specific investigation
or validating a query before rolling it out widely.

---

## 6. Retry failed hunts — `MAN - Hunting - RetryFailedHuntsV3`

Re-runs hunts that **failed** during the nightly scheduled run.

- Reads the **`DailyHunts`** log for rows with status **Error**.
- Re-executes those template/tenant combinations.
- Updates the run log with the new outcome.

This recovers from transient failures (API timeouts, throttling, tenant connectivity)
without re-running the entire nightly batch. Run it the morning after a scheduled run if
errors are present.

---

## 7. Hunting template structure & example

Each template is a self-contained KQL detection with MITRE mapping. Example
(representative of `WSH-0001`):

```kql
// WSH-0001 — MSBuild.exe as a LOLBin
// dataEnvironment: graph
// productFamily:   Device Protect
// MITRE:           T1127 (Trusted Developer Utilities Proxy Execution) / TA0005 (Defense Evasion)

DeviceProcessEvents
| where Timestamp > ago(1d)
| where FileName =~ "MSBuild.exe"
| where ProcessCommandLine has_any (".xml", ".csproj", ".proj")
   or ProcessCommandLine has_any ("http", "DownloadString", "FromBase64String")
| project Timestamp, DeviceName, AccountName, FileName, ProcessCommandLine,
          InitiatingProcessFileName, ReportId
| order by Timestamp desc
```

When a template runs, the platform combines:

- the `query` (KQL body),
- the `dataEnvironment` (decides Graph vs Sentinel execution),
- the in-scope tenant list,

…and the results are enriched with the template's `name`, `mitreTechniques`,
`mitreTactics`, and tenant identity before being logged or exported.

---

## 8. Operations & conventions

| Topic | Detail |
|-------|--------|
| **Schedule** | Nightly at **02:00** for all active templates × all tenants. |
| **Enable/disable a detection** | Set `active` in `HuntingTemplates` (no need to delete). |
| **Add a new detection** | Add a row to `HuntingTemplates` with next `WSH-####`, set `dataEnvironment`, MITRE fields, and `active = true`. |
| **Versioning** | Bump the `version` column when editing an existing template's logic. |
| **Workflow naming** | `CRON - …` = scheduled · `MAN - …` = manual · `…V3` = current revision. |
| **Failure handling** | Failures logged to `DailyHunts`; re-run via `RetryFailedHuntsV3`. |
| **API throttling** | `Throttle Wait` paces Graph execution across many tenants. |
| **Reuse** | Tenant discovery (`Find Tenants`) and execution (`Run Query For Tenants`) are shared sub-workflows — edit logic in one place. |

---

*Microsoft security portals referenced: Defender XDR (advanced hunting via Graph) and
Microsoft Sentinel.*
