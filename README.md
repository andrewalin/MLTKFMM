# MLTK for Mere Mortals
Companion for "MLTK for Mere Mortals" conference talk at Splunk's .conf2025

a great link from Splunk: https://docs.splunk.com/Documentation/MLApp/5.6.1/User/Algorithms

## High-level summary of clustering ##
1. Gather Data
2. Vectorize
3. Clustering

## Gather Data ##
<em>Nothing particularly 'ML' at this stage; just starting from ground zero and getting some raw data to play with.</em>
<br>
<br>
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
<em>Now that you have some raw data, it requires a translation to speak ML's language -- numbers, in other words!</em>
<br>
<br>
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
| fit TFIDF <TEXT_FIELD_NAME>
```

```
| fit HashingVectorizer <TEXT_FIELD_NAME>
```

## Clustering ##
<em>Now that you have vectors, send it to a Clustering algorithm! There's a large family of ones ending in "Means", including the old faithful KMeans. It works great but has the caveat that it depends on you to choose K --  the desired number of clusters.</em>
<br>
<br>


```
| fit KMeans k=<#_of_clusters> <FEATURE_FIELD_NAMES_AND_GLOBS>
```

```
| fit GMeans <FEATURE_FIELD_NAMES_AND_GLOBS>
```

```
| fit XMeans <FEATURE_FIELD_NAMES_AND_GLOBS>
```

```
| fit DBSCAN <FEATURE_FIELD_NAMES_AND_GLOBS>
```

### KMeans / XMeans / GMeans → “Centroid-based, members of a cluster are close to center of mass” ###

Think of these as clustering approaches where you pre-decide how many clusters you want, like saying “I want N buckets of results.”

KMeans: You tell it “give me 5 buckets”. The math tries to pull events toward the “center of gravity” of each bucket.

XMeans / GMeans: These are like smarter versions that adjust the bucket count if your assumption doesn’t fit the data (like if you said 5, but really 7 groups exist).

Key Concept: Centroid-based clustering assumes the data is kind of “globular” around centers, and the real question is: how many globes?

### DBSCAN → “Density-aware, and considering distance to closest cluster member” ###

Instead of fixing the number of clusters, DBSCAN says: “I’ll group events together only if they’re tightly packed in time/space/value, and I’ll leave loners out.”

Analogy to Splunk’s `transaction`:

maxspan or maxpause controls how far events can drift apart and still belong to the same session.

DBSCAN works similarly: it has a “neighborhood distance” (ε) and “min number of points” that must be in that distance.

If enough points are close together, they form a cluster; if not, they’re treated as noise.

So DBSCAN feels like building “clusters as transactions,” where proximity and minimum population matter more than pre-deciding how many clusters there should be.
