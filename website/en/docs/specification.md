# OpenCodeList Specification

#### Version 0.4.0

The key words "MUST", "MUST NOT", "SHOULD", "SHOULD NOT", "MAY", and "REQUIRED" in this document are to be interpreted as described in [RFC2119 and RFC8174](https://tools.ietf.org/html/bcp14), when, and only when, they appear in all capitals, as shown here.

This specification is licensed under the [Apache License, Version 2.0](https://opensource.org/license/apache-2-0/).

## Introduction

OpenCodeList defines a generic standard data format for representing code lists and key directories. Based on the [JSON standard](https://datatracker.ietf.org/doc/html/rfc8259), this format can easily be generated and read by almost any programming language. With the help of the [OpenCodeList Document Schema](https://github.com/openpotato/opencodelist/tree/main/schemas/v0.4/schema.json), documents in OpenCodeList format can be validated for syntactic correctness.

OpenCodeList can be used to exchange code lists between services or applications, as a representation format for official code lists, or as a response format for API requests (e.g. for RESTful web services).

### What are code lists?

Code lists and key directories are structured collections of codes or keys that are used to identify and classify data. These directories are essential in numerous areas such as databases, administrative systems, scientific research, and industrial applications. They are used to organize, store, and retrieve data consistently and efficiently.

Code lists play a central role in the standardization and harmonization of data. By using standardized codes, different systems and organizations can interpret and exchange data consistently. 

Examples of code lists include:

+ International codes for countries, languages, and currencies issued by the International Organization for Standardization (ISO).

+ National geographic codes (e.g. municipality codes of the Federal Republic of Germany)

+ National and subnational codes in the public sector (e.g. statistical codes issued by statistical authorities)

In principle, any selection of data can be mapped to a code list; even a simple Boolean value can be represented by the codes *No* and *Yes*. 

### How are code lists structured?

There is no natural law prescribing the structure of code lists. In most cases, however, a tabular representation can be agreed upon very quickly. 

The simplest form of such a tabular code list consists of two columns: *Key* and *Name*.

Here is an example of a country directory:

Key | Name
--- | ----
AT  | Austria
CH  | Switzerland
DE  | Germany

Code lists can potentially contain any number of columns. Here is an example of a country directory with three columns:

Key | Short name  | Long name
--- | ----------- | ---------
AT  | Austria     | Republic of Austria
CH  | Switzerland | Swiss Confederation
DE  | Germany     | Federal Republic of Germany

Code lists can reference one another. Here is an example of a country directory with a reference to a separate continent directory:

Key | Short name  | Long name                    | Continent
--- | ----------- | ---------------------------- | ---------
DE  | Germany     | Federal Republic of Germany  | EU 
MA  | Morocco     | Kingdom of Morocco           | AF
AU  | Australia   | Commonwealth of Australia    | OC

The corresponding continent directory could then look like this:

Key | Name
--- | ----
AF  | Africa
AM  | Americas
AS  | Asia
EU  | Europe
OC  | Oceania

Code lists can also have more than one key. Here is an example of a country directory with different ISO3166 codes:

Alpha2Code | Alpha3Code | NumericCode | Name
---------- | ---------- | ----------- | ----
AT         | AUT        | 040         | Austria
CH         | CHE        | 756         | Switzerland
DE         | DEU        | 276         | Germany

Let us go one step further with this multilingual country directory. Here it is the combination of key and language (also represented by a language key) that establishes uniqueness: 

Key | Language | Name
--- | -------- | ----
AT  | de       | Österreich
AT  | en       | Austria
CH  | de       | Schweiz
CH  | en       | Switzerland
DE  | de       | Deutschland
DE  | en       | Germany

In addition to text and numbers, code lists can also define complex column types that are filled, for example, with XML, JSON, or HTML.

### What makes code lists so complex?

#### Format and semantics

When thinking about code lists, it is easy to picture an Excel table with a few columns and rows. But what do the columns actually mean? Is the code always in the first column? Is the content of a column always to be interpreted as plain text? And why Excel in the first place? After all, it is a fairly complex data format that is not particularly well suited for automated processing of code lists. CSV would definitely be easier to handle, but unfortunately it does not answer the first three questions either.

#### Versioning

Code lists can change over time. In these cases, there is both a current version of a code list and older versions of that code list. As part of these changes, both the number of codes and the meaning of individual codes can change.

#### Dependencies

Code lists can have dependencies on one another, i.e. values from one code list can refer to codes in another code list. These references must also take into account possible versioning of the respective other code list.

#### Multilingualism

When code lists are used across language boundaries, they often need to be adapted for multiple languages. The same code may then, for example, have different labels in different languages.

#### User-defined code lists

When we talk about code lists, this does not always mean standardized codes defined by a committee, organization, or institution. It often also refers to codes that a small user group wants to use for a specific domain or even a specific application. These must be able to coexist with established standard codes without conflicts.

### Aren't there already standards for code lists?

Yes, there are: the [Organization for the Advancement of Structured Information Standards (OASIS)](https://www.oasis-open.org) has defined an XML-based standard for code lists called [Code List Representation (genericode)](https://docs.oasis-open.org/codelist/genericode/v1.0/genericode-v1.0.html).

> The OASIS Code List Representation format, “genericode”, is a single model and XML format (with a W3C XML Schema) that can encode a broad range of code list information. The XML format is designed to support interchange or distribution of machine-readable code list information between systems.  Note that genericode is not designed as a run-time format for accessing code list information, and is not optimized for such usage.  Rather, it is designed as an interchange format that can be transformed into formats suitable for run-time usage, or loaded into systems that perform run-time processing using code list information.

Sounds good, but there is a catch. There is no standardized JSON representation of "genericode". 

> Recognizing the custom use of JSON in a tight binding between user-defined processes, the committee sees no purpose served by standardizing a JSON syntax for the genericode vocabulary.

This means there is no official support for JSON as a data format and therefore no official JSON Schema either.

Anyone who wants to represent code lists in XML format is therefore well advised to use the OASIS standard. However, anyone who wants to work with JSON has a suitable alternative in OpenCodeList. Naturally, OpenCodeList is heavily influenced by the *OASIS Code List Representation Format* and aims to remain semantically compatible with it as far as possible.

### Why JSON? XML is good!

Of course, XML is an excellent, standardized format that leaves little to be desired. The reason for a JSON representation of code lists, however, lies in the explicit desire to use JSON:

+ In the world of cloud-based services, JSON has largely established itself as the payload for RESTful APIs. Of course, such an API can also return XML as its payload, but doing so introduces an additional dependency, both on the server side (generating XML) and on the client side (consuming XML). Instead of working with one format (JSON), an additional format (XML) must be handled when code lists are used. This increases the effort involved.

+ JSON is more compact because its syntax is less verbose. With large code lists, this has a positive effect on performance. In general, JSON is faster to parse than XML because it carries less syntactic overhead and JSON parsers in most modern programming languages are now highly optimized.

## Definitions

### OpenCodeList document

An OpenCodeList document is a self-contained resource that defines and describes either a code list or a code list set. It MUST contain the property `$opencodelist` and exactly one of the two properties `codeList` or `codeListSet`. An OpenCodeList document uses the OpenCodeList specification and conforms to it.

### Code lists

A code list is a classic relational table with columns and data rows, where at least one column should serve as a key (code). OpenCodeList allows generic code lists to be defined.

An OpenCodeList document with the `codeList` property set is also called a **CodeList document**. 

An OpenCodeList document with the `codeList` property set but without data (i.e. without the `dataSet` subproperty) is also called a **CodeList meta document**, because it contains only metadata.

### Code list sets

A code list set is a list of references to external code lists or external code list sets. A code list set can represent the following:

+ Combining different versions of a code list in a collection.

+ Creating a hierarchy of code list sets.

+ Creating an index of all usable code lists.

An OpenCodeList document with the `codeListSet` property set is also called a **CodeListSet document**. 

An OpenCodeList document with the `codeListSet` property set but without references (i.e. without the `referenceSet` subproperty) is also called a **CodeListSet meta document**, because it contains only metadata.

## Specification

### Versioning

The OpenCodeList specification is versioned according to the `major.minor.patch` scheme. The major-minor part of the version number (e.g. `0.4`) MUST identify the feature set of the specification. Patch versions address errors in this document or provide clarifications to this document, but do not change the feature set. Tools that support OpenCodeList version `0.4` MUST be compatible with all `0.4.*` versions of OpenCodeList. The patch version SHOULD NOT be taken into account by tools, so that, for example, no distinction is made between `0.4.0` and `0.4.1`.

An OpenCodeList document always contains a mandatory `$opencodelist` property specifying the version of the OpenCodeList specification used.

### Format

An OpenCodeList document conforming to the OpenCodeList specification is itself a JSON object that can be represented in JSON format.

All property names in the specification are case-sensitive. The schema defines two types of properties: explicitly defined properties with declared names, and free-form properties whose names MUST match a specific pattern. Additional properties MUST have unique names within the containing JSON object.

#### JSON Schema

[JSON Schema](https://json-schema.org/) is a specification for defining JSON data structures. A JSON Schema is itself expressed declaratively using [JSON](https://www.json.org/). The [OpenCodeList Document Schema](https://github.com/openpotato/opencodelist/tree/main/schemas/v0.4/schema.json) is a JSON Schema for OpenCodeList documents.

#### Multilingualism

OpenCodeList supports multilingual content at several levels. Languages are identified exclusively by [IETF BCP 47 language tags](https://datatracker.ietf.org/doc/html/rfc5646).

The `identification.language` property defines the default language for the contents of an OpenCodeList document. This language applies unless a different language is specified for particular content.

Columns of type `string`, `enum`, or `enum-set` can specify their own language using the `language` property. For the values of that column, this language specification overrides the default language of the document.

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
]
```

The `name` and `description` properties of columns, keys, and foreign keys, as well as the `description` property of enumeration values, can be specified either as a simple JSON string or as a JSON object containing language-dependent values. In such a localized object, every property name MUST be a valid BCP 47 language tag and every corresponding property value MUST be a JSON string.

Example:

```json
{
  "id": "col-1",
  "type": "string",
  "name": {
    "de": "Beschreibung",
    "en": "Description",
    "fr": "Description"
  },
  "description": {
    "de": "Spalte mit einer lokalisierten Beschreibung.",
    "en": "Column with a localized description.",
    "fr": "Colonne avec une description localisée."
  }
}
```

Values of a column of type `string` MAY also be specified as localized values. In this case, the column value is represented as a JSON object whose property names are BCP 47 language tags and whose property values are the corresponding translations.

Example of a non-localized column value:

```json
{
  "code": "DE",
  "name": "Germany"
}
```

Example of a localized column value:

```json
{
  "code": "DE",
  "name": {
    "de": "Deutschland",
    "en": "Germany",
    "fr": "Allemagne"
  }
}
```

For a localized string value, the BCP 47 language tags within the value directly determine the language of the individual texts. Any default language specified for the column or the document therefore does not apply to these individual localized values.

Alternatively, language-specific versions of an entire OpenCodeList document can be published separately. For this purpose, `identification.alternateLanguageLocations` can reference corresponding documents in other languages.

#### Language tags

[IETF BCP 47 (Best Current Practice 47)](https://www.rfc-editor.org/info/bcp47) defines the rules for identifying natural languages and language variants. BCP 47 is maintained by the Internet Engineering Task Force (IETF) and is based in particular on [RFC 5646](https://datatracker.ietf.org/doc/html/rfc5646), which defines the structure and use of language tags, and [RFC 4647](https://datatracker.ietf.org/doc/html/rfc4647), which describes procedures for comparing and selecting language tags.

BCP 47 language tags allow applications to identify the language of content unambiguously. In addition to the actual language, they can also specify, for example, the writing system used, a region, or a particular language variant.

A BCP 47 language tag consists of one or more subtags separated by hyphens. A simple language tag contains only the language:

```text
de
en
fr
```

Additional subtags can specify further information:

* **Primary language subtag**: Identifies the language, for example `de` for German, `en` for English, or `fr` for French. In common usage, this subtag consists of two or three letters.

* **Script subtag**: Optionally identifies the writing system used, for example `Latn` for the Latin script or `Cyrl` for the Cyrillic script. Example: `sr-Latn`.

* **Region subtag**: Optionally identifies a country or region, for example `DE` for Germany or `US` for the United States. Examples: `de-DE` and `en-US`.

* **Variant subtag**: Optionally identifies a specific variant of a language, for example `1901` for traditional German orthography. Example: `de-1901`.

* **Extension subtag**: Allows additional information defined by an extension to be specified.

* **Private-use subtag**: Allows application-specific language identifiers. The private-use area begins with the singleton `x`, for example `de-x-example`.

Subtags can be combined. For example,

```text
zh-Hans-CN
```

denotes Chinese (`zh`) written in Simplified Chinese script (`Hans`) with a regional reference to China (`CN`).

#### Date and time values

OpenCodeList distinguishes between date values, date/time values, and local time values.

The `date` and `date-time` formats correspond to the formats of the same names in [JSON Schema](https://json-schema.org/understanding-json-schema/reference/string.html#dates-and-times) and are based on [RFC 3339, Section 5.6](https://www.rfc-editor.org/rfc/rfc3339#section-5.6).

For the `time` type, OpenCodeList instead uses a local time without a date and without a UTC offset. It is represented in the format `HH:mm:ss` with optional fractional seconds of up to seven digits.

Examples:

+ **`date-time`**: Date and time with UTC offset, e.g. `2024-11-13T20:20:39Z` or `2024-11-13T20:20:39+01:00`.
+ **`date`**: Date without time, e.g. `2024-11-13`.
+ **`time`**: Local time without date and UTC offset, e.g. `20:20:39` or `20:20:39.1234567`.

#### URIs

A [URI (Uniform Resource Identifier)](https://datatracker.ietf.org/doc/html/rfc3986) is a string used to identify a resource on the Internet. It is a broad term that includes both URLs and URNs. There are two main types of URIs:

+ *URL (Uniform Resource Locator)*: Specifies the location of a resource.
+ *URN (Uniform Resource Name)*: Specifies the name of a resource without implying its location.

A **URL (Uniform Resource Locator)** is a type of URI that describes how a resource can be found on a network. It contains information such as the protocol to use (e.g. HTTPS), the host name (e.g. [www.example.com](http://www.example.com)), and sometimes a path or query string identifying the specific resource.

Example of a URL:

```
https://www.example.com/books/the-adventures-of-tom-sawyer.pdf
```

Here:

+ `https` is the protocol.
+ `www.example.com` is the host name.
+ `/books/the-adventures-of-tom-sawyer.pdf` is the path to the specific resource.

A **URN (Uniform Resource Name)** is a type of URI that provides a unique and persistent identifier for a resource without describing its location or method of access. URNs are intended to serve as persistent, location-independent resource identifiers.

Example of a URN:

```
urn:isbn:978-3-96111-268-5
```

Here:

+ `urn` indicates that this is a URN.
+ `isbn` is the namespace identifier.
+ `978-3-96111-268-5` is the specific resource name within the `isbn` namespace.

### Schema

#### OpenCodeList document

An OpenCodeList document contains the following properties:

**`$opencodelist`**

:   A JSON string containing the version number of the OpenCodeList specification. **This property is REQUIRED**.

**`$comments`**

:   A list of comments. It MUST be a JSON string array.

**`codeList`**

:   A [`codeList`](#codelist-object) object containing the column definitions and data contents of a code list. 

**`codeListSet`**

:   A [`codeListSet`](#codelistset-object) object defining a collection of references to external OpenCodeList documents. 

The `codeList` and `codeListSet` properties are mutually exclusive. **One of these properties is REQUIRED**. 

If the `codeList` property is set:

+ If the `dataSet` subproperty is also set, the document is a CodeList document. 

+ Otherwise, it is a CodeList meta document.

If the `codeListSet` property is set:

+ If the `referenceSet` subproperty is also set, the document is a CodeListSet document. 

+ Otherwise, it is a CodeListSet meta document.

#### codeList object

The `codeList` object defines a complete code list including its data:

**`annotation`**

:   An [`annotation`](#annotation-object) object containing accompanying annotations of any kind.

**`identification`**

:   An [`identification`](#identification-object) object containing metadata about the code list. **This property is REQUIRED**.

**`columnSet`**

:   A [`columnSet`](#columnset-object) object defining the columns and unique keys of the code list. **This property is REQUIRED**.

**`dataSet`**

:   A [`dataSet`](#dataset-object) object containing the data rows of the code list. 

#### codeListSet object

The `codeListSet` object defines a collection of references to external OpenCodeList documents:

**`annotation`**

:   An [`annotation`](#annotation-object) object containing accompanying annotations of any kind.

**`identification`**

:   An [`identification`](#identification-object) object containing metadata about the reference list collection. **This property is REQUIRED**.

**`referenceSet`**

:   A list of references. It MUST be a JSON array of [`documentRef`](#documentref-object) objects.

#### annotation object

The `annotation` object provides a place for annotations of any kind:

**`descriptions`**

:   A list of written annotations. It MUST be a JSON array of [`markup`](#markup-object) objects. 

**`appInfo`**

:   A JSON object containing machine-processable metadata. Its content is unrestricted and only needs to conform to JSON syntax.

At least one of the two properties is **REQUIRED**. Both properties MAY be specified together.

#### markup object

The `markup` object is a text block formatted in a markup language (e.g. Markdown):

**`language`**

:   A JSON string containing the language of the text block. This MUST be an [IETF BCP 47 language tag](https://datatracker.ietf.org/doc/html/rfc5646).

**`format`**

:   The markup language of the text block. **This property is REQUIRED**. It MUST be a JSON string with one of the following values:

    + `text`
    + `markdown`
    + `html`
    + `xml`

**`content`**

:   A JSON string representing the actual content of the text block. **This property is REQUIRED**.

#### identification object

The `identification` object contains metadata about an OpenCodeList document:

**`language`**

:   A JSON string containing the default language for the contents of this document. This MUST be an [IETF BCP 47 language tag](https://datatracker.ietf.org/doc/html/rfc5646). The default language applies unless a column or localized value specifies its own language.

**`shortName`**

:   A JSON string containing the short name of the document. **This property is REQUIRED**.

**`longName`**

:   A JSON string containing the long name of the document. 

**`description`**

:   A JSON string containing a short description of the document. 

**`tags`**

:   A JSON string array containing tags or keywords describing the content of the document.

**`version`**

:   A JSON string containing the version of the document.

**`changeLog`**

:   Allows changes compared with previous versions of this document to be documented. It MUST be a JSON array of JSON strings.

**`publishedAt`**

:   A JSON string in `date-time` format containing the time at which this document was published.

**`publisher`**

:   A [`publisher`](#publisher-object) object containing information about the entity responsible for publishing and/or maintaining the document.

**`validFrom`**

:   A JSON string in `date-time` format defining the point in time from which this document is valid.

**`validTo`**

:   A JSON string in `date-time` format defining the point in time until which this document remains valid.

**`canonicalUri`**

:   A JSON string in `uri` format. This URI uniquely identifies all versions of this document collectively. **This property is REQUIRED**.

**`canonicalVersionUri`**

:   A JSON string in `uri` format. This URI identifies a specific version of this document. **This property is REQUIRED**.

**`locationUrls`**

:   A JSON array containing JSON string values in `uri` format. These URIs are suggested retrieval locations for this document in OpenCodeList format.

**`alternateLanguageLocations`**

:   A JSON array of [`localizedUri`](#localizeduri-object) objects listing translated versions of this OpenCodeList document and their suggested storage locations.

**`alternateFormatLocations`**

:   A JSON array of [`mimeTypedUri`](#mimetypeduri-object) objects listing formats other than OpenCodeList (e.g. CSV) and their suggested storage locations.

This object MAY be extended.

Example:


``` json
"identification": {
  "language": "en",
  "shortName": "GermanFederalStateCodes",
  "longName": "ISO 3166-2 Codes for Germany",
  "description": "ISO 3166-2 Codes for the federal states of Germany",
  "publishedAt": "2017-11-24T12:00:00Z",
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

#### publisher object

The `publisher` object defines the publisher (authority, institution, group of persons, etc.) responsible for publishing and/or maintaining the document:

**`shortName`**

:   A JSON string containing the short name of the publisher. **This property is REQUIRED**.

**`longName`**

:   A JSON string containing the long name of the publisher. 

**`identifier`**

:   An [`identifier`](#identifier-object) object containing additional information (e.g. a registry entry) identifying the publisher.

**`url`**

:   A JSON string in `uri` format. This URI is a reference to additional external information (e.g. a website) about the publisher.

#### localizedUri object

The `localizedUri` object defines a reference to a localized resource:

**`language`**

:   A JSON string containing the language of the referenced resource. This MUST be an [IETF BCP 47 language tag](https://datatracker.ietf.org/doc/html/rfc5646). **This property is REQUIRED**.

**`url`**

:   A JSON string in `uri` format. This URI is the retrieval location of the referenced resource. **This property is REQUIRED**.

#### mimeTypedUri object

The `mimeTypedUri` object defines a reference to a resource in a specified format:

**`mimeType`**

:   A JSON string containing a standardized [MIME type (Multipurpose Internet Mail Extensions)](https://developer.mozilla.org/en-US/docs/Web/HTTP/MIME_types). **This property is REQUIRED**.

**`url`**

:   A JSON string in `uri` format. This URI is the retrieval location of the referenced resource. **This property is REQUIRED**.

#### identifier object

The `identifier` object represents a general identifier:

**`value`**

:   A JSON string containing a key, code, or ID. **This property is REQUIRED**.

**`source`**

:   An [`identifierSource`](#identifiersource-object) object containing source information for an identifier.

#### identifierSource object

The `identifierSource` object provides source information for a general identifier:

**`shortName`**

:   A JSON string containing the short name of the source. **This property is REQUIRED**.

**`longName`**

:   A JSON string containing the long name of the source. 

**`url`**

:   A JSON string in `uri` format. This URI is a reference to additional external information (e.g. a website) about the source.

#### columnSet object

The `columnSet` object defines columns and unique keys of a code list:

**`columns`**

:   Defines the columns of the code list. It MUST be a JSON array of [`column`](#column-object) objects. **This property is REQUIRED**.

**`keys`**

:   Defines the unique keys of the code list. It MUST be a JSON array of [`key`](#key-object) objects. **This property is REQUIRED**.

**`defaultKey`**

:   If multiple keys exist, defines the default key, i.e. the key from `keys` that should preferably be used as the source of codes. It MUST be a [`defaultKey`](#defaultkey-object) object.

**`foreignKeys`**

:   Defines foreign keys referencing keys in external code lists. It MUST be a JSON array of [`foreignKey`](#foreignkey-object) objects.

#### column object

The `column` object defines a column for a code list:

**`id`**

:   A JSON string containing the ID of the column. **This property is REQUIRED**.

**`name`**

:   The name of the column. Either a JSON string or an object containing localized names. For a localized object, every property name MUST be a valid [IETF BCP 47 language tag](https://datatracker.ietf.org/doc/html/rfc5646) and the corresponding property value MUST be a JSON string. **This property is REQUIRED**.

**`description`**

:   A short description of the column. Either a JSON string or an object containing localized descriptions. For a localized object, every property name MUST be a valid [IETF BCP 47 language tag](https://datatracker.ietf.org/doc/html/rfc5646) and the corresponding property value MUST be a JSON string.

**`type`**

:   Defines the data type of the column. **This property is REQUIRED**. It MUST be a JSON string with one of the following values:

    + `string`
    + `enum`
    + `enum-set`
    + `integer`
    + `number`
    + `boolean`
    + `time`
    + `date`
    + `date-time`
    + `document`

**`nullable`**

:   A JSON Boolean value specifying whether this column may also contain a JSON null value. If `nullable` is not specified, it defaults to `false`.

**`optional`**

:   A JSON Boolean value specifying whether this column may be omitted from a data row. If `optional` is not specified, it defaults to `false`.

Depending on the value of `type`, additional properties are available.

##### string

Text. The text may be either a JSON string or a JSON object containing localized string values. The following additional schema properties are available:

+ **`minLength`** : A JSON number in `integer` format specifying the minimum allowed number of characters
+ **`maxLength`** : A JSON number in `integer` format specifying the maximum allowed number of characters
+ **`pattern`** : A JSON string containing a regular expression that must always match the values in this column. The regular expression syntax used corresponds to the syntax from JavaScript ([ECMAScript 2024 language specification](https://ecma-international.org/publications-and-standards/standards/ecma-262/)), as described and used in [JSON Schema](https://json-schema.org/understanding-json-schema/reference/regular_expressions).
+ **`language`** : A JSON string containing the language of the contents of this column. This MUST be an [IETF BCP 47 language tag](https://datatracker.ietf.org/doc/html/rfc5646). 

##### enum

Represents a JSON string containing an enumeration value. The following additional schema properties are available:

+ **`members`** : Defines the possible enumeration values. It MUST be a JSON array of [`enumMember`](#enummember-object) objects. **This property is REQUIRED**.
+ **`language`** : A JSON string containing the language of the contents of this column. This MUST be an [IETF BCP 47 language tag](https://datatracker.ietf.org/doc/html/rfc5646).

##### enum-set

Represents a JSON array containing a set of enumeration values. The following additional schema properties are available:

+ **`members`** : Defines the possible values of the enumeration set. It MUST be a JSON array of [`enumMember`](#enummember-object) objects. **This property is REQUIRED**.
+ **`language`** : A JSON string containing the language of the contents of this column. This MUST be an [IETF BCP 47 language tag](https://datatracker.ietf.org/doc/html/rfc5646).

##### integer

Represents a JSON number in `integer` format. The following additional schema properties are available:

+ **`minValue`** : A JSON number in `integer` format containing the minimum allowed value.
+ **`maxValue`** : A JSON number in `integer` format containing the maximum allowed value.

##### number

Represents a JSON number in `number` format. The following additional schema properties are available:

+ **`minValue`** : A JSON number in `number` format containing the minimum allowed value.
+ **`exclusiveMinValue`** : A JSON number in `number` format defining the exclusive lower bound.
+ **`maxValue`** : A JSON number in `number` format containing the maximum allowed value.
+ **`exclusiveMaxValue`** : A JSON number in `number` format defining the exclusive upper bound.

##### boolean

Represents a JSON Boolean. No additional schema properties are available.

##### date

Represents a JSON string in `date` format. The following additional schema properties are available:

+ **`minValue`** : A JSON string in `date` format containing the minimum allowed value.
+ **`maxValue`** : A JSON string in `date` format containing the maximum allowed value.

##### date-time

Represents a JSON string in `date-time` format. The following additional schema properties are available:

+ **`minValue`** : A JSON string in `date-time` format containing the minimum allowed value.
+ **`maxValue`** : A JSON string in `date-time` format containing the maximum allowed value.

##### time

Represents a local time without a date and UTC offset in the OpenCodeList format `HH:mm:ss[.fffffff]`. The following additional schema properties are available:

+ **`minValue`** : A JSON string containing the minimum allowed local time.
+ **`maxValue`** : A JSON string containing the maximum allowed local time.

##### document

Represents an embedded JSON object or JSON array. The following additional schema properties are available:

+ **`schemaUri`** : A JSON string in `uri` format referencing a JSON Schema used to validate the embedded data.

#### enumMember object

The `enumMember` object defines a value in an enumeration:

**`value`**

:   A JSON string containing the enumeration value. **This property is REQUIRED**.

**`description`**

:   The description of the enumeration value. Either a JSON string or an object containing localized descriptions. For a localized object, every property name MUST be a valid [IETF BCP 47 language tag](https://datatracker.ietf.org/doc/html/rfc5646) and the corresponding property value MUST be a JSON string.

#### key object

The `key` object defines a unique key for the code list:

**`id`**

:   A JSON string containing the unique ID of the key. **This property is REQUIRED**.

**`name`**

:   The name of the key. Either a JSON string or an object containing localized names. For a localized object, every property name MUST be a valid [IETF BCP 47 language tag](https://datatracker.ietf.org/doc/html/rfc5646) and the corresponding property value MUST be a JSON string.

**`description`**

:   The description of the key. Either a JSON string or an object containing localized descriptions. For a localized object, every property name MUST be a valid [IETF BCP 47 language tag](https://datatracker.ietf.org/doc/html/rfc5646) and the corresponding property value MUST be a JSON string. 

**`columnIds`**

:   A JSON string array containing IDs, each referencing a [`column`](#column-object) object. **This property is REQUIRED**.

    A column that is part of a key MUST have `optional: false` and `nullable: false`. The combination of the values of the columns referenced by `columnIds` MUST be unique within the `dataSet` for every data row.

Example:	

``` json
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

#### defaultKey object

The `defaultKey` object defines the default key for the code list:

**`keyId`**

:   A JSON string containing an ID that references a [`key`](#key-object) object in this code list. **This property is REQUIRED**.

Example:	

``` json
"defaultKey": {
  "keyId": "codeKey"
}
```

#### foreignKey object

The `foreignKey` object defines a foreign key to an external code list:

**`id`**

:   A JSON string containing the unique ID of the foreign key. **This property is REQUIRED**.

**`name`**

:   The name of the foreign key. Either a JSON string or an object containing localized names. For a localized object, every property name MUST be a valid [IETF BCP 47 language tag](https://datatracker.ietf.org/doc/html/rfc5646) and the corresponding property value MUST be a JSON string.

**`description`**

:   The description of the foreign key. Either a JSON string or an object containing localized descriptions. For a localized object, every property name MUST be a valid [IETF BCP 47 language tag](https://datatracker.ietf.org/doc/html/rfc5646) and the corresponding property value MUST be a JSON string. 

**`columnIds`**

:   A JSON string array containing IDs, each referencing a [`column`](#column-object) object. **This property is REQUIRED**.

    The number of columns referenced by `columnIds` MUST match the number of columns of the referenced key. The data types of the corresponding columns MUST match. Columns are matched according to their order.

**`keyRef`**

:   A `keyRef` object referencing a key in an external code list. **This property is REQUIRED**.

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

#### keyRef object

The `keyRef` object defines a reference to a key in an external code list:

**`codeListRef`**

:   A [`codeListRef`](#codelistref-object) object referencing an external code list. **This property is REQUIRED**.

**`keyId`**

:   A JSON string containing an ID that references a [`key`](#key-object) object in the external code list defined under `codeListRef`. **This property is REQUIRED**.

#### codeListRef object

The `codeListRef` object defines a reference to an external CodeList document:

**`canonicalUri`**

:   A JSON string in `uri` format. This URI uniquely identifies all versions of the referenced code list collectively. **This property is REQUIRED**.

**`canonicalVersionUri`**

:   A JSON string in `uri` format. This URI identifies a specific version of the referenced code list. 

**`locationUrls`**

:   A JSON array containing JSON string values in `uri` format. These URIs are suggested retrieval locations for the referenced code list in OpenCodeList format.

#### dataSet object

The `dataSet` object contains the data of a code list:

**`rows`**

:   Defines the data rows of the code list. It MUST be a JSON array of [`row`](#row-object) objects. **This property is REQUIRED**.

#### row object
The `row` object defines a data row in a code list. It is a JSON object whose properties correspond to the columns defined in the [`column`](#column-object) objects.

+ For every `column` object with `optional: false`, the `row` object MUST contain a property whose name corresponds to the `id` of that column.

+ For every `column` object with `optional: true`, the corresponding property MAY be omitted from the `row` object.

+ The `row` object MUST NOT contain a property whose name does not correspond to the `id` of a defined [`column`](#column-object) object.

+ At most one property MAY be assigned to each column within a `row` object. Since property names within a JSON object must be unique, each column can occur at most once per data row.

+ The value of a property MUST conform to the `type` and all additional constraints of the corresponding [`column`](#column-object) object.

+ Depending on the column type, a value MAY be a JSON null value, JSON number, JSON Boolean, JSON string, JSON object, or JSON array.

+ A JSON null value MAY only be used if the corresponding column permits null values.

+ A JSON object MAY either represent the value of a column of type `document` or a localized value of a column of type `string`. For a localized string, every property name MUST be a valid IETF BCP 47 language tag and every corresponding property value MUST be a JSON string.

+ A JSON array MAY only be used for columns of type `enum-set` or `document`. For a column of type `enum-set`, every element MUST correspond to an enumeration value defined in `members`.

Example:	

``` json
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

#### documentRef object

The `documentRef` object defines a reference to an external OpenCodeList document:

**`type`**

:   Defines the document type of the reference. **This property is REQUIRED**. It MUST be a JSON string with one of the following values:

+ `codeListRef`: Reference to a CodeList document
+ `codeListSetRef`: Reference to a CodeListSet document

**`annotation`**

:   An [`annotation`](#annotation-object) object containing user-defined annotations of any kind.

**`canonicalUri`**

:   A JSON string in `uri` format. This URI uniquely identifies all versions of the referenced document collectively. **This property is REQUIRED**.

**`canonicalVersionUri`**

:   A JSON string in `uri` format. This URI identifies a specific version of the referenced document.

**`locationUrls`**

:   A JSON array containing JSON string values in `uri` format. These URIs are suggested retrieval locations for the referenced document in OpenCodeList format.

### Extending the specification

The OpenCodeList specification can be extended with additional data for the [`identification`](#identification-object) object and the [`publisher`](#publisher-object) object.

Extension properties are implemented as free-form properties that MUST always be prefixed with `x-` (e.g. `x-external-id`). The value may be a string, number, Boolean value, null, object, or array.

The extensions may or may not be supported by the available tools. Ideally, these tools can be extended to add the desired support (e.g. in open source projects).

Example:

``` json
"identification": {
  "shortName": "GermanFederalStateCodes",
  "publisher": {
	"shortName": "ISO",
	"longName": "International Organization for Standardization",
    "x-contact-name": "ISO Central Secretariat",
    "x-contact-address": "Chemin de Blandonnet 8, 1214 Geneva, Switzerland",
    "x-contact-email": "central@iso.org "
  }
}
```
