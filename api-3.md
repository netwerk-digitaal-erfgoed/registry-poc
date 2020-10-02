
Register-function: API
==============================

# <a id="contents">Contents</a>

* [Contents](#contents)
* [Introduction](#intro)
* [Status of this document](#status)
* [Design principles](#design-principles)
* [Endpoints](#endpoints)
  * [Dataset descriptions](#endpoint-dataset)
    * [Register a URI of a dataset description](#endpoint-dataset-register)
    * [List dataset descriptions](#endpoint-dataset-list)
    * [Retrieve a dataset description](#endpoint-dataset-retrieve)
  * [Organization profiles](#endpoint-organization)
    * [List organization profiles](#endpoint-organization-list)
    * [Retrieve an organization profile](#endpoint-organization-retrieve)
  
# <a id="intro">Introduction</a>

This document outlines the _application programming interface_ (API) of NDE's _proof of concept_ Register. It describes the endpoints that can be used by consuming applications, such as the collection management systems of cultural heritage institutions.

# <a id="status">Status of this document</a>

This document is published for examination, experimental implementation and evaluation. It is not an official specification.

This document is a third iteration of a specification in which dataset descriptions are crawled after registration. As organisation information is part of the (crawled) dataset descriptions, there is no need for a seperate administration of organisations. Authentication/authorization is not needed as data is crawled from the websites of (to be trusted) publishers of dataset descriptions.

Elements not specified yet included: filtering, searching, including wildcards, aliases for common queries (eg. list all organisation with 'archive' in the name), paginating, sorting (only nice-to-have).

TODO: 
 - API versioning
 - API rate limiting

# <a id="design-principles">Design principles</a>

1. The API uses modern API standards and best practices
2. The API uses [Linked Data Platform](https://www.w3.org/TR/ldp/) (LDP) for HTTP operations on web resources
3. The API uses [JSON-LD](http://json-ld.org/) as the preferred format for transmitting data
4. The API uses established Linked Data vocabularies, one of which is the (schema.org based) NDE publication model 

# <a id="endpoints">Endpoints</a>


## <a id="endpoint-dataset">Dataset descriptions</a>
A dataset description contains the metadata of a dataset describing its characteristics, both administrative, descriptive and structural.

### <a id="endpoint-dataset-register">Register a URI of a dataset description</a>

#### Request

<code>POST /dataset/register</code>

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

#### Response

###### Headers
Key | Value
--|--
Status | 200 OK

###### Contents
Body | Description
--|--
{ "status":"added" } | URI added, will be crawled soon
{ "status":"updated" } | URI already known, will be crawled soon
{ "status":"delete" } | URI was deleted (because URI already known and HEAD request to URI results in a 404)

###### Errors
Status | Error description
--|--
404 Not Found | The URI does not exist.
400 Bad Request | The URI could not be fetched.
403 Forbidden | The domain name part of the URI is not on the access list.

### <a id="endpoint-dataset-register">Validate a dataset description</a>

#### Request

<code>POST /dataset/validate</code>

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

#### Response

###### Headers
Key | Value
--|--
Status | 200 OK

###### Contents
TODO: **recognized data model, eg. schema.org/Dataset, DCAT, and information about absence of required properties or invalid parts**

###### Errors
Status | Error description
--|--
404 Not Found | The URI does not exist.

### <a id="endpoint-dataset-list">List dataset descriptions</a>

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
	"@context":"https://schema.org/", 
	"@type":"Dataset", 
	"@id":"http://data.bibliotheken.nl/id/dataset/rise-alba", 
	"name":"Alba amicorum van de Koninklijke Bibliotheek", 
	"description":"Alba amicorum van de Koninklijke Bibliotheek, een dataset gedefinieerd voor het Europeana Rise of Literacy project.", 	
	"url":"https://www.kb.nl/bronnen-zoekwijzers/kb-collecties/moderne-handschriften-vanaf-ca-1550/alba-amicorum", 
	"identifier": "http://data.bibliotheken.nl/id/dataset/rise-alba", 
	"keywords":[ "alba amicorum" ], 
	"license" : "http://creativecommons.org/publicdomain/zero/1.0/", 
	"creator":{ "@type":"Organization", "url": "https://www.kb.nl/", 
	"name":"Koninklijke Bibliotheek", 
	"sameAs":"https://ror.org/02w4jbg70" }, 
	"distribution":[ 
		{ "@type":"DataDownload", "encodingFormat":"application/rdf+xml", "contentUrl":"http://data.bibliotheken.nl/id/dataset/rise-alba.rdf" }, 
		{ "@type":"DataDownload", "encodingFormat":"text/turtle", "contentUrl":"http://data.bibliotheken.nl/id/dataset/rise-alba.ttl" }, 
		{ "@type":"DataDownload", "encodingFormat":"application/n-triples", "contentUrl":"http://data.bibliotheken.nl/id/dataset/rise-alba.nt" }, 
		{ "@type":"DataDownload", "encodingFormat":"application/ld+json", "contentUrl":"http://data.bibliotheken.nl/id/dataset/rise-alba.json" } 
		] }
```

###### Errors
Status | Error description
--|--
400 Bad Request | The request is invalid
404 Not Found | The resource does not exist

> **TODO** rewrite body based on (schema.org based) publication model
> 
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

{ "@context":"https://schema.org/", 
  "@type":"Organization", 
  "url": "https://www.kb.nl/", 
  "name":"Koninklijke Bibliotheek", 
  "sameAs":"https://ror.org/02w4jbg70" 
}
```

###### Errors
Status | Error description
--|--
400 Bad Request | The request is invalid
404 Not Found | The resource does not exist