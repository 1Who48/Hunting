---
title: Detection - New IP Sign-ins in from User
---

:::warning
THIS DOCUMENTATION IS STILL IN DEVELOPMENT AND SUBJECT TO CHANGE.
:::

## Detection: New IP Sign-ins in from User Defender Portal

## Purpose

Detect **previously unseen (new) IP addresses** used to sign in to Azure AD accounts, by comparing **recent sign-ins** against a **historical baseline**. It then summarizes activity per user + IP and flags failures.

## Data Source

- **Table:** AADSignInEventsBeta

## Scope Windows

- **Baseline:** last **30 days** excluding the most recent 24 hours
- **Recent:** last **10 days** (configurable)

## Identity Key

- Uses `AccountObjectId` (as `AccountId`) for reliable joins
- Optional filter: `account_id_equals` to focus on a specific user

---

## How It Works

1. **Build baseline (30d excl. last 24h)**
   - For each `AccountId`, collect:
     - `KnownIPs` = all IPs seen in the baseline
     - `KnownUAs` = all user-agents seen (from `UserAgent` or `Browser on OSPlatform`)

2. **Collect recent sign-ins (10d)**
   - Create a robust **UPN label** using multiple possible columns
   - Project key fields: `TimeGenerated`, `UPN`, `AccountId`, `IPAddress`, `UA`, `Country`, `City`, `ErrorCode`, `ResultType`

3. **Join recent ↔ baseline on AccountId**
   - Compute detection flags:
     - `SignInSucceeded` → true if `ErrorCode == 0` or `ResultType == 0`
     - `IsNewIP` → IP **not** in `KnownIPs` for that account
     - `IsKnownUA` → UA **is** in `KnownUAs` for that account

4. **Filter to detections**
   - Keep only:

     ```kql
     | where IsNewIP
     ```

5. **Summarize results**
   - Per UPN, AccountId, IPAddress, IsNewIP, compute:
     - `Logins`, `FirstSeen`, `LastSeen`
     - `AnyFailed` (any failed attempt in the group)
     - `AnyKnownUA` / `AllUserAgentsNew`
     - Example UA and sets of `CountriesSeen`, `CitiesSeen`

---

## What It Detects

- **Primary detection:** Any **IP address never observed** for the account in the 30-day baseline
- **Context included:**
  - Whether sign-ins succeeded or failed (`AnyFailed`)
  - Whether user-agents were previously known

---

## Why Events Might Not Appear

- The IP **was seen in baseline** → not "new"
- The event is **outside the recent window** (e.g., older than 10 days)
- The account has **no baseline data** (all recent IPs will appear as new)
- Other filters exclude the record

---

## Key Logic Notes

- **User-Agent normalization:**

  ```kql
  coalesce(UserAgent, Browser + " on " + OSPlatform)
  ```

### KQL

```kql
// === Parameters ===
let lookback   = 30d;          // baseline (excl. laatste 24u)
let recent     = 10d;          // recente periode
let account_id_equals = "79d4a658-e204-46d7-9521-4a8c0cf337d0"; // test-id

// === Baseline ===
let Baseline =
AADSignInEventsBeta
| extend AccountId = tostring(column_ifexists("AccountObjectId",""))
| where isnotempty(AccountId)
| where TimeGenerated between (ago(lookback + 1d) .. ago(1d))
| extend UA = coalesce(column_ifexists("UserAgent",""),
                       strcat(tostring(column_ifexists("Browser","")), " on ", tostring(column_ifexists("OSPlatform",""))))
| summarize KnownIPs = make_set(IPAddress, 4096),
            KnownUAs = make_set(UA, 4096)
  by AccountId;

// === Recent ===
let RecentRaw =
AADSignInEventsBeta
| extend UPN = tostring(coalesce(column_ifexists("UserPrincipalName",""),
                                 column_ifexists("AccountUPN",""),
                                 column_ifexists("Identity",""),
                                 column_ifexists("AccountDisplayName",""),
                                 tostring(column_ifexists("AccountObjectId",""))))
| extend AccountId = tostring(column_ifexists("AccountObjectId",""))
| where TimeGenerated >= ago(recent)
| where isempty(account_id_equals) or AccountId =~ account_id_equals
| extend UA = coalesce(column_ifexists("UserAgent",""),
                       strcat(tostring(column_ifexists("Browser","")), " on ", tostring(column_ifexists("OSPlatform",""))))
| extend Country = tostring(column_ifexists("Country","")),
         City    = tostring(column_ifexists("City",""))
| extend ErrorCode  = tolong(column_ifexists("ErrorCode", int(-1))),
         ResultType = tolong(column_ifexists("ResultType", int(-1)))
| project TimeGenerated, UPN, AccountId, IPAddress, UA, Country, City, ErrorCode, ResultType;

// === Join + flags + ONLY UNKNOWN IPs ===
RecentRaw
| join kind=leftouter Baseline on AccountId
| extend SignInSucceeded =
    case(
        isnotempty(ErrorCode),  ErrorCode  == 0,
        isnotempty(ResultType), ResultType == 0,
        bool(false)
    )
| extend IsKnownUA = iff(isnull(KnownUAs), false, set_has_element(KnownUAs, UA))
| extend IsNewIP   = iff(isnull(KnownIPs) or not(set_has_element(KnownIPs, IPAddress)), true, false)
| where IsNewIP or SignInSucceeded == false
| summarize
    Logins     = count(),
    FirstSeen  = min(TimeGenerated),
    LastSeen   = max(TimeGenerated),
    AnyFailed  = any(SignInSucceeded == false),
    AnyKnownUA = any(IsKnownUA),
    AllUserAgentsNew = iff(max(toint(IsKnownUA)) == 0, true, false),
    CountriesSeen = make_set(Country, 8),
    CitiesSeen    = make_set(City, 16),
    ExampleUA     = arg_max(TimeGenerated, UA).UA
  by UPN, AccountId, IPAddress, IsNewIP
| order by LastSeen desc
```
