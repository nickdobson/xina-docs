---
id: api-syntax-sel
title: Select Syntax
---

The `SELECT` syntax is essentially a JSON representation of the MySQL `SELECT` syntax. See the
[MySQL documentation](https://dev.mysql.com/doc/refman/8.0/en/select.html) for more detailed information.

## SELECT

The `SELECT` syntax is contained in a single JSON object.

| Property    | Value                                      | Notes                                                    |
|-------------|--------------------------------------------|----------------------------------------------------------|
| `distinct`  | `boolean`, default `false`                 | If `true`, only returns unique values                    |
| `columns`   | [result columns](#result-columns)          | If empty, returns all columns available from source      |
| `from`      | [source](#source)                          | Source being selected from                               |
| `where`     | [expression](api-syntax-ex.md)             | Condition for rows, where expression returns `true`      |
| `group`     | `array` of [expressions](api-syntax-ex.md) | Used to group rows for aggregation functions             |
| `having`    | [expression](api-syntax-ex.md)             | Like `where`, but can filter aggregation results         |
| `order`     | `array` of [order terms](#order-term)      | Used to sort the results                                 |
| `limit`     | [expression](api-syntax-ex.md)             | Limit the number of rows returned                        |
| `offset`    | [expression](api-syntax-ex.md)             | Offset of the start of the rows                          |

## Result Columns

Specifies the column(s) to select.

=== Columns Array ===

Specifies the column(s) as an array of column objects.

 {
  "type": "array",
  "array": [ <[[#Result Column|result column]]> ... ]
 }

 [ <[[#Result Column|result column]]> ... ]

=== All Columns ===

Specifies all columns in the source. This is the same as the SQL <code>SELECT *</code> query.

 {
  "type": "all"
 }

## Result Column

Specifies an expression and optional alias.

 {
  "e":     <[[XINA API :: Expression Syntax|expression]]>,
  "alias": <string> (optional)
 }

## Source

A source is a SQL table (or virtual table) from which a select statement selects.

### System Table Source

A source from a system table.

 {
  "type":  "table_system",
  "table": <string>
 }

### Database Table Source

A source from a database table.

 {
  "type":     "table_database",
  "database": <[[XINA API :: Specifier Syntax#Database|database specifier]]>,
  "table":    <string>
 }

### Group Table Source

A source from a group table.

 {
  "type":     "table_group",
  "database": <[[XINA API :: Specifier Syntax#Database|database specifier]]>,
  "group":    <[[XINA API :: Specifier Syntax#Group|group specifier]]>
 }

### Join Source

A source derived from a SQL join of two sources.

 {
  "type": "join",
  "op":   <string>, (optional, default "join")
  "s1":   <[[#Source|source]]>,
  "s2":   <[[#Source|source]]>
 }

The options for the <code>op</code> are:

 "join"
 "left"
 "left_outer"
 "inner"
 "cross"

### Select Source

Source from the result of a select statement.

 {
  "type":   "select",
  "select": <[[XINA API :: Select Syntax|select]]>
 }

## Order Term

Specifies an expression and optional order.

 {
  "e":     <[[XINA API :: Expression Syntax|expression]]>,
  "order": <"asc" or "desc"> (optional, default "asc")
 }
