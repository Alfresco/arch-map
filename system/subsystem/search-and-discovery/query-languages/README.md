## Query Languages

![Completeness Badge](https://img.shields.io/badge/Document_Level-In_Progress-yellow.svg?style=flat-square)

![Version Badge](https://img.shields.io/badge/Version-5.2-orange.svg?style=flat-square)
![Version Badge](https://img.shields.io/badge/Version-ASS_1.0-green.svg?style=flat-square)

### Purpose

The query languages allow nodes to be found in the repository using predicates based on both metadata and/or content.
The results reflect the access rights of the user executing the query.
The desired query behaviour of properties is defined in the content model.

### Overview 

There are three query languages 
* The legacy lucene query language (LUCENE)
* Alfresco Full Text Search (AFTS)
* CMIS QL (CMIS)

The legacy lucene query language relies on the SOLR/lucene query parser. The AFTS and CMIS QL languages are ANTLR based.
They parse to an intermediate abstract syntax tree that can be used to generate a query to run against the database or SOLR.
The lucene query language does not support execution to the database. It was used originally used for the lucene search subsystem.

AFTS is a mind meld of the lucene 1.4.3 query syntax. without many of the escaping restrictions, and a Google like query syntax. 
The CMIS QL is defined as part of the CMIS specification. The CONTAINS() clause of the CMIS QL cna contain any AFTS expression subject to some renaming rules that map some properties like cm:name to the corresponding CMIS query name; in this case cmis:name.   

When a call is made to the Search Component of the Java API the query language is one of the required parameters.

The parsers are written in ANTLR. Special tokens in the language, like the minus character '-', have to be escaped when it does not mean "not".
Escaping is not required in phrases. There are several ambiguities in the parser, both semantic and syntactic, and recursion issues  that are resolved in the grammar that would not be required with ANTLR 4. 

There was a POC to test the use of the Parboiled  query parser for the CMIS QL - excluding CONTAINS().
Parboiled is a good choice for implementing an adhoc simple parser.   



### Artifacts and Guidance

* Source Code Link
* License
* Issue Tracker Link
* Documentation Link
* Contribution Model (describe who can change the code and how the contribution process works).   e.g. Aikau: Enhancements should be submitted as pull requests to GitHub. Ideas and suggestions can be raised as issues in the issue tracker.

*** 


### Prerequisite Knowledge
This might be knowledge of certain software, technologies or other components in Alfresco.
Add any training courses or links to background docs at docs.alfresco.com.
Bullet points work well in this section.

*** 

### Design

#### Component Model

#### Data Model
n image is good here (use the PlantUML tooling) with an explanation of the elements of the model.  You can add a sub heading for each element.
#### Data Dictionary
Explain how the Alfresco data dictionary is extended as a result of this component. Explain any new types or aspects that it introduces.
#### Flows
This is where PlantUML comes into its own.  Add your diagram and explain your user flows.
#### Class Diagram
This is where PlantUML comes into its own.  Add your diagram and explain your method and class relationships.
*** 

### APIs and Interfaces
Explain any APIs that are provided as part of the component. How are these built and configured? Are there any related services?
What new interfaces does this component add to Alfresco? 
*** 

### Configuration
You only need this section if there is complex configuration information you've not already included.  You can link to docs.alfresco.com if appropriate.
*** 

### Performance Considerations
Explain caching considerations and anything else that affects Alfresco performance.
*** 

### Security Considerations
What does this component use or implement from a security perspective?  Are access privileges enforced?
*** 

### Cloud Considerations
Is this component deployed to Cloud?  Are there considerations with its implementation?
his component deployed to Cloud?  Are there considerations with its implementation?
***

### Design Decisions
Record import design decisions here

| Decision        | Rationale                  | Date         |
| --------------- |:--------------------------:| ------------:|
| decision 1      | why                        | 01 Jan, 2016 |


### Quiz

You can provide a link to an online quiz where the respondent is asked questions to assess how well they have understood the material.
It is easy to create a quiz using a service that Alfresco already has a license to use.  If you are interested in providing a quiz, contact Greg Melahn to obtain access to the testing service we use.
