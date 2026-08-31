# Change Log

## OpenCodeList specification

### 0.4.0 <small>_ August 31, 2026</small>

**Added:**

+ Better support for localized text:
    
    + Names and descriptions of columns, keys, and foreign keys, as well as descriptions of enumeration values, can now be specified not only as simple JSON strings but also as language-dependent values using BCP 47 language tags.
    + Values of columns of type `string` can also be specified as localized values using BCP 47 language tags.

+ For `identification.language`, the semantics of a default language for the document content have been defined. Columns of type `string`, `enum`, and `enum-set` can override this default using their `language` property.

**Changed:**

+ Breaking Change: For columns of type `document`, the `schema` property has been replaced by `schemaUri`. Embedded JSON Schemas as column definitions are no longer supported.
+ Breaking Change: Values of the `time` column type now explicitly represent a local time of day without a date or UTC offset, using the format `HH:mm:ss` with optional fractional seconds of up to seven digits.
+ Breaking Change: Columns that are part of a key MUST have `optional: false` and `nullable: false`. The combination of key values MUST be unique within the dataset.
+ The rules for foreign keys have been clarified. The number and data types of the local foreign-key columns MUST match the columns of the referenced key; columns are matched by their order.
+ The rules for data rows have been clarified. Non-optional columns MUST be present, optional columns MAY be omitted, and values MUST conform to the respective column type and its constraints.
+ The `annotation` object has been clarified to explicitly allow `descriptions` and `appInfo` to be specified together.
+ The permitted extension points for custom `x-` properties have been clarified to include `identification` and `publisher`.

**Removed:**

+ The `referenceSet` property of a `codeListSet` object is no longer required. This allows a code list collection to be published as a pure CodeListSet metadata document without references.

In addition, numerous minor (and major) issues on the website have been fixed.

### 0.3.0 <small>_ February 19, 2025</small>

**Changed:**

+ Breaking change: The `canonicalVersionUri` property was marked as required for the `identification` sub-schema.	
+ Breaking change: For the sub-schemas `documentRef` and `keyRef`, the required property `canonicalVersionUri` has been changed to `canonicalUri`.

### 0.2.0 <small>_ January 25, 2025</small>

+ First publication