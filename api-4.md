Register-function: API
==============================

# <a id="contents">Contents</a>

* [Contents](#contents)
* [Introduction](#intro)
* [Status of this document](#status)
* [Design principles](#design-principles)
* [Endpoints](#endpoints)
  * [Dataset descriptions and catalogs](#endpoint-dataset)
    * [Register a URL of a dataset description](#endpoint-dataset-register)
    * [Validate a dataset description](#endpoint-dataset-validate)  
    * [Search dataset descriptions](#endpoint-dataset-search)
  
  
# <a id="intro">Introduction</a>

This document outlines the _application programming interface_ (API) of NDE's _proof of concept_ Register. It describes the endpoints that can be used by dataset producing applications, such as the collection management systems of cultural heritage institutions. Key in the design is the fact that heritage organization publish descriptions of the datasets they provide. See [requirements for datasets](https://netwerk-digitaal-erfgoed.github.io/requirements-datasets/) for how to describe datasets. This API focuses on getting the URL's of dataset descriptions and catalogs in order to crawl, fetch, validatie, convert and store (a part of) these dataset descriptions.

# <a id="status">Status of this document</a>

This document is published for examination, experimental implementation and evaluation. It is not an official specification.

This document is a fourth iteration of a specification in which dataset descriptions are crawled after registration. As organisation information is part of the (crawled) dataset descriptions, there is no need for a seperate administration of organisations. Authentication/authorization is not needed as data is crawled from the websites of (to be trusted) publishers of dataset descriptions.

Elements not yet included: API versioning and API rate limiting.

# <a id="design-principles">Design principles</a>

1. The API uses modern API standards and best practices
2. The API uses [Linked Data Platform](https://www.w3.org/TR/ldp/) (LDP) for HTTP operations on web resources
3. The API uses [JSON-LD](http://json-ld.org/) as the preferred format for transmitting data
4. The API uses established Linked Data vocabularies, one of which is the (schema.org based) [NDE publication model](https://netwerk-digitaal-erfgoed.github.io/requirements-datasets/)

# <a id="endpoints">Endpoints</a>


## <a id="endpoint-dataset">Dataset descriptions and catalogs</a>
A dataset description contains the metadata of a dataset describing its characteristics, both administrative, descriptive and structural. A data catalog contains one or more datasets descriptions.

### <a id="endpoint-dataset-register">Register a URL of a dataset description or catalog</a>

#### Request

<code>POST https://demo.netwerkdigitaalerfgoed.nl/register-api/datasets</code>

##### Headers
Key | Value
--|--
Content-Type | application/ld+json
Link | ```<http://www.w3.org/ns/ldp#RDFSource>; rel="type",<http://www.w3.org/ns/ldp#Resource>; rel="type"```

##### Contents

The URL which is registered via this endpoint can depict the following resources:
- HTML page with one or more dataset descriptions inlined in JSON-LD
- HTML page with dataset catalog (=set of dataset descriptions) inlined in JSON-LD (not yet implemented)
- HTML page with one or more dataset description as microdata (not yet implemented)
- HTML page with dataset catalog (=set of dataset descriptions) as microdata (not yet implemented)
- RDF resource with one or more datasets descriptions, where content negotionation is used to fetch the resource
- RDF resource with data catalog, where content negotionation is used to fetch the resource (not yet implemented)

##### Body (example)
```
{
  "@id": "https://demo.netwerkdigitaalerfgoed.nl/datasets/kb/2.html"
}
```

##### Example (Curl)
```
curl 'https://demo.netwerkdigitaalerfgoed.nl/register-api/datasets' \
  -H 'link: <http://www.w3.org/ns/ldp#RDFSource>; rel="type",<http://www.w3.org/ns/ldp#Resource>; rel="type"' \
  -H 'content-type: application/ld+json' \
  --data-binary '{"@id":"https://demo.netwerkdigitaalerfgoed.nl/datasets/kb/2.html"}'
```

#### Response

##### Headers
Key | Value
--|--
Status | 200 OK

##### Contents
Body | Description
--|--
{ "status":"added" } | URL added, will be crawled soon
{ "status":"updated" } | URL already known, will be crawled soon
{ "status":"deleted" } | URL was deleted (because URL already known and HEAD request to URL results in a 404)

##### Errors
Status | Error description
--|--
404 Not Found | The URL does not exist.
400 Bad Request | The URL could not be fetched.
403 Forbidden | The domain name part of the URL is not on the access list.
406 Not Acceptable |  The URL could be fetched but a dataset was not found.

### <a id="endpoint-dataset-validate">Validate a dataset description</a>

#### Request

<code>PUT https://demo.netwerkdigitaalerfgoed.nl/register-api/datasets/validate</code>

##### Headers
Key | Value
--|--
Content-Type | application/ld+json
Link | ```<http://www.w3.org/ns/ldp#RDFSource>; rel="type",<http://www.w3.org/ns/ldp#Resource>; rel="type"```

##### Body (example)

```
{
  "@id": "https://data.kb.nl/datasets/nationale-bibliografie"
}
```
##### Example (Curl)

```
curl -i -X PUT 'https://demo.netwerkdigitaalerfgoed.nl/register-api/datasets/validate' \
  -H 'link: <http://www.w3.org/ns/ldp#RDFSource>; rel="type",<http://www.w3.org/ns/ldp#Resource>; rel="type"' \
  -H 'content-type: application/ld+json' \
  --data-binary '{"@id":"https://demo.netwerkdigitaalerfgoed.nl/datasets/kb/2.html"}'
```

#### Response

When the validation of the dataset description finds no violations, the output is empty (HTTP 200). Otherwise (HTTP 400), the results of the (SHALC) validation is shown as triples.

##### Headers
Key | Value
--|--
Status | 200 OK

##### Contents (example of found violations)

Example validation reports which indicates the dataset description is missing a creator and a description.

```
_:b5 a <http://www.w3.org/ns/shacl#ValidationResult>;
    <http://www.w3.org/ns/shacl#resultSeverity> <http://www.w3.org/ns/shacl#Violation>;
    <http://www.w3.org/ns/shacl#sourceConstraintComponent> <http://www.w3.org/ns/shacl#MinCountConstraintComponent>;
    <http://www.w3.org/ns/shacl#sourceShape> _:df_7_4.
_:df_7_4 <http://www.w3.org/ns/shacl#path> <http://schema.org/creator>;
    <http://www.w3.org/ns/shacl#minCount> 1;
    <http://www.w3.org/ns/shacl#class> <http://schema.org/Organization>;
    <http://www.w3.org/ns/shacl#node> "schema:CreatorShape".
_:b5 <http://www.w3.org/ns/shacl#focusNode> <http://data.bibliotheken.nl/id/dataset/gtt>;
    <http://www.w3.org/ns/shacl#resultPath> <http://schema.org/creator>;
    <http://www.w3.org/ns/shacl#resultMessage> "Less than 1 values".
_:b6 a <http://www.w3.org/ns/shacl#ValidationResult>;
    <http://www.w3.org/ns/shacl#resultSeverity> <http://www.w3.org/ns/shacl#Violation>;
    <http://www.w3.org/ns/shacl#sourceConstraintComponent> <http://www.w3.org/ns/shacl#MinCountConstraintComponent>;
    <http://www.w3.org/ns/shacl#sourceShape> _:df_7_2.
_:df_7_2 <http://www.w3.org/ns/shacl#path> <http://schema.org/description>;
    <http://www.w3.org/ns/shacl#minCount> 1.
_:b6 <http://www.w3.org/ns/shacl#focusNode> _:n3-5;
    <http://www.w3.org/ns/shacl#resultPath> <http://schema.org/description>;
    <http://www.w3.org/ns/shacl#resultMessage> "Less than 1 values".
```

##### Errors
Status | Error description
--|--
400 Bad Request | There were validation errors.
404 Not Found | The URL does not exist.
406 Not Acceptable |  The URL could be fetched but a dataset was not found.

### <a id="endpoint-dataset-search">Search dataset descriptions</a> - not yet implemented (meanwhile use https://triplestore.netwerkdigitaalerfgoed.nl/)

#### Request

<code>GET https://demo.netwerkdigitaalerfgoed.nl/register-api/datasets</code>

##### Headers
Key | Value
--|--
Accept | application/ld+json

#### Response

##### Headers
Key | Value
--|--
Status | 200 OK
ETag | {etagValue}
Content-Type | application/ld+json
Accept-Post | application/ld+json
Allow | POST,GET
Link | ```<http://www.w3.org/ns/ldp#BasicContainer>; rel="type",<http://www.w3.org/ns/ldp#Resource>; rel="type"```

##### Body (example)
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

##### Errors
Status | Error description
--|--
400 Bad Request | The request is invalid
404 Not Found | The resource does not exist


##### Not yet specified (TODO)

> filtering, searching, including wildcards, aliases for common queries (eg. list all organisation with 'archive' in the name), paginating, sorting (only nice-to-have).
