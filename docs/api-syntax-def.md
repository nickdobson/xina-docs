---
id: api-syntax-def
title: Definitions Syntax 
---

> Under Construction

## Group

Defines a XINA group.

| Property   | Value                               |
|------------|-------------------------------------|
| `name`     | `string`                            |
| `desc`     | `string`                            |

## Database

Defines a XINA database. The `name` and `fields` values are required, and at least one field must be provided. If
`label` is not provided it will be the same as `name`.

| Property     | Value                                        |
|--------------|----------------------------------------------|
| `name`       | `string`                                     |
| `label`      | `string` (optional)                          |
| `format`     | `string` (optional)                          |
| `path`       | `string` (optional)                          |
| `desc`       | `string` (optional)                          |
| `dynamic`    | `boolean` (optional, default `false`)        |
| `event`      | `boolean` (optional, default `false`)        |
| `file`       | `boolean` (optional, default `false`)        |
| `link`       | `boolean` (optional, default `false`)        |
| `lock`       | `boolean` (optional, default `false`)        |
| `log`        | `boolean` (optional, default `false`)        |
| `notify`     | `boolean` (optional, default `false`)        |
| `subscribe`  | `boolean` (optional, default `false`)        |
| `tag`        | `boolean` (optional, default `false`)        |
| `track`      | `boolean` (optional, default `false`)        |
| `trash`      | `boolean` (optional, default `false`)        |
| `wall`       | `boolean` (optional, default `false`)        |
| `objects`    | `object` (optional)                          |
| `files`      | `object` (optional)                          |
| `fields`     | `array` of [fields](#field)                  |
| `blobs`      | `array` of [blobs](#blob) (optional)         |
| `indexes`    | `array` of `string` values (optional)        |
| `databases`  | `array` of [databases](#database) (optional) |

### Field

Defines a XINA database field. The <code>name</code> and <code>type</code> are required. If <code>label</code> is not provided it will be the same as <code>name</code>.

 {
  "name"    : <string>,
  "label"   : <string>,  (optional)
  "type"    : <string>,
  "format"  : <string>,  (optional)
  "meas"    : <string>,  (optional)
  "unit"    : <string>,  (optional)
  "desc"    : <string>,  (optional)
  "def"     : <string>,  (optional)
  "ref"     : <string>,  (optional)
  "key"     : <boolean>, (optional, default false)
  "nul"     : <boolean>, (optional, default false)
  "strict"  : <boolean>, (optional, default false)
  "lock"    : <boolean>, (optional, default false)
  "options" : [ <[[#Field Option|field option]]>, ... ] (optional)
 }

#### Field Option

A value option for a field. Regardless of the field type the value here should be a string representation of the actual value.

 {
  "value" : <string>,
  "desc"  : <string>  (optional)
 }

### Blob

Defines a XINA database blob. The <code>name</code> is required. If <code>label</code> is not provided it will be the same as <code>name</code>.

 {
  "name"    : <string>,
  "label"   : <string>, (optional)
  "desc"    : <string>, (optional)
  "nul"     : <boolean> (optional, default false)
 }

### Index

Defines an index on one or more columns of a database record table.


