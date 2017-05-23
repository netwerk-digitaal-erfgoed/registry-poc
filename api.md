Registry: API
==============================

# <a id="contents">Contents</a>

* [Contents](#contents)
* [Introduction](#intro)
* [Status of this document](#status)
* [Design principles](#design-principles)
* [Endpoints](#endpoints)
  * [Organization profiles](#endpoint-organization)
    * [List all organization profiles](#endpoint-organization-list)
    * [Register an organization profile](#endpoint-organization-register)
    * [Retrieve an organization profile](#endpoint-organization-retrieve)
    * [Update an organization profile](#endpoint-organization-update)
    * [Unregister an organization profile](#endpoint-organization-unregister)
  * [Dataset profiles](#endpoint-dataset)
    * [List all dataset profiles](#endpoint-dataset-list)
    * [Register a dataset profile](#endpoint-dataset-register)
    * [Retrieve a dataset profile](#endpoint-dataset-retrieve)
    * [Update a dataset profile](#endpoint-dataset-update)
    * [Unregister a dataset profile](#endpoint-dataset-unregister)
  * [Object profiles](#endpoint-object)
    * [Notify the Registry about an object](#endpoint-object-notify)

# <a id="intro">Introduction</a>

This document outlines the _application programming interface_ (API) of NDE's _proof of concept_ Registry. It describes the endpoints that can be used by consuming applications, such as the collection management systems of cultural heritage institutions.

# <a id="status">Status of this document</a>

This document is published for examination, experimental implementation and evaluation. It is not an official specification.

# <a id="design-principles">Design principles</a>

1. The API uses modern API standards and best practices
2. The API uses [Linked Data Platform](https://www.w3.org/TR/ldp/) (LDP) for HTTP operations on web resources
3. The API uses [JSON-LD](http://json-ld.org/) as the preferred format for transmitting data
4. The API uses established Linked Data vocabularies (e.g. DCAT, Dublin Core, FOAF)
5. The API doesn't support authentication or authorization (yet)
6. The API doesn't support pagination (yet)

# <a id="endpoints">Endpoints</a>

## <a id="endpoint-organization">Organization profiles</a>
An organization profile contains the metadata of an organization describing its characteristics, both administrative, descriptive and structural.

### <a id="endpoint-organization-list">List all organization profiles</a>

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
  "ldp:contains": {
    {"@id": "/organizations/1"},
    {"@id": "/organizations/2"},
    {"@id": "/organizations/3"}
  }
}
```

###### Errors
Status | Error description
--|--
400 Bad Request | The request is invalid

### <a id="endpoint-organization-register">Register an organization profile</a>

#### Request

<code>POST /organizations</code>

###### Headers
Key | Value
--|--
Content-Type | application/ld+json
Link | ```<http://www.w3.org/ns/ldp#RDFSource>; rel="type",<http://www.w3.org/ns/ldp#Resource>; rel="type"```

###### Body (example)
```
{
  "@context": {
    "dcterms": "http://purl.org/dc/terms/",
    "foaf": "http://xmlns.com/foaf/0.1/"
  },
  "@type": "foaf:Organization",
  "foaf:name": "Koninklijke Bibliotheek",
  "dcterms:identifier": {
    "@id": "https://www.kb.nl/"
  }
}
```

#### Response

###### Headers
Key | Value
--|--
Status | 201 Created
Location | /organizations/{organizationId}
Link | ```<http://www.w3.org/ns/ldp#RDFSource>; rel="type",<http://www.w3.org/ns/ldp#Resource>; rel="type"```
Content-Length | 0

###### Errors
Status | Error description
--|--
400 Bad Request | The request is invalid

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
Allow | GET,PATCH,DELETE
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

###### Errors
Status | Error description
--|--
400 Bad Request | The request is invalid
404 Not Found | The resource does not exist

### <a id="endpoint-organization-update">Update an organization profile</a>

#### Request

<code>PATCH /organizations/{organizationId}</code>

###### Headers
Key | Value
--|--
If-Match | {etagValue}
Content-Type | application/ld+json
Link | ```<http://www.w3.org/ns/ldp#RDFSource>; rel="type",<http://www.w3.org/ns/ldp#Resource>; rel="type"```

###### Body (example)
```
{
  "@context": {
    "dcterms": "http://purl.org/dc/terms/",
    "foaf": "http://xmlns.com/foaf/0.1/"
  },
  "@type": "foaf:Organization",
  "foaf:name": "Koninklijke Bibliotheek",
  "dcterms:identifier": {
    "@id": "https://www.kb.nl/"
  }
}
```

#### Response

###### Headers
Key | Value
--|--
Status | 204 No Content
Link | ```<http://www.w3.org/ns/ldp#RDFSource>; rel="type",<http://www.w3.org/ns/ldp#Resource>; rel="type"```
Content-Length | 0

###### Errors
Status | Error description
--|--
400 Bad Request | The request is invalid
404 Not Found | The resource does not exist
428 Precondition Required | The precondition header is missing (If-Match)
412 Precondition Failed | The precondition header is invalid (If-Match)

### <a id="endpoint-organization-unregister">Unregister an organization profile</a>

#### Request

<code>DELETE /organizations/{organizationId}</code>

###### Headers
Key | Value
--|--
If-Match | {etagValue}

#### Response

###### Headers
Key | Value
--|--
Status | 204 No Content
Link | ```<http://www.w3.org/ns/ldp#RDFSource>; rel="type",<http://www.w3.org/ns/ldp#Resource>; rel="type"```
Content-Length | 0

###### Errors
Status | Error description
--|--
400 Bad Request | The request is invalid
404 Not Found | The resource does not exist
428 Precondition Required | The precondition header is missing (If-Match)
412 Precondition Failed | The precondition header is invalid (If-Match)

## <a id="endpoint-dataset">Dataset profiles</a>
A dataset profile contains the metadata of a dataset describing its characteristics, both administrative, descriptive and structural.

### <a id="endpoint-dataset-list">List all dataset profiles</a>

#### Request

<code>GET /organizations/{organizationId}/datasets</code>

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
  "ldp:contains": {
    {"@id": "/organizations/4/datasets/1"},
    {"@id": "/organizations/4/datasets/2"},
    {"@id": "/organizations/4/datasets/3"}
  }
}
```

###### Errors
Status | Error description
--|--
400 Bad Request | The request is invalid
404 Not Found | The resource does not exist

### <a id="endpoint-dataset-register">Register a dataset profile</a>

#### Request

<code>POST /organizations/{organizationId}/datasets</code>

###### Headers
Key | Value
--|--
Content-Type | application/ld+json
Link | ```<http://www.w3.org/ns/ldp#RDFSource>; rel="type",<http://www.w3.org/ns/ldp#Resource>; rel="type"```

###### Body (example)
```
{
  "@context": {
    "dcat": "http://www.w3.org/ns/dcat#",
    "dcterms": "http://purl.org/dc/terms/"
  },
  "@type": "dcat:Dataset",
  "dcterms:title": "Nationale Bibliografie",
  "dcterms:identifier": {
    "@id": "https://data.kb.nl/datasets/nationale-bibliografie"
  },
  "dcterms:publisher": {
    "@id": "/organizations/4"
  }
}
```

#### Response

###### Headers
Key | Value
--|--
Status | 201 Created
Location | /organizations/{organizationId}/datasets/{datasetId}
Link | ```<http://www.w3.org/ns/ldp#RDFSource>; rel="type",<http://www.w3.org/ns/ldp#Resource>; rel="type"```
Content-Length | 0

###### Errors
Status | Error description
--|--
400 Bad Request | The request is invalid
404 Not Found | The resource does not exist

### <a id="endpoint-dataset-retrieve">Retrieve a dataset profile</a>

#### Request

<code>GET /organizations/{organizationId}/datasets/{datasetId}</code>

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
Allow | GET,PATCH,DELETE
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

### <a id="endpoint-dataset-update">Update a dataset profile</a>

#### Request

<code>PATCH /organizations/{organizationId}/datasets/{datasetId}</code>

###### Headers
Key | Value
--|--
If-Match | {etagValue}
Content-Type | application/ld+json
Link | ```<http://www.w3.org/ns/ldp#RDFSource>; rel="type",<http://www.w3.org/ns/ldp#Resource>; rel="type"```

###### Body (example)
```
{
  "@context": {
    "dcat": "http://www.w3.org/ns/dcat#",
    "dcterms": "http://purl.org/dc/terms/"
  },
  "@type": "dcat:Dataset",
  "dcterms:title": "Nationale Bibliografie",
  "dcterms:identifier": {
    "@id": "https://data.kb.nl/datasets/nationale-bibliografie"
  },
  "dcterms:publisher": {
    "@id": "/organizations/4"
  }
}
```

#### Response

###### Headers
Key | Value
--|--
Status | 204 No Content
Link | ```<http://www.w3.org/ns/ldp#RDFSource>; rel="type",<http://www.w3.org/ns/ldp#Resource>; rel="type"```
Content-Length | 0

###### Errors
Status | Error description
--|--
400 Bad Request | The request is invalid
404 Not Found | The resource does not exist
428 Precondition Required | The precondition header is missing (If-Match)
412 Precondition Failed | The precondition header is invalid (If-Match)

### <a id="endpoint-dataset-unregister">Unregister a dataset profile</a>

#### Request

<code>DELETE /organizations/{organizationId}/datasets/{datasetId}</code>

###### Headers
Key | Value
--|--
If-Match | {etagValue}

#### Response

###### Headers
Key | Value
--|--
Status | 204 No Content
Link | ```<http://www.w3.org/ns/ldp#RDFSource>; rel="type",<http://www.w3.org/ns/ldp#Resource>; rel="type"```
Content-Length | 0

###### Errors
Status | Error description
--|--
400 Bad Request | The request is invalid
404 Not Found | The resource does not exist
428 Precondition Required | The precondition header is missing (If-Match)
412 Precondition Failed | The precondition header is invalid (If-Match)

## <a id="endpoint-object">Object profiles</a>
An object profile is a subset of the metadata of an object containing its URI and URI references to (a) the terms that characterize the object and (b) other, related objects (for instance, the parts of a multi-volume book).

Contrary to an organization and dataset profile, the Registry does not assign a URI to an object profile. An object profile is merely a container of backlinks, not a resource on its own. If therefore cannot be registered, updated or unregistered like an organization or dataset profile. Instead, an object profile is maintained via notifications.

We’re considering [Webmention](https://www.w3.org/TR/webmention/) as the protocol for sending and receiving notifications. The basic flow is as follows:

1. A curator at a heritage institution creates or updates an object description in his collection management system (CMS), including a term from a terminology source.
2. The CMS sends a notification to the Webmention endpoint of the institution in the Registry, containing the URI of the object description ("source") and the URI of the term ("target").
3. The Registry receives the notification.
4. The Registry verifies that the target (the URI of the term) is valid.
5. The Registry verifies that the source (the URI of the object description) is valid and contains a link to the target (by making a GET request to the source).

### <a id="endpoint-object-notify">Notify the Registry about an object</a>

#### Request

<code>POST /organizations/{organizationId}/datasets/{datasetId}/objects</code>

###### Headers
Key | Value
--|--
Content-Type | application/x-www-form-urlencoded

###### Body (example)
```
source=http://hdl.handle.net/10934/RM0001.COLLECT.446245&
target=http://iconclass.org/45K1.json
```

#### Response

###### Headers
Key | Value
--|--
Status | 202 Accepted
Content-Length | 0

###### Errors
Status | Error description
--|--
400 Bad Request | The request is invalid
404 Not Found | The resource does not exist
