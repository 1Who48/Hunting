---
title: Special-use-detection-case
---

## Document Classification

This document is only for internal use of Wortell Managed Detection and Response.

## Introduction

This use case is designed for customers who require an additional filter on leaked identities before a case is created in Vidare. By applying this logic, only relevant leaks tied to known identities in the customer environment are escalated. This ensures higher accuracy and reduces noise from irrelevant or external leaks.

At the moment, this use case is only active for Bol.com but can be extended to other customers when required.

## The Detection

The detection identifies newly leaked identity data from the Firework_CL dataset.
Key filtering logic:

-Only leaks with a risk score of 3 or higher are included (Medium/High severity).
- The uid_s field must be present and valid.
- Entries from the stealer_logs_samples source are excluded to avoid false positives.
- Results are cross-checked against the IdentityInfo table (last 14 days) to confirm the leaked identity belongs to the customer’s environment.
- When a match is found, a case can be created in Vidare with the relevant details (severity, leak source, timeline).

## Active on Customer

Currently, this case logic is implemented only for Bol.com.
For Bol.com, this ensures that only real leaked identities belonging to their employees/users trigger cases in Vidare, minimizing unnecessary alerts and improving operational focus.

## Explanation of the KQL

1.The query looks at events from the last hour.

2.It only keeps events that have:

- new leaked data,
- a risk score of 3 or higher,
- a valid user ID,
- and are not from the “stealer logs samples” source (to avoid irrelevant data).

3.The risk score is then translated into a severity level:

- 3 = Medium
- 4 or 5 = High
- Anything else = Informational

4.A direct link to the case in Flare.io is created, so investigators can quickly open the details.

5.If one event contains multiple leaked identities, these are split out so each identity can be checked separately.

6.Each leaked identity is cleaned up (made lowercase, trimmed of extra spaces) for consistency.

7.The leaked identities are then compared with the company’s known user accounts from the last 14 days.
- If there’s no match, the leak is ignored.
- If there is a match, it means the leak belongs to someone in the company.

8.Finally, the results are presented in a clear format showing:

- when the leak happened,
- where it came from,
- the type of leak,
- which identity was exposed,
- the severity level,
- and a link to the Flare.io case.
The newest leaks appear at the top of the list.

### KQL

```kql
Firework_CL
| where TimeGenerated > ago(1h)
| where notempty(data_new_leaks_s) and source_s != 'stealer_logs_samples' and (risk_score_d >= 3) and isnotempty(uid_s)
| extend Severity_Score = case (
        risk_score_d == 3, "Medium",
        risk_score_d == 4, "High",
        risk_score_d == 5, "High",
        "Informational"
    )
| extend uid_ = iff(uid_s has "domain",strcat(strcat(split(uid_s, "domain",0)[0],"domain"),replace_string(tostring(split(uid_s, "domain",1)[0]),@"/",@"%2F")),uid_s)
| extend url = strcat("https://app.flare.io/#/",uid_)
| project-away uid_
| mv-expand todynamic(data_new_leaks_s)
| extend Leaked_Identity = trim(" ", tolower(tostring(data_new_leaks_s.identity_name)))
| where isnotempty(Leaked_Identity)
| join kind=innerunique (IdentityInfo 
    | where TimeGenerated > ago(14d)
    | extend UserPrincipalName = tolower(AccountUPN)
    | distinct UserPrincipalName
) on $left.Leaked_Identity == $right.UserPrincipalName
| project TimeGenerated, category_name_s, source_name_s, type_s, Leaked_Identity, url, content_hash_s, first_crawled_at_t, last_crawled_at_t, materialized_at_t, Severity_Score
| sort by TimeGenerated desc
```
