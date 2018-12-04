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

### All

Specifies all columns from the source. This is the same as the MySQL `SELECT *` syntax. This is the default if
no value for the `columns` property is set.

| Property   | Value                               |
|------------|-------------------------------------|
| `type`     | `"all"`                             |

_Example_

```json
{ "type": "all" }
```

### Array

Specifies column(s) as an array of [result column](#result-column) objects. May be provided directly
as a JSON array.

| Property   | Value                                         |
|------------|-----------------------------------------------|
| `type`     | `"array"`                                     |
| `array`    | `array` of [result column](#result-column)(s) |

_Example as JSON object:_

```json
{
 "type"  : "array",
 "array" : [ ... ]
}
```

_Example as JSON array:_

```json
[ ... ]
```

## Result Column

Specifies an expression and optional alias.

| Property   | Value                               |
|------------|-------------------------------------|
| `e`        | [expression](api-syntax-ex.md)      |
| `alias`    | `string` (optional)                 |

## Source

A source is a SQL table (or virtual table) from which a `SELECT` statement loads data.

### Table Source

A source from any table.

| Property   | Value                               |
|------------|-------------------------------------|
| `type`     | `"table"`                           |
| `table`    | `string` table                      |
| `alias`    | `string` (optional)                 |

The table syntax is the same as the table portion of the [column expression syntax](api-syntax-ex.md#columns)

```text
table           = system-table-name | database-table
database-table  = database-path ['@' database-table-name]
```

May also be provided directly as a JSON string (without the `alias` property).

### System Table Source

> Deprecated

A source from a system table.

| Property   | Value                               |
|------------|-------------------------------------|
| `type`     | `"table_system"` or `"ts"`          |
| `table`    | `string` table name                 |
| `alias`    | `string` (optional)                 |

---

### Database Table Source

A source from a database table.

| Property   | Value                                             |
|------------|---------------------------------------------------|
| `type`     | `"table_database"` or `"td"`                      |
| `database` | [database specifier](api-syntax-spec.md#database) |
| `table`    | `string` table name                               |
| `alias`    | `string` (optional)                               |

---

### Join Source

A source derived from a SQL join of two sources.

| Property   | Value                                                       |
|------------|-------------------------------------------------------------|
| `type`     | `"join"`                                                    |
| `op`       | `"join"`, `"left"`, `"left_outer"`, `"inner"`, or `"cross"` |
| `s1`       | left join [source](#source)                                 |
| `s2`       | right join [source](#source)                                |

---

### Select Source

Source from the result of a select statement.

| Property   | Value                                                       |
|------------|-------------------------------------------------------------|
| `type`     | `"select"`                                                  |
| `select`   | [select](api-syntax-sel.md)                                 |


## Order Term

Specifies an expression and optional order.

| Property   | Value                                                       |
|------------|-------------------------------------------------------------|
| `e`        | [expression](api-syntax-ex.md)                              |
| `order`    | `"asc"` or `"desc"` (optional, default `"asc"`              |