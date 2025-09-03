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

## Vectorize ##
Using a function to convert non-numerical data (e.g. a string) to an integer.
```
| eval len_http_user_agent = len(http_user_agent)
```
---
SplunkBase purpose-built parser apps, for example to parse a URL into it's consituent parts that can be vectorized (e.g. by `len()`) as well as metrics that are already numeric.
Note that the first line simply hardcodes the literal string "mozilla" to be passed to the function. 
See pre-recorded demo that uses URL Toolbox
```
| eval list="mozilla" 
| `ut_parse_extended(url,list)`
```
---
One-hot encoding to encode low-cardinality categorical fields.
This can be done manually or through MLTK's `fit` function, e.g. with `fit StandardScaler`.
`http_method` is a classic example of low-cardinality categorical data but plenty of other fields could fit that.
```
|fit StandardScaler http_method
```
---
Both TFIDF and HashingVectorizers are essentially "easy button" ways to turn text data into vectors.<br>
>"TFIDF finds words and n-grams that are common in a given event, but that are rare in your set of events."

>"The HashingVectorizer algorithm converts text documents to a matrix of token occurrences."

Not exactly the same but see https://en.wikipedia.org/wiki/Bag-of-words_model for the basic concept.

```
| fit TFIDF <TEXT>
```

```
| fit HashingVectorizer <TEXT>
```
