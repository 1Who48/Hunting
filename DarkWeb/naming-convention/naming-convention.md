---
title: Naming Convention
---

## Document Classification

This document is only for internal use of Wortell Managed Detection and Response.

## Introduction

DArkweb are code definitions responsible for querying data and define the data in our report when something is a hit.

Darkweb are defined in **".KQL"** format. When naming new or renaming existing Darkweb Case we must use a and follow our standards. Formally this is called a **"naming convention"**.

## Filename Guidelines

* The filename of the Darkwebcase must be the same as the incident **"name:"** parameter as defined in the **".KQL"** file
  * This includes casing!
* Production Darkweb names must start with a **"WSD-XXXX-"** prefix where **"XXXX"** is an incremental number based upon the last DarkWeb Case.
  * For example **"WSD-1001-"**
* Darkwebcase names must be static
  * I.e. they should **NOT** contain dynamically generated content as part of the title such as an IP address or user principal name
* The Darkwebcase filenames adhere to the documented naming convention (this documentation)

Examples:

  WSD-1001-Darkweb Monitoring Alert

## Naming Convention for Darkweb in Development or Testing

Darkweb Case that are in the **development** or **testing** phase should adhere to the above naming convention with the the following exception:

* In development or testing Darkwebcase names must start with a **"TST-WSD-XXXX-"** prefix where **"XXXX"** is an incremental number based upon the last Darkweb case.
  * For example **"TST-WSD-0123-"**

Examples:

    TST-WSD-1001.yaml
    TST-WSD-1002.yaml

## Detection Case for Darkweb

### KQL

```kql
  Firework_CL
  | where TimeGenerated > ago(30d)
  | where isnotempty(uid_s)
  | where risk_score_d >= 3
  | extend uid_ = iff(uid_s contains "domain",strcat(strcat(split(uid_s, "domain",0)[0],"domain"),replace_string(tostring(split(uid_s, "domain",1)[0]),@"/",@"%2F")),uid_s)
  | extend url = strcat("https://app.flare.io/#/",uid_)
  | mv-expand todynamic(identifiers_s)
  | extend identifier_name_ = tostring(identifiers_s.name)
  | extend Severity_Score = case (
          risk_score_d == 3, "Medium",
          risk_score_d == 4, "High",
          risk_score_d == 5, "High",
          "Informational"
      )
  | project  TimeGenerated, identifier_name_, category_name_s, source_name_s, type_s, url, content_hash_s, first_crawled_at_t, last_crawled_at_t, materialized_at_t, Severity_Score
```
