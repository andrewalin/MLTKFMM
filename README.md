# MLTK for Mere Mortals
Companion for "MLTK for Mere Mortals" conference talk at Splunk's .conf2025

## High-level summary of clustering ##
1. Gather Data
2. Vectorize
3. Clustering

## Gather Data ##
Conventional method. Actual index and sourcetype values will depend on your particular environment.
```
index=____ AND sourcetype=_____ 
| stats count by http_user_agent
```
---
Accelerated Data Model. Prerequisite: data is well-normalized for CIM and the Web Data Model is accelerated.
```
| tstats count from datamodel=Web.Web by Web.http_user_agent
```
---
Retrieve data from a Lookup. This could work for a CSV or a KVstore lookup.
```
| inputlookup ______.csv 
| stats count by http_user_agent
```
---
>💡For simplicity, the previous examples are if you only want to return a single column of `http_user_agent` values.
It would look different if additional columns are needed, e.g. counts of requests involving each `http_user_agent` value.
