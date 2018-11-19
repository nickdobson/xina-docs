---
id: api-syntax-data
title: Data Syntax 
---

## Records

Provides the data for one or more records.

### Array

Provides record data as an array of record objects.

 [ <[[#Record|record]]> ... ]

### DSV

Provides record data as a delimiter separated values file. See the [[XINA API :: DSV Format|DSV format]] page for the
syntax and more information.

## Record

Provides data for a single record as a JSON object.

| Property            | Value                               |
|---------------------|-------------------------------------|
| field name or label |                                     |
| blob name or label  |                                     |
| `tags`              |                                     |
| `file`              |                                     |

 {
  "<field>" : <[[#Fields|fields]]>,
  "<blob>"  : <[[#Blobs|blobs]]>,    (optional)
  "tags"    : [<tag> ...] (optional)
  "file"    : <object ID> (optional)
 }


## DSV Format

Certain types of data may be provided in a delimiter separated values format.

The default format of the separated values files is largely based on the
[RFC 4180 standard](http://tools.ietf.org/html/rfc4180). The specific requirements are:

* lines end with `CR` `LF`
* line breaks cannot be used in values
* any field _may_ be escaped
* the default quote character is `"` (double quotes) 
* any field containing the delimiter must be quoted
* an escape character in an escaped field must be represented by two escape characters
* the first record (row) must contain the names of each field for entries, or each group field for items
* blank lines with no data are ignored

### DSV

The basic DSV format must explicitly specify a delimiter.

 {
  "type":      "dsv",
  "object_id": <string>,
  "delimit":   <string>,
  "quote":     <string>, (optional)
  "line":      <string>  (optional)
 }

### TSV

The TSV format uses <code>TAB</code> as the default delimiter, but it may be overridden.

 {
  "type":      "tsv",
  "object_id": <string>,
  "delimit":   <string>, (optional)
  "quote":     <string>, (optional)
  "line":      <string>  (optional)
 }

### CSV

The CSV format uses <code>,</code> (comma) as the default delimiter, but it may be overridden.

 {
  "type":      "csv",
  "object_id": <string>,
  "delimit":   <string>, (optional)
  "quote":     <string>, (optional)
  "line":      <string>  (optional)
 }