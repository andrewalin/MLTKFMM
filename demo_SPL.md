# How to explore these examples #
I recommend that you don't simply run the full SPL.
<br>
Rather:
<br>
1. Run the first line, observe what is the intermediate data representation.
2. Then, run the 1st and 2nd lines, observe what is the intermediate data represenation now.
3. Then, run the 1st, 2nd, and 3rd [..]
<br>
In this way you will be able to see what each line of SPL accomplishes.

## Converting Categorical Data to One Hot Encoding ##
| inputlookup url_method_examples.csv 
<br>
| fit StandardScaler http_method

## Vectorizing Text: User Agent strings
| inputlookup user_agent_string_examples.csv
<br>
| fit TFIDF user_agent_string
<br>
| fit GMeans user_agent_string_tfidf*
<br>
| stats values(user_agent_string) by cluster

## Feature Engineering on URLS ##
*(Note: the 3rd line requires having a working installation of URL Toolbox)*
<br>
<br>
| inputlookup url_examples.csv
<br>
| eval list="mozilla"
<br>
| `ut_parse_extended(url,list)`
<br>
| eval lendomain=len(ut_domain)
<br>
| eval lennetloc=len(ut_netloc)
<br>
| eval lenpath=len(ut_path)
<br>
| eval lenquery=len(ut_query)
<br>
|fields url len* ut_subdomain_count ut_tld ut_scheme
<br>
| fit StandardScaler len* ut_subdomain_count ut_tld ut_scheme
<br>
| fit GMeans SS*
<br>
| stats values(url) by cluster
