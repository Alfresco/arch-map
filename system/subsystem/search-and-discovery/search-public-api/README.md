## Search and Discovery Public API  

![Completeness Badge](https://img.shields.io/badge/Document_Level-In_Progress-yellow.svg?style=flat-square)

![Version Badge](https://img.shields.io/badge/Version-5.2-orange.svg?style=flat-square)
![Version Badge](https://img.shields.io/badge/Version-ASS_1.0-green.svg?style=flat-square)

### Purpose

The search public API accepts POST requests containing JSON structured as described below.
The JSON is structured to group options related to different query concepts together.
All properties and types etc are intuitive from the Alfresco data model and independent of any implementation.
  
It returns JSON that follows the Alfresco public API guidelines with extensions described blow.

The JSON request/response allows functionality to be added as the API versions.

Version 1.0 of the public search API is implemented against the 5.2 Java Search API.

*** 

### Overview 

The ![Version Badge](https://img.shields.io/badge/Version-5.1-green.svg?style=flat-square)
search sub-system and its Java API does not allow easy extension to 
expose additional features. It requires additional parameters to be added to a strongly
typed command object. This creates a strong coupling in the API with the SOLR  
implementation and SOLR version which is a major impediment to releasing the index 
server as its own component. There is also some coupling 
between the repository and the SOLR version as JSON responses 
change with SOLR releases and new features are added.

The search public API provides a mechanism to decouple SOLR and the repository except for the API by which SOLR
tracks changes in the repository via the search sub-system.

The ![Version Badge](https://img.shields.io/badge/Version-5.2-orange.svg?style=flat-square)
public search API is self describing and follows the public API guidelines.
It is currently a mapping of the Java public query API into JSON.
New features, post 5.2, will appear in this API and *may* appear in the Java API.

It is expected that the public JSON API will evolve into a pass through API.
At this point the Java API will be deprecated or re-implemented to rewrite to the pass through API.
The JSON will be decorated in the search sub-system, for example with security enforcement, and then passed to SOLR.
At this stage the JSON search API and the repository would be fully decoupled. SOLR will be responsible for handling the decorated request.
SOLR can also "stand alone" if the repository provides suitable APIs to decorate/proxy a request.
New query features can be added to SOLR and exposed via the pass through API independent of the repository.
A discovery API for supported query features may be required at this point 


*** 

### Artifacts and Guidance

* [Live explorer] (https://api-explorer.alfresco.com/api-explorer/)
* [YAML] (https://github.com/Alfresco/rest-api-explorer/blob/master/src/main/webapp/definitions/alfresco-search.yaml)
* [Public API Issues] (https://issues.alfresco.com/jira/secure/RapidBoard.jspa?rapidView=42&projectKey=PUBLICAPI&view=detail)

*** 

### Prerequisite Knowledge

YAML

*** 

### Design

#### API End points:

Currently there is only one end point for generic search support.
While it is potentially complex there are many sensible defaults.
There are some pitfalls and error conditions as some options do not make sense with the cmis query language.
For example, sort is part of the query language.

http://localhost:8080/api-explorer/#!/search/search

![Version Badge](https://img.shields.io/badge/Version-5.2-orange.svg?style=flat-square)
* /search

![Version Badge](https://img.shields.io/badge/Version-Future-blue.svg?style=flat-square)
Potential API end points:
* /suggest
  * Field
  * Values
  * Synonyms
  * Metadata
  * Tags
  * Categories
* /recommend
 * document
 * user
* R&A
* Admin/Configuration
* Language specific end points to protect from incompatible options
 * /search/cmis
 * /search/afts
* /summary
* /stream
* More like this
 * /mlt
* ML/AI/Clustering/Model ?

#### Query end point

The JSON structure follows SOLR style request groupings.
For example, the options required to control highlighting are grouped together.

![Version Badge](https://img.shields.io/badge/Version-5.2-orange.svg?style=flat-square)
The high level request groupings in the JSON are:

##### query

This is the only required entry with query the only required parameter.
If provided, the userQuery should be exactly what the user typed in. It is useful to distinguish this from other bits that may have been added to the query.
If provided, userQuery is used for highlighting, did-you-mean, query logging. etc ..

* query
* userQuery
* language

##### paging

* maxItems
* skipCount

##### include

What additional information to include in the standard response.

* ["aspectNames", "properties", "isLink"]

##### fields

What fields to explicitly include in the response. Currently these are live from the repository and not eventually consistent values from the index.

* []

##### sort

How to sort the results. This does not make sense with the cmis query language. 

* [{"type":"FIELD", "field":"cm:description", "ascending":"true"}]

##### templates

A template is a way to define and abstract complex queries from the user.
It is similar to the dismax query parser in SOLR but more powerful as a template can be treated as a field.
Any number of templates are allowed. Template definitions can not be circular.

* [{"name": "_PERSON","template": "|%firstName OR |%lastName OR |%userName"}, {"name": "mytemplate","template": "%cm:content"}] 

##### defaults

Default values that are not usually changed.
In particular, these options can be used to set default/implicit AND or OR behaviour. 

* textAttributes
* defaultFTSOperator
* defaultFTSFieldOperator
* namespace
* defaultFieldName

##### filter Queries

Filter queries limit the results found. They do not affect the score or ranking of the results found by the query.
The results of filter queries  are cached and can be reused, warmed, and combined with other filter queries.
If you find yourself writing AND  you may in fact be better off adding two filter queries which would be anded together and cached/warmed/reused in their own right.

* [{"query": "TYPE:'cm:folder'"},{"query": "cm:creator:mjackson"}] 

##### facet Queries

Queries that are used to generate a single facet value based on the number of documents that matched the overall query AND the facet query 

* [{"query": "created:2016","label": "CreatedThisYear"}]

##### facet Fields

Facets generated by counting field values for all results that match the query.
By default this is with all the filter queries applied.
Some filter queries may be excluded. This is multi-select faceting. 

* {"facets": [{"field": "'content.size'"}]}
* {"facets": [{"field": "creator", "mincount": 1}, {"field": "modifier", "mincount": 1}]}
* The options are complex - see the yaml

##### spellcheck

If present this enables spellcheck support. An explicit spell check query can be provided.

* query

##### scope

The locations that are queried. 

* "locations": ["nodes", "versions", "deleted-nodes"]

##### limits

Limits applied to the query of the query goes to the database

* permissionEvaluationTime
* permissionEvaluationCount

##### highlight

Term hit hightlighting is complex and has many options - see the yaml.
Most terms map to the obvious SOLR term for the standard highlighter.

##### Multi-select facets

There are use cases where facet counts are required for values that would be excluded by filter queries.
The most common example is something like a state field with 4 values shown as a facet.
When selecting one, the results should be restricted but the counts for all states remain.
This allows more then one facet value to be used in a filter.
Note they would have to be ORed together in the filter.

To do this each filter query can be given a tag.
Any number of filter queries can be ignored by a field facet if it references the tags of the filter queries that should not affect the counts. 


#### Future Query end point additions 

![Version Badge](https://img.shields.io/badge/Version-Future-blue.svg?style=flat-square)
Future (some may be part of existing or the query language):

##### GEO

Currently we have no location in the context used for queries. 
This allows location to be explicitly added to query requests.
It is then possible to think about filtering, ordering and ranking based on proximity.

##### Nested Facets, statistics and aggregation - JSON faceting

The current public API does not support nested faceting.
The SOLR JSON facet API provides nesting for facets of all types plus functions, aggregation and statistics.
These facets would be approximate as the JSON facet API does not do refinement.

##### Other Extension for R&A use cases

To be determined.

##### Spacial heat map

SOLR supports heat maps which we could expose.
This makes most sense for geographic data but other data can be mapped into a suitable space.

##### Functions

We can expose functions. There are several use cases around faceting and custom grouping.
This could be used for our current rather messy csv mapping of mimetypes to groupings.
These mimetype groupings were a key part of R&A v1. 


##### Relevance/Ranking

Enhanced ranking over and above score. Personalised ranking. Time decay. 

##### Limits

SOLR can time out queries during execution. We should expose this to developers.

 * Early termination


#####  Graph query

Linking objects together may well be part of the query language. 
It more be more intuitive to specify links outside the query language.
This needs more thought.

##### Streaming

Watch and notification use cases.
Potential time series analysis (iSAX)

##### Explicit query hints 

We can spend time extracting shard keys from a query and sending a query to only the shards that matter.
It would be simpler just to be told what to do - here are the shard keys .....
We may be able to find an index sharded correctly and save a whole load of work ....

 * shard key type and value 


##### Query log access

We have POC level support for query logs which we could move forward with a decoupled Alfresco Search Services release.
How should we support query and provide access control etc.

##### Session

Support an explicit session id in some way - may be just the alfresco ticket.
This helps link queries and events together to infer user intent.
This may require session ids in other data sets (workflow, activities) to link this up  


##### Suggestion

More advanced suggestion to cover what metadata can be queried, and specific value suggestion for specific fields.

 * Fields
 * Field values
 
 
##### Recommendations

Person, document or other entity focused recommendation.

##### Query expansion

Control, how queries may be expanded to find things indirectly related to a query. 
Expand to search things related by clustering, associations, etc .... 

 * synonym
 * similar docs by cluster
 
 
##### Hybrid query

Combine DB and SOLR queries together to give the fastest up to date query. 
Us the DB to fill in the gap between the state of the index and now.
Metadata only.

##### Smart facets

Use the query results to generate facets rather then have then requested.
For example, driven by the TYPE or ASPECTs found in the results.
Definition of facets for an ASPECT if predominant ...

##### Grouping/collapsing/expansion

Increase diversity in the result set by grouping some things together.
The obvious use cases is collapsing versions of a document to one result entry.
Support expansion/collapse by key.

##### Promotion

This is more an e-commerce thing. Bias the results.
Perhaps everyone needs to see an update to the company hand book .....

##### Other formatting

Something other then JSON - CSV?

##### Multiple queries

Support more then one query on one request. They can be executed in parallel.
Faster union operation?


##### Property/field Decoration

Translate facet tags or properties more human readable values.

 * Mapping user name to full name
 * Mapping mimetype
 * Other?

##### Parameters

Support parameterisation of queries.

##### Localisation

Currently only one locale is given on the request. We try to guess some from the query and configuration.
Explicitly set the locale for ordering and additional locales for searching.
This could include query translation if we could do that using some external service.

 * primary locale (default for ordering)
 * secondary locales
 * timezone
 * location (GEO Attribute)?
 * mlAnalaysisMode
 
 
##### Consistency

Set consistency requirements in terms of latency, finding out of date content etc 

 * queryConsistency
 * sinceTxId
 * atLeastTxId (consider waiting ...)


##### Scope

Enhancements to query scope - currently we only have "store" via the locations option

 * tenants (replaces tenantFilter)
 * repositories
 * aggregation
 
 
##### Pivot based nested facets (not JSON)

Another more limited way of nesting facets but with refinement.

##### Interval facets 

More restrictive but fast range based facets

##### Caveat Filters

Support to simplify adding RM style caveats and security filters. 

##### Cursor

Deep paging support for large result sets.


##### Query Response 

The above may apply some extension to the JSON result set returned   
* Index State/"out-of-date"ness 
* Cursor
* Termination
* Distance
* Grouping collapse keys and expansion support

### Performance Considerations

As normal queries. The extra decoration provided by the public API will be faster than doing it yourself. 
It takes advantage of bulk fetching results as the result set is traversed. 

*** 

### Security Considerations

Authentication issues are the same as the general public API. 

*** 

### Cloud Considerations

These are the same as the general public API.

***

### Quiz

* Should I use the public API? 

     **Yes** 
