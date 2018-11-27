---
id: xina-types
title: Data Types
---

> Under Construction

XINA has a fixed set of **data types** which apply to attributes and fields. They are intended to provide consistent
behavior across MySQL, Java, and JavaScript data types.

## Numeric Types

| Type       | Java      | MySQL      | JavaScript  | Notes                                                            |
|------------|-----------|------------|-------------|------------------------------------------------------------------|
| `int(1)`   | `byte`    | `tinyint`  | `number`    | integer, -2<sup>8</sup> to 2<sup>8</sup>-1                       |
| `int(2)`   | `short`   | `smallint` | `number`    | integer, -2<sup>16</sup> to 2<sup>16</sup>-1                     |
| `int(4)`   | `int`     | `int`      | `number`    | integer, -2<sup>32</sup> to 2<sup>32</sup>-1                     |
| `int(8)`   | `long`    | `bigint`   | `number`    | integer, -2<sup>64</sup> to 2<sup>64</sup>-1 **!**               |
| `float(4)` | `float`   | `float`    | `number`    | IEEE 754 4 byte floating point                                   |
| `float(8)` | `double`  | `double`   | `number`    | IEEE 754 8 byte floating point                                   |
| `boolean`  | `boolean` | `tinyint`  | `boolean`   | MySQL treats 0 as `false`, non-zero as `true`                    |

> ! JavaScript number is 8 byte float, so only -2<sup>53</sup> to 2<sup>53</sup>-1 is stored with exact precision

---

## Character Types

Character data types offer two encoding options:

* **UTF-8** - default encoding, variable length, 1 to 4 bytes per character
* **ASCII** - subset of UTF-8, fixed length, 1 byte per character

Two SQL types:

* **char(n)** - data stored in the table, fast search and index, uses fixed amount of space per row (n * max_bytes_per_character)
* **text** - data stored outside the table, slower search and index, uses only as much space as needed

Two general types:

* **string** - text is **normalized** before insertion
  * leading and trailing whitespace is trimmed
  * all internal whitespace is reduced to a single space character
* **text** - text is inserted as provided

> Note, all string operations are case-insensitive by default. This can be overridden with the
  [collate](api-syntax-ex.md#collate) expression by specifying a binary collation.

| Type             | Java     | MySQL        | JavaScript | Notes                                                      |
|------------------|----------|--------------|------------|------------------------------------------------------------|
| `utf8string(n)`  | `string` | `char(n)`    | `string`   | n up to 128, uses n*4 bytes, normalized                    |
| `utf8string`     | `string` | `mediumtext` | `string`   | up to 2<sup>24</sup> bytes, normalized                     |
| `utf8text`       | `string` | `mediumtext` | `string`   | up to 2<sup>24</sup> bytes, not normalized                 |
| `asciistring(n)` | `string` | `char(n)`    | `string`   | n up to 256, uses n*4 bytes, normalized                    |
| `asciistring`    | `string` | `mediumtext` | `string`   | up to 2<sup>24</sup> bytes, normalized                     |
| `asciitext`      | `string` | `mediumtext` | `string`   | up to 2<sup>24</sup> bytes, not normalized                 |

---

## Temporal Types

Temporal data types store time data. There are two categories of temporal types:

* **instants** - identify specific moment in time, independent of time zone
  * stored numerically in the database in milliseconds
  * `datetime` and `date` use Unix epoch
  * `datetime` and `date` comparable in database
  * `date` + `time` = `datetime`
  * typically displayed in local time zone in front-end applications
* **timestamps** - identify specific formatted time without time zone consideration (thus `local`)
  * stored as ISO 8601 formatted `string` in database
  * `localdate` and `localdatetime` comparable in database
  * `CONCAT(localdate, 'T', localtime)` = `localdatetime`

| Type            | Java            | MySQL      | JavaScript | Notes                                                   |
|-----------------|-----------------|------------|------------|---------------------------------------------------------|
| `datetime`      | `DateTime`      | `bigint`   | `date`     | instant with millisecond precision, as Unix time        |
| `date`          | `XDate`         | `bigint`   | `date`     | instant at start of date UTC, as Unix time              |
| `time`          | `LocalTime`     | `int`      | `number`   | length of time up to 23:59:59.999, as millisecond count |
| `localdatetime` | `LocalDateTime` | `char(24)` | `string`   | full timestamp without timezone, stored as `string`     |
| `localdate`     | `LocalDate`     | `char(10)` | `string`   | date without timezone, stored as `string`               |
| `localtime`     | `LocalTime`     | `char(12)` | `string`   | length of time up to 23:59:59.999, as `string`          |

---

## JSON Types

JSON data types store JSON data directly in the database. 

| Type         | Java         | MySQL  | JavaScript |
|--------------|--------------|--------|------------|
| `json`       | `JsonValue`  | `json` | `*`        |
| `jsonarray`  | `JsonArray`  | `json` | `array`    |
| `jsonobject` | `JsonObject` | `json` | `object`   |
