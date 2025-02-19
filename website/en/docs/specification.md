# OpenCodeList Specification

#### Version 0.3.0

The key words "MUST", "MUST NOT", "SHOULD", "SHOULD NOT", "MAY", and "REQUIRED" in this document are to be interpreted as described in [RFC2119 and RFC8174](https://tools.ietf.org/html/bcp14), when, and only when, they appear in all capitals, as shown here.

This specification is licensed under the [Apache License, Version 2.0](https://opensource.org/license/apache-2-0/).

## Introduction

OpenCodeList defines a generic standard data format for representing code lists. Based on the [JSON Standard](https://datatracker.ietf.org/doc/html/rfc8259), this format can be easily generated and read by almost any programming language. Documents in the OpenCodeList format can be validated for syntactic correctness using the [OpenCodeList Document Schema](https://github.com/openpotato/opencodelist/tree/main/schemas/v0.3/schema.json).

OpenCodeList can be used for exchanging code lists between services or applications, as a representation format for official code lists, or as a response format for API requests (e.g., for RESTful web services).

### What are Code Lists?

Code lists are structured collections of codes or keys used for the identification and classification of data. These code lists are essential in numerous fields such as databases, management systems, scientific research, and industrial applications. They help in organising, storing, and retrieving data consistently and efficiently.

Code lists play a crucial role in the standardisation and harmonisation of data. By using standardised codes, different systems and organisations can interpret and exchange data uniformly.

Examples of code lists include:

+ International codes for countries, languages, and currencies by the International Organization for Standardization (ISO).

+ National area codes (e.g., municipal codes of the Federal Republic of Germany).

+ National and subnational codes in the public sector (e.g., statistical codes from statistical authorities).

In principle, any data selection can be mapped to a code list; even a simple Boolean value can be represented by the codes *No* and *Yes*.

### How are Code Lists structured?

There is no natural law dictating the structure of code lists. However, a tabular representation is often quickly agreed upon in most cases.

The simplest form of such a tabular code list consists of two columns: *Code* and *Name*.

Here’s an example of a country directory:

Code | Name       
-----|-----
AT   | Austria    
CH   | Switzerland
DE   | Germany    

Code lists can potentially contain as many columns as needed. Here’s an example of a country directory with three columns:

Code | Short Name    | Full Name                        
-----|---------------|----------
AT   | Austria       | Republic of Austria              
CH   | Switzerland   | Swiss Confederation              
DE   | Germany       | Federal Republic of Germany      

Code lists can also reference one another. Here’s an example of a country directory with a reference to a separate continent directory:

Code | Short Name    | Full Name                        | Continent 
-----|---------------|----------------------------------|----------
DE   | Germany       | Federal Republic of Germany      | EU        
MA   | Morocco       | Kingdom of Morocco               | AF        
AU   | Australia     | Commonwealth of Australia        | OC        

The corresponding continent directory might look like this:

Code | Name    
-----|-----
AF   | Africa  
AM   | Americas
AS   | Asia    
EU   | Europe  
OC   | Oceania 

Code lists can also have more than one code. Here’s an example of a country directory with various ISO3166 codes:

Alpha2Code | Alpha3Code | NumericCode | Name       
-----------|------------|-------------|-----
AT         | AUT        | 040         | Austria    
CH         | CHE        | 756         | Switzerland
DE         | DEU        | 276         | Germany    

Let’s take it a step further with this multilingual country directory. In this case, the combination of the key and language (also represented by a language code) ensures uniqueness:

Code | Language | Name        
-----|----------|-----
AT   | de       | Österreich  
AT   | en       | Austria     
CH   | de       | Schweiz     
CH   | en       | Switzerland 
DE   | de       | Deutschland 
DE   | en       | Germany     

In addition to text and numbers, code lists can also define complex column types, such as those filled with XML, JSON, or HTML.

### What Makes Code Lists Complex?

#### Format and Semantics

When you think of code lists, a mental image of an Excel spreadsheet with a few columns and rows might quickly come to mind. But what do the columns actually mean? Is the code always in the first column? Is the content of a column always to be interpreted as plain text? And why Excel? It’s a rather complex data format that’s not very suitable for automated processing of code lists. CSV would definitely be easier to handle but still doesn’t answer the first three questions.

#### Versioning

Code lists can change over time. In such cases, there is both a current version of a code list as well as older versions. With these changes, both the number of codes and the meaning of individual codes may evolve.

#### Dependencies

Code lists can have dependencies on one another, meaning that values from one code list can reference codes in another code list. These references must also account for possible versioning of the other code list.

#### Multilingualism

When code lists are used across language barriers, they often need to be adapted for multiple languages. A single code may have different labels in different languages.

#### Custom Code Lists

When we talk about code lists, it’s not always about standardised codes defined by a committee, organisation, or institution. Often, it’s also about codes used by a small group of users for a specific field or even a particular application. These must coexist conflict-free with established standard codes.

### Are There Already Standards for Code Lists?

Yes, there are: The [Organization for the Advancement of Structured Information Standards (OASIS)](https://www.oasis-open.org) has defined an XML-based standard for code lists with [Code List Representation (genericode)](https://docs.oasis-open.org/codelist/genericode/v1.0/genericode-v1.0.html).

> The OASIS Code List Representation format, “genericode”, is a single model and XML format (with a W3C XML Schema) that can encode a broad range of code list information. The XML format is designed to support interchange or distribution of machine-readable code list information between systems.  Note that genericode is not designed as a run-time format for accessing code list information, and is not optimized for such usage.  Rather, it is designed as an interchange format that can be transformed into formats suitable for run-time usage, or loaded into systems that perform run-time processing using code list information.

This sounds good, but there’s a catch. There is no JSON representation of "genericode".

> Recognising the custom use of JSON in a tight binding between user-defined processes, the committee sees no purpose served by standardising a JSON syntax for the genericode vocabulary.

This means there is no official support for JSON as a data format and thus no official JSON schema.

So, if you want to represent code lists in XML, the OASIS standard is highly recommended. However, if you prefer to work with JSON, OpenCodeList offers a suitable alternative. Of course, OpenCodeList is heavily influenced by the *OASIS Code List Representation Format* and strives to remain semantically compatible with it as much as possible.

### Why JSON? XML Is Great!

Of course, XML is a great, standardised format that leaves hardly anything to be desired. However, the reason for using a JSON representation of code lists lies in the explicit desire to use JSON:

+ In the world of cloud-based services, JSON has largely become the de facto payload format for RESTful APIs. While such an API could return XML as a payload, this would create an additional dependency, both on the server side (generating XML) and the client side (consuming XML). Instead of dealing with one format (JSON), code lists would require handling an additional format (XML), increasing complexity.

+ JSON is more compact since its syntax is less verbose. With extensive code lists, this has a positive impact on performance. In general, JSON is faster to parse than XML as it carries less syntactic overhead, and JSON parsers in most modern programming languages are now highly optimised.

## Definitions

### OpenCodeList Document

An OpenCodeList document is a self-contained resource that defines and describes either a code list or a set of code lists. It MUST contain at least the `opencodelist` field and either `codeList` or `codeListSet`, but not both. An OpenCodeList document uses and conforms to the OpenCodeList specification.

### Code List

A code list is a classic relational table with columns and data rows, where at least one column should serve as the key (code). OpenCodeList allows for defining generic code lists.

### Code List Set

A code list set is a list of references to external code lists. A code list set can be used to group different versions of a code list.

## Specification

### Versioning

The OpenCodeList specification is versioned according to the `major.minor.patch` scheme. The major-minor part of the version number (e.g., `0.3`) MUST indicate the functional set of the specification. Patch versions address errors in this document or provide clarifications to this document, not to the functionality. Tools that support OpenCodeList version `0.3` MUST be compatible with all `0.3.*` versions of OpenCodeList. The patch version SHOULD NOT be considered by the tools, so that no distinction is made between `0.3.0` and `0.3.1`.

An OpenCodeList document always contains a mandatory `opencodelist` field that specifies the version of the OpenCodeList specification being used.

### Format

An OpenCodeList document that conforms to the OpenCodeList specification is itself a JSON object that can be represented in JSON format.

All field names in the specification are case-sensitive. The schema defines two types of fields: fixed fields that have a declared name, and free fields whose names MUST conform to a specific pattern. Additional fields MUST have unique names within the contained JSON object.

#### JSON Schema

[JSON Schema](https://json-schema.org/) is a specification for defining JSON data structures. A JSON schema itself is expressed declaratively using [JSON](https://www.json.org/). The [OpenCodeList Document Schema](https://github.com/openpotato/opencodelist/tree/main/schemas/v0.3/schema.json) is a JSON schema for OpenCodeList documents.

#### Multilingual Support

OpenCodeList supports multilingualism, i.e. text columns in a code list can optionally be provided with an [IETF BCP 47 language tag](https://www.rfc-editor.org/rfc/bcp/bcp47.txt).   

Example:

``` json
"columns": [
  {
    "id": "col-1",
    "name": "Beschreibung",
    "description": "Column with German description",
    "type": "string",
    "language": "de"
  },
  {
    "id": "col-2",
    "name": "Description",
    "description": "Column with English description",
    "type": "string",
    "language": "en"
  }
],
```

#### Language Tags

IETF BCP 47 (Best Current Practice 47) is a document that defines the rules and procedures for creating and using language tags. These tags are used to identify the language of content on the Internet. BCP 47 is maintained by the Internet Engineering Task Force (IETF) and consists of two RFCs (Request for Comments): [RFC 5646](https://datatracker.ietf.org/doc/html/rfc5646) and [RFC 4647](https://datatracker.ietf.org/doc/html/rfc4647).

Language tags are necessary to ensure proper localisation and internationalisation of web content and software. They allow applications and services to provide the correct language content to users and support the accurate display of language-specific data such as date formats, numbers, and text direction.

A BCP 47 language tag consists of a series of subtags separated by hyphens. These subtags define various aspects of the language and its variants. The most basic components are:

+ **Primary language subtag**: This is a mandatory subtag consisting of a two- or three-letter code that denotes the main language (e.g., `en` for English, `de` for German).

+ **Region subtag**: An optional subtag that specifies a country or region (e.g., `US` for the United States in `en-US`).

+ **Script subtag**: An optional subtag that indicates the writing system (e.g., `Latn` for the Latin alphabet in `zh-Latn`).

+ **Variant subtag**: An optional subtag that describes language variants or dialects (e.g., `1901` for traditional German orthography in `de-1901`).

+ **Extension subtag**: Extensions for specifying additional information.

+ **Private use subtag**: A range reserved for private use and not standardised.

#### Dates and Times

The formatting of dates and times in OpenCodeList, as specified by [JSON Schema](https://json-schema.org/understanding-json-schema/reference/string.html#dates-and-times), is defined by [RFC 3339, Section 5.6](https://tools.ietf.org/html/rfc3339#section-5.6).

Examples:

+ **`date-time`**: Date and time together, e.g., `2024-11-13T20:20:39` or `2024-11-13T20:20:39+00:00`.
+ **`time`**: Time only, e.g., `20:20:39` or `20:20:39+00:00`.
+ **`date`**: Date only, e.g., `2024-11-13`.

#### URIs

When a [Uniform Resource Identifier (URI)](https://tools.ietf.org/html/rfc3986) is required, it is important to distinguish between two JSON string formats depending on the context:

+ The `uri` format expects the JSON string to be an absolute URI. An absolute URI contains all the necessary information to identify the resource independently of its context. This means a `uri`:
    
    + begins with a scheme (like http, https, etc.),
    + may contain a hostname or an IP address,
    + and may optionally include a path, a query, and a fragment.

+ The `uri-reference` format is more flexible and allows both absolute and relative URIs. `uri-reference` can be a complete URI as described above, or it can be a relative reference that needs to be interpreted in a specific context. A relative URI might include only a path or even just a fragment.

Examples:

+ **`uri`**:

    + `https://example.com/path/to/resource?query=param#fragment`

+ **`uri-reference`**:

    + `https://example.com/path/to/resource?query=param#fragment`
    + `/path/to/resource`
    + `#fragment`
  
### Schema

#### OpenCodeList Document

An OpenCodeList document contains the following properties:

**`$opencodelist`**

:   A JSON string with the version number of the OpenCodeList specification. **This property is REQUIRED**.

**`$comments`**

:   A list of comments. It MUST be a JSON string array.

**`codeList`**

:   A [`codeList`](#codelist-object) object that includes the column definitions and the data content of a code list.

**`codeListSet`**

:   A [`codeListSet`](#codelistset-object) object that defines a collection of references to external OpenCodeList documents.

The properties `codeList` and `codeListSet` are mutually exclusive. **One of these properties is REQUIRED**.

If the `codeList` property is set:

+ If the `dataSet` sub-property is also set, it is a CodeList document.
+ Otherwise, it is a CodeList metadata document.

If the `codeListSet` property is set:

+ If the `referenceSet` sub-property is also set, it is a CodeListSet document.
+ Otherwise, it is a CodeListSet metadata document.

#### codeList Object

The `codeList` object defines a complete code list along with its data:

**`annotation`**

:   An [`annotation`](#annotation-object) object with accompanying notes of any kind.

**`identification`**

:   An [`identification`](#identification-object) object containing metadata about the code list. **This property is REQUIRED**.

**`columnSet`**

:   A [`columnSet`](#columnset-object) object defining the columns and unique keys of the code list. **This property is REQUIRED**.

**`dataSet`**

:   A [`dataSet`](#dataset-object) object containing the data rows of the code list.

#### codeListSet Object

The `codeListSet` object defines a collection of references to external OpenCodeList documents:

**`annotation`**

:   An [`annotation`](#annotation-object) object with accompanying notes of any kind.

**`identification`**

:   An [`identification`](#identification-object) object containing metadata about the reference list collection. **This property is REQUIRED**.

**`referenceSet`**

:   A list of references. It MUST be a JSON array of [`documentRef`](#documentref-object) objects. **This property is REQUIRED**.

#### annotation Object

The `annotation` object is a space for notes of any kind:

**`descriptions`**

:   A list of detailed notes. It MUST be a JSON array of [`markup`](#markup-object) objects.

**`appInfo`**

:   A JSON object containing machine-readable metadata. The content is freely defined but must comply with JSON syntax.

One of these properties is **REQUIRED**.

#### markup Object

The `markup` object is a text block formatted in a markup language (e.g., Markdown):

**`language`**

:   A JSON string specifying the language of the text block. This MUST be an [IETF BCP 47 language tag](https://www.rfc-editor.org/rfc/bcp/bcp47.txt).

**`format`**

:   The markup language of the text block. **This property is REQUIRED**. It MUST be a JSON string with one of the following values:

    + `text`
    + `markdown`
    + `html`

**`content`**

: A JSON string representing the actual content of the text block. **This property is REQUIRED**.

#### identification Object

The `identification` object contains metadata about an OpenCodeList document:

**`language`**

:   A JSON string specifying the language of this document. This MUST be an [IETF BCP 47 language tag](https://www.rfc-editor.org/rfc/bcp/bcp47.txt).

**`shortName`**

:   A JSON string with the short name of the document. **This property is REQUIRED**.

**`longName`**

:   A JSON string with the long name of the document.

**`description`**

:   A JSON string with a brief description of the document.

**`tags`**

:   A JSON string array containing tags or keywords that define the content of the document.

**`version`**

:   A JSON string with the version of the document.

**`changeLog`**

:   Documents the changes compared to previous versions of this document. It MUST be a JSON array of JSON strings.

**`publishedAt`**

:   A JSON string in `date-time` format specifying the publication date of this document.

**`publisher`**

:   An [`publisher`](#publisher-object) object containing information about the organisation responsible for publishing and/or maintaining the document.

**`validFrom`**

:   A JSON string in `date-time` format specifying the date from which this document is valid.

**`validTo`**

:   A JSON string in `date-time` format specifying the date until which this document is valid.

**`canonicalUri`**

:   A JSON string in `uri` format. This URI uniquely identifies all versions (collectively) of this document. **This property is REQUIRED**.

**`canonicalVersionUri`**

:   A JSON string in `uri` format. This URI uniquely identifies a specific version of this document. **This property is REQUIRED**.

**`locationUrls`**

:   A JSON array of JSON strings in `uri` format. These URIs are the suggested retrieval locations for this document in the OpenCodeList format.

**`alternateLanguageLocations`**

:   A JSON array of [`localizedUri`](#localizeduri-object) objects listing translated versions of this OpenCodeList document and their suggested locations.

**`alternateFormatLocations`**

:   A JSON array of [`mimeTypedUri`](#mimetypeduri-object) objects listing other formats (e.g., CSV) of this document and their suggested locations.

This object CAN be extended.

Example:

``` json
"identification": {
  "language": "en",
  "shortName": "GermanFederalStateCodes",
  "longName": "ISO 3166-2 Codes for Germany",
  "description": "ISO 3166-2 Codes for the federal states of Germany",
  "publishedAt": "2017-11-24T12:00:00",
  "publisher": {
    "shortName": "ISO",
    "longName": "International Organization for Standardization",
    "url": "https://www.iso.org/"
  },
  "version": "2017-11-23",
  "canonicalUri": "urn:iso:std:iso:3166-2:de",
  "canonicalVersionUri": "urn:iso:std:iso:3166-2:de:2017-11-23",
  "locationUrls": [
    "https://iso.example.com/germany.federal-state-codes-2017-11-23.json"
  ]
}
```

#### publisher Object

The `publisher` object defines the entity (organisation, institution, group, etc.) responsible for publishing and/or maintaining the document:

**`shortName`**

:   A JSON string with the short name of the publisher. **This property is REQUIRED**.

**`longName`**

:   A JSON string with the full name of the publisher.

**`identifier`**

:   An [`identifier`](#identifier-object) object with additional details (e.g., registration information) for identifying the publisher.

**`url`**

:   A JSON string in the `uri` format. This URI references additional external information (e.g., a website) about the publisher.

#### localizedUri Object

The `localizedUri` object defines a reference to a localised resource:

**`language`**

:   A JSON string with the language of the referenced resource. This MUST be an [IETF BCP 47 language tag](https://www.rfc-editor.org/rfc/bcp/bcp47.txt). **This property is REQUIRED**.

**`url`**

:   A JSON string in the `uri` format. This URI is the location for retrieving the referenced resource. **This property is REQUIRED**.

#### mimeTypedUri Object

The `mimeTypedUri` object defines a reference to a resource in a specified format:

**`mimeType`**

:   A JSON string with a standardised [MIME type (Multipurpose Internet Mail Extensions)](https://developer.mozilla.org/en-US/docs/Web/HTTP/MIME_types). **This property is REQUIRED**.

**`url`**

:   A JSON string in the `uri` format. This URI is the location for retrieving the referenced resource. **This property is REQUIRED**.

#### identifier Object

The `identifier` object represents a general identifier:

**`value`**

:   A JSON string with a key, code, or ID. **This property is REQUIRED**.

**`source`**

:   An [`identifierSource`](#identifiersource-object) object providing source details for the identifier.

#### identifierSource Object

The `identifierSource` object is a source reference for a general identifier:

**`shortName`**

:   A JSON string with the short name of the source. **This property is REQUIRED**.

**`longName`**

:   A JSON string with the full name of the source.

**`url`**

:   A JSON string in the `uri` format. This URI references additional external information (e.g., a website) about the source.

#### columnSet Object

The `columnSet` object defines the columns and unique keys of a code list:

**`columns`**

:   Defines the columns of the code list. It MUST be a JSON array of [`column`](#column-object) objects. **This property is REQUIRED**.

**`keys`**

:   Defines the unique keys of the code list. It MUST be a JSON array of [`key`](#key-object) objects. **This property is REQUIRED**.

**`defaultKey`**

:   Defines the default key when multiple keys are present, indicating the preferred key from `keys` to be used as the code source. It MUST be a [`defaultKey`](#defaultkey-object) object.

**`foreignKeys`**

:   Defines internal and external references to other code lists. It MUST be a JSON array of [`foreignKey`](#foreignkey-object) objects.

#### column Object

The `column` object defines a column for a code list:

**`id`**

: A JSON string with the ID of the column. **This field is REQUIRED**.

**`name`**

: A JSON string with the name of the column. **This field is REQUIRED**.

**`description`**

: A JSON string with a short description of the column.

**`type`**

:   Defines the data type of the column. **This field is REQUIRED**. It MUST be a JSON string with one of these values:

	+ `string`
	+ `enum`
	+ `enum-set`
	+ `integer`
	+ `number`
	+ `bool`
	+ `time`
	+ `date`
	+ `date-time`
	+ `object`

**`nullable`**

:   A JSON boolean that defines whether this column can also contain JSON NULLs.

**`optional`**

:   A JSON boolean that defines whether this column is optional, meaning it can be omitted from a data row.

Depending on the value in `type`, additional properties are available.

##### string

A JSON string. The following additional schema properties are available:

+ **`minLength`**: A JSON number in the `integer` format, specifying the minimum allowable number of characters.
+ **`maxLength`**: A JSON number in the `integer` format, specifying the maximum allowable number of characters.
+ **`pattern`**: A JSON string with a regular expression that must always match the values in this column. The regular expression syntax follows JavaScript ([ECMAScript 2024 specification](https://ecma-international.org/publications-and-standards/standards/ecma-262/)) and is used in [JSON Schema](https://json-schema.org/understanding-json-schema/reference/regular_expressions).
+ **`language`**: A JSON string indicating the language of the contents in this column. This MUST be an [IETF BCP 47 language tag](https://www.rfc-editor.org/rfc/bcp/bcp47.txt).

##### enum

A JSON string representing an enumeration. The following additional schema properties are available:

+ **`members`**: Defines the possible values of the enumeration. It MUST be a JSON array of `enumMember` objects. **This field is REQUIRED**.
+ **`language`**: A JSON string indicating the language of the contents in this column. This MUST be an [IETF BCP 47 language tag](https://www.rfc-editor.org/rfc/bcp/bcp47.txt).

##### enum-set

A JSON array representing an enumeration set. The following additional schema properties are available:

+ **`members`**: Defines the possible values of the enumeration. It MUST be a JSON array of `enumMember` objects. **This field is REQUIRED**.
+ **`language`**: A JSON string indicating the language of the contents in this column. This MUST be an [IETF BCP 47 language tag](https://www.rfc-editor.org/rfc/bcp/bcp47.txt).

##### integer

Represents a JSON number in the `integer` format. The following additional schema properties are available:

+ **`minValue`**: A JSON number in `integer` format with the minimum allowable value.
+ **`maxValue`**: A JSON number in `integer` format with the maximum allowable value.

##### number

Represents a JSON number in the `number` format. The following additional schema properties are available:

+ **`minValue`**: A JSON number in the `number` format specifying the minimum allowable value.
+ **`exclusiveMinValue`**: A JSON number in the `number` format specifying the exclusive lower limit.
+ **`maxValue`**: A JSON number in the `number` format specifying the maximum allowable value.
+ **`exclusiveMaxValue`**: A JSON number in the `number` format specifying the exclusive upper limit.

##### boolean

Represents a JSON value in the `boolean` format. No additional schema properties are available.

##### date

Represents a JSON string in the `date` format. The following additional schema properties are available:

+ **`minValue`**: A JSON string in `date` format specifying the minimum allowable value.
+ **`maxValue`**: A JSON string in `date` format specifying the maximum allowable value.

##### date-time

Represents a JSON string in the `date-time` format. The following additional schema properties are available:

+ **`minValue`**: A JSON string in the `date-time` format specifying the minimum allowable value.
+ **`maxValue`**: A JSON string in the `date-time` format specifying the maximum allowable value.

##### time

Represents a JSON string in the `time` format. The following additional schema properties are available:

+ **`minValue`**: A JSON string in the `time` format specifying the minimum allowable value.
+ **`maxValue`**: A JSON string in the `time` format specifying the maximum allowable value.

##### object

Represents a JSON object. The following additional schema properties are available:

+ **`schema`**: Either a JSON string in the `uri` format, defining the retrieval location of the JSON schema for this column, or a JSON object containing an embedded JSON schema.

#### enumMember Object

The `enumMember` object defines a value in an enumeration:

**`value`**

:   A JSON string with the enumeration value. **This property is REQUIRED**.

**`description`**

:   A JSON string with the description of the enumeration value.

#### key Object

The `key` object defines a unique key for the code list:

**`id`**

:   A JSON string with the unique ID of the key. **This property is REQUIRED**.

**`name`**

:   A JSON string with the name of the key.

**`description`**

:   A JSON string with a brief description of the key.

**`columnIds`**

:   A JSON string array with IDs, each referencing a [`column`](#column-object) object. **This property is REQUIRED**.

Example:

```json
"keys": [
  {
    "id": "codeKey",
    "name": "My primary key",
    "columnIds": [
      "code"
    ]
  }
]
```

#### defaultKey Object

The `defaultKey` object defines the default key for the code list:

**`keyId`**

: A JSON string with an ID that refers to a [`key`](#key-object) object in this code list. **This property is REQUIRED**.

Example:

```json
"defaultKey": {
  "keyId": "codeKey"
}
```

#### foreignKey Object

The `foreignKey` object defines a foreign key to an external code list:

**`id`**

:   A JSON string with the unique ID of the foreign key. **This property is REQUIRED**.

**`name`**

:   A JSON string with the name of the foreign key.

**`description`**

:   A JSON string with a short description of the foreign key.

**`columnIds`**

:   A JSON string array with IDs, each referencing a [`column`](#column-object) object. **This property is REQUIRED**.

**`keyRef`**

:   A `keyRef` object that refers to a key in an external code list. **This property is REQUIRED**.

Example:

``` json
"foreignKeys": [
  {
    "id": "foreignKey",
    "name": "My foreign key",
    "columnIds": [
	  "federalState"
    ],
    "keyRef": {
	  "codeListRef": {
	    "canonicalUri": "urn:iso:std:iso:3166-2:de",
	    "canonicalVersionUri": "urn:iso:std:iso:3166-2:de:2017-11-23",
  	    "locationUrls": [
		  "https://iso.example.com/germany.federal-state-codes-2017-11-23.json"
		]
	  },
	  "keyId": "codeKey"
    }
  }
]
```

#### keyRef Object

The `keyRef` object defines a reference to a key in an external code list:

**`codeListRef`**

:   A [`codeListRef`](#codelistref-object) object that refers to an external code list. **This property is REQUIRED**.

**`keyId`**

:   A JSON string with an ID that refers to a [`key`](#key-object) object in the external code list defined under `codeListRef`. **This property is REQUIRED**.

#### codeListRef Object

The `codeListRef` object defines a reference to an external CodeList document:

**`canonicalUri`**

:   A JSON string in `uri` format. This URI uniquely identifies all versions (collectively) of the referenced code list. **This property is REQUIRED**.

**`canonicalVersionUri`**

:   A JSON string in `uri` format. This URI uniquely identifies a specific version of the referenced code list. 

**`locationUrls`**

:   A JSON array of JSON string values in `uri` format. These URIs are the suggested retrieval locations for the referenced code list in the OpenCodeList format.

#### dataSet Object

The `dataSet` object contains the data of a code list:

**`rows`**

:   Defines the data rows of the code list. It MUST be a JSON array of [`row`](#row-object) objects. **This property is REQUIRED**.

#### row Object

The `row` object defines a data row in a code list. A `row` object is a JSON object where its properties are derived from the column definitions of the [`column`](#column-object) objects:

+ There MUST be at least as many properties as there are [`column`](#column-object) objects with the property `"optional": false`.

+ There MUST NOT be more properties than the total number of [`column`](#column-object) objects.

+ For each property:

    + The name corresponds to an ID referencing a [`column`](#column-object) object.

    + The value can be a JSON null, a JSON string, a JSON number, a JSON object, or a JSON array but must match the column type of the referenced [`column`](#column-object).

Example:

```json
"codeList": {
  "identification": { ... },
  "columnSet": {
    "columns": [
      {
        "id": "code",
        "name": "Code",
        "type": "string"
      },
      {
        "id": "name",
        "name": "Name",
        "type": "string"
      },
      {
        "id": "population",
        "name": "Population",
        "type": "integer"
      }
    ]
  },
  "dataSet": {
    "rows": [
      {
        "code": "BW",
        "name": "Baden-Württemberg",
        "population": 11280000
      },
      {
        "code": "BY",
        "name": "Bavaria",
        "population": 7450000
      }
    ]
  }
}
```

#### documentRef Object

The `documentRef` object defines a reference to an external OpenCodeList document:

**`type`**

:   Defines the document type of the reference. **This property is REQUIRED**. It MUST be a JSON string with one of the following values:

    + `codeListRef`: Reference to a CodeList document
    + `codeListSetRef`: Reference to a CodeListSet document

**`annotation`**

:   An [`annotation`](#annotation-object) object containing custom annotations of any kind.

**`canonicalUri`**

:   A JSON string in `uri` format. This URI uniquely identifies all versions (collectively) of the referenced document. **This property is REQUIRED**.

**`canonicalVersionUri`**

:   A JSON string in `uri` format. This URI uniquely identifies a specific version of the referenced document.

**`locationUrls`**

:   A JSON array of JSON string values in `uri` format. These URIs are the suggested retrieval locations for the referenced document in the OpenCodeList format.

### Extension of the Specification

The OpenCodeList specification can be extended with additional data at certain points.

The properties of these extensions are implemented as free fields, which must always be prefixed with `x-` (e.g., `x-external-id`). The value can be a string, a number, a boolean value, null, an object, or an array.

The extensions may or may not be supported by the available tools. Ideally, these tools can be extended to add the desired support (e.g., in Open Source projects).

Example:

```json
"identification": {
  "shortName": "GermanFederalStateCodes",
  "publisher": {
    "shortName": "ISO",
    "longName": "International Organization for Standardization",
    "x-contact-name": "ISO Central Secretariat",
    "x-contact-address": "Chemin de Blandonnet 8, 1214 Geneva, Switzerland",
    "x-contact-email": "central@iso.org"
  }
}
```