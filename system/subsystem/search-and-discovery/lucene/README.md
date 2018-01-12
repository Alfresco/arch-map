## Lucene

![Completeness Badge](https://img.shields.io/badge/Document_Level-Complete-green.svg?style=flat-square)

![Version Badge](https://img.shields.io/badge/Version-4.2-red.svg?style=flat-square)

### Purpose

The Lucene subsystem provides transactional query support againast metadata in older versions of Alfresco.
It has been replaced by transactionl metadata query for this purpose since
![Version Badge](https://img.shields.io/badge/Version-5.0-green.svg?style=flat-square)

*** 

### Overview 

Lucene is a legacy search implementation in Alfresco. We now use the latest version of SOLR which itself uses lucene.
The direct Lucene indexing of metadata is transactional. Content is eventually updated. SOLR decouples indexing from the repository and provides an eventually consistent view for both metadata and content in the repository.

Lucene is currently still used in more than 400 unit tests as it is integrated within the repository.

*** 

### Artifacts and Guidance

This is not going to be provided for this component as it is no longer relevant and requires only limited support.

*** 


### Prerequisite Knowledge

Lucene 1.4.3.

*** 

### Design

This is not going to be provided for this component as it is no longer relevant and requires only limited support.

### APIs and Interfaces

API for node service to update the index directly.
Search Service API common to all Search Service implementations.

*** 

### Configuration

Documentation has been removed from the wiki.

*** 

### Performance Considerations

The Lucene subsystem throttles commits so it can keep up with transactional indexing.
It limits the throughput of the repository.

*** 

### Security Considerations

Access control is enforced post query, which does not scale.

*** 

### Cloud Considerations

Not used in cloud.

***

### Design Decisions

| Decision        | Rationale                  | Date         |
| --------------- |:--------------------------:| ------------:|
| EOL             | Replace with SOLR          | 5.0          |


### Quiz

* Should you be using Lucene?

    **No**

* Should we be using it in unit tests?

    **No**

* Are we supporting lucene?

    It only has limited support in 3, it is deprecated in 4 in favour of SOLR 1, and end-of-life in 5.0.



