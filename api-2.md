Registry: API
==============================

# <a id="contents">Contents</a>

* [Contents](#contents)
* [Introduction](#intro)
* [Status of this document](#status)
* [Design principles](#design-principles)
* [Endpoints](#endpoints)
  * [Organization profiles](#endpoint-organization)
    * [List organization profiles](#endpoint-organization-list)
    * [Retrieve an organization profile](#endpoint-organization-retrieve)
  * [Dataset profiles](#endpoint-dataset)
    * [List dataset profiles](#endpoint-dataset-list)
    * [Ping a dataset profile](#endpoint-dataset-register)
    * [Retrieve a dataset profile](#endpoint-dataset-retrieve)

# <a id="intro">Introduction</a>

This document outlines the _application programming interface_ (API) of NDE's _proof of concept_ Registry. It describes the endpoints that can be used by consuming applications, such as the collection management systems of cultural heritage institutions.

# <a id="status">Status of this document</a>

This document is published for examination, experimental implementation and evaluation. It is not an official specification.

This document is a second iteration of a specification in which dataset profiles are crawled upon notification (via ping) of it's existance (or change or deletion). As organisation information are part of the (crawled) dataset profiles, there is no need for a seperate administration of organisations. Authentication/authorization is not needed as data is crawled from the websites of (to be trusted) publishers of dataset profiles.

Elements not specified yet included:

 - Dataset profiles endpoint validation (/validate, which directly fetches dataset profile from specified URL and returns a validation report, based on publication model SHACL)
 - Filtering (eg. list data setprofiles with a SPARQL endpoint as distribution)
 - Searching, including wildcards, aliases for common queries (eg. list all organisation with 'archive' in the name)
 - Paginating
 - Sorting (only nice-to-have)

 - API versioning
 - API rate limiting

# <a id="design-principles">Design principles</a>

1. The API uses modern API standards and best practices
2. The API uses [Linked Data Platform](https://www.w3.org/TR/ldp/) (LDP) for HTTP operations on web resources
3. The API uses [JSON-LD](http://json-ld.org/) as the preferred format for transmitting data
4. The API uses established Linked Data vocabularies, one of which is the (schema.org based) NDE publication model 

# <a id="endpoints">Endpoints</a>

## <a id="endpoint-organization">Organization profiles</a>
An organization profile contains the metadata of an organization describing its characteristics, both administrative, descriptive and structural.

### <a id="endpoint-organization-list">List organization profiles</a>

#### Request

<code>GET /organizations</code>

###### Headers
Key | Value
--|--
Accept | application/ld+json

#### Response

###### Headers
Key | Value
--|--
Status | 200 OK
ETag | {etagValue}
Content-Type | application/ld+json
Accept-Post | application/ld+json
Allow | POST,GET
Link | ```<http://www.w3.org/ns/ldp#BasicContainer>; rel="type",<http://www.w3.org/ns/ldp#Resource>; rel="type"```

###### Body (example)
```
{
  "@context": {
    "dcterms": "http://purl.org/dc/terms/",
    "ldp": "http://www.w3.org/ns/ldp#"
  },
  "@id": "/organizations",
  "@type": ["ldp:Container", "ldp:BasicContainer"],
  "dcterms:title": "Organizations",
  "ldp:contains": [
    {"@id": "/organizations/1"},
    {"@id": "/organizations/2"},
    {"@id": "/organizations/3"}
  ]
}
```

###### Errors
Status | Error description
--|--
400 Bad Request | The request is invalid

##### Filtering/searching/sorting/paging

> **TODO**: describe how to filter, search, sort and paginate the list (via POST)

### <a id="endpoint-organization-retrieve">Retrieve an organization profile</a>

#### Request

<code>GET /organizations/{organizationId}</code>

###### Headers
Key | Value
--|--
Accept | application/ld+json

#### Response

###### Headers
Key | Value
--|--
Status | 200 OK
ETag | {etagValue}
Content-Type | application/ld+json
Accept-Patch | application/ld+json
Allow | GET
Link | ```<http://www.w3.org/ns/ldp#RDFSource>; rel="type",<http://www.w3.org/ns/ldp#Resource>; rel="type"```

###### Body (example)
```
{
  "@context": {
    "dcterms": "http://purl.org/dc/terms/",
    "foaf": "http://xmlns.com/foaf/0.1/"
  },
  "@id": "/organizations/4",
  "@type": "foaf:Organization",
  "foaf:name": "Koninklijke Bibliotheek",
  "dcterms:identifier": {
    "@id": "https://www.kb.nl/"
  },
  "dcterms:created": {
    "@type": "http://www.w3.org/2001/XMLSchema#dateTime",
    "@value": "2017-05-16T10:00:00"
  },
  "dcterms:modified": {
    "@type": "http://www.w3.org/2001/XMLSchema#dateTime",
    "@value": "2017-05-16T10:00:00"
  }
}
```

> **TODO** rewrite body based on (schema.org based) publication model

###### Errors
Status | Error description
--|--
400 Bad Request | The request is invalid
404 Not Found | The resource does not exist


## <a id="endpoint-dataset">Dataset profiles</a>
A dataset profile contains the metadata of a dataset describing its characteristics, both administrative, descriptive and structural.

### <a id="endpoint-dataset-list">List dataset profiles</a>

#### Request

<code>GET /datasets</code>

###### Headers
Key | Value
--|--
Accept | application/ld+json

#### Response

###### Headers
Key | Value
--|--
Status | 200 OK
ETag | {etagValue}
Content-Type | application/ld+json
Accept-Post | application/ld+json
Allow | POST,GET
Link | ```<http://www.w3.org/ns/ldp#BasicContainer>; rel="type",<http://www.w3.org/ns/ldp#Resource>; rel="type"```

###### Body (example)
```
{
  "@context": {
    "dcterms": "http://purl.org/dc/terms/",
    "ldp": "http://www.w3.org/ns/ldp#"
  },
  "@id": "/organizations/4/datasets",
  "@type": ["ldp:Container", "ldp:BasicContainer"],
  "dcterms:title": "Datasets of Koninklijke Bibliotheek",
  "ldp:contains": [
    {"@id": "/organizations/4/datasets/1"},
    {"@id": "/organizations/4/datasets/2"},
    {"@id": "/organizations/4/datasets/3"}
  ]
}
```

###### Errors
Status | Error description
--|--
400 Bad Request | The request is invalid
404 Not Found | The resource does not exist


##### Filtering/searching/sorting/paging

> **TODO**: describe how to filter, search, sort and paginate the list (via POST)

 
### <a id="endpoint-dataset-register">Ping the location of a dataset profile</a>

#### Request

<code>POST /ping</code>

###### Headers
Key | Value
--|--
Content-Type | application/ld+json
Link | ```<http://www.w3.org/ns/ldp#RDFSource>; rel="type",<http://www.w3.org/ns/ldp#Resource>; rel="type"```

###### Body (example)
```
{
  "@id": "https://data.kb.nl/datasets/nationale-bibliografie"
}
```
> **TODO** describe triple-function of method: add (when specified URL of datasetprofile doesn't exist), update (when specified URL contains dataset profile which already exists) and delete (when specified URL returns 404). 

#### Response

###### Headers
Key | Value
--|--
Status | 201 Created
Location | ~~/organizations/{organizationId}/datasets/{datasetId}~~ **TODO**
Link | ```<http://www.w3.org/ns/ldp#RDFSource>; rel="type",<http://www.w3.org/ns/ldp#Resource>; rel="type"```
Content-Length | 0

###### Errors
Status | Error description
--|--
400 Bad Request | The request is invalid
404 Not Found | The resource does not exist

### <a id="endpoint-dataset-retrieve">Retrieve a dataset profile</a>

#### Request

<code>GET /datasets/{datasetId}</code>

###### Headers
Key | Value
--|--
Accept | application/ld+json

#### Response

###### Headers
Key | Value
--|--
Status | 200 OK
ETag | {etagValue}
Content-Type | application/ld+json
Allow | GET
Link | ```<http://www.w3.org/ns/ldp#RDFSource>; rel="type",<http://www.w3.org/ns/ldp#Resource>; rel="type"```

###### Body (example)
```
{
  "@context": {
    "dcat": "http://www.w3.org/ns/dcat#",
    "dcterms": "http://purl.org/dc/terms/"
  },
  "@id": "/organizations/4/datasets/4",
  "@type": "dcat:Dataset",
  "dcterms:title": "Nationale Bibliografie",
  "dcterms:identifier": {
    "@id": "https://data.kb.nl/datasets/nationale-bibliografie"
  },
  "dcterms:publisher": {
    "@id": "/organizations/4"
  },
  "dcterms:created": {
    "@type": "http://www.w3.org/2001/XMLSchema#dateTime",
    "@value": "2017-05-16T10:00:00"
  },
  "dcterms:modified": {
    "@type": "http://www.w3.org/2001/XMLSchema#dateTime",
    "@value": "2017-05-16T10:00:00"
  }
}
```

###### Errors
Status | Error description
--|--
400 Bad Request | The request is invalid
404 Not Found | The resource does not exist

> **TODO** rewrite body based on (schema.org based) publication model