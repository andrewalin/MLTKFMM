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
<br>

Accelerated Data Model. Prerequisite: data is well-normalized for CIM and the Web Data Model is accelerated.
```
| tstats count from datamodel=Web.Web by Web.http_user_agent
```
<br>

Retrieve data from a Lookup. This could work for a CSV or a KVstore lookup.
```
| inputlookup ______.csv 
| stats count by http_user_agent
```
