
Register-function: API
==============================

# <a id="contents">Contents</a>

* [Contents](#contents)
* [Introduction](#intro)
* [Status of this document](#status)
* [Design principles](#design-principles)
* [Endpoints](#endpoints)
  * [Dataset descriptions and catalogs](#endpoint-dataset)
    * [Register a URI of a dataset description](#endpoint-dataset-register)
    * [Validate a dataset description](#endpoint-dataset-validate)  
    * [Search dataset descriptions](#endpoint-dataset-search)
  
  
# <a id="intro">Introduction</a>

This document outlines the _application programming interface_ (API) of NDE's _proof of concept_ Register. It describes the endpoints that can be used by dataset producing applications, such as the collection management systems of cultural heritage institutions. Key in the design is the fact that heritage organization publish descriptions of the datasets they provide. See [requirements for datasets](https://netwerk-digitaal-erfgoed.github.io/requirements-datasets/) for how to describe datasets. This API focuses on getting the URI's of dataset descriptions and catalogs in order to crawl, fetch, validatie, convert and store (a part of) these dataset descriptions.

# <a id="status">Status of this document</a>

This document is published for examination, experimental implementation and evaluation. It is not an official specification.

This document is a fourth iteration of a specification in which dataset descriptions are crawled after registration. As organisation information is part of the (crawled) dataset descriptions, there is no need for a seperate administration of organisations. Authentication/authorization is not needed as data is crawled from the websites of (to be trusted) publishers of dataset descriptions.

Elements not yet included: API versioning and API rate limiting.

filtering, searching, including wildcards, aliases for common queries (eg. list all organisation with 'archive' in the name), paginating, sorting (only nice-to-have).


# <a id="design-principles">Design principles</a>

1. The API uses modern API standards and best practices
2. The API uses [Linked Data Platform](https://www.w3.org/TR/ldp/) (LDP) for HTTP operations on web resources
3. The API uses [JSON-LD](http://json-ld.org/) as the preferred format for transmitting data
4. The API uses established Linked Data vocabularies, one of which is the (schema.org based) [NDE publication model](https://netwerk-digitaal-erfgoed.github.io/requirements-datasets/)

# <a id="endpoints">Endpoints</a>


## <a id="endpoint-dataset">Dataset descriptions and catalogs</a>
A dataset description contains the metadata of a dataset describing its characteristics, both administrative, descriptive and structural. A data catalog contains one or more datasets descriptions.

### <a id="endpoint-dataset-register">Register a URI of a dataset description or catalog</a>

#### Request

<code>POST https://demo.netwerkdigitaalerfgoed.nl/register-api/datasets</code>

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
{ "status":"deleted" } | URI was deleted (because URI already known and HEAD request to URI results in a 404)

###### Errors
Status | Error description
--|--
404 Not Found | The URI does not exist.
400 Bad Request | The URI could not be fetched.
403 Forbidden | The domain name part of the URI is not on the access list.

### <a id="endpoint-dataset-validate">Validate a dataset description</a> - not yet implemented

#### Request

<code>PUT https://demo.netwerkdigitaalerfgoed.nl/register-api/dataset/validate</code>

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

TODO

###### Headers
Key | Value
--|--
Status | 200 OK

###### Contents
TODO: **recognized data model, eg. schema.org/Dataset, DCAT, and information about absence of required properties or invalid parts, SHACL based validation format**

###### Errors
Status | Error description
--|--
404 Not Found | The URI does not exist.

### <a id="endpoint-dataset-search">Search dataset descriptions</a> - not yet implemented

#### Request

<code>GET https://demo.netwerkdigitaalerfgoed.nl/register-api/datasets</code>

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


###### Not yet specified (TODO)

> filtering, searching, including wildcards, aliases for common queries (eg. list all organisation with 'archive' in the name), paginating, sorting (only nice-to-have).


