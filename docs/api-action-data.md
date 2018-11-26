---
id: api-action-data
title: Data Actions
---

> Under Construction

Data actions read from or write to XINA databases.

## Read Actions

### `SELECT`

The `SELECT` action is the primary read action in XINA. It closely mirrors the MySQL `SELECT` query, and returns data
as a table of values. The full syntax for the `SELECT` object is available [here](api-syntax-sel.md).

| Property      | Value                                 |
|---------------|---------------------------------------|
| `action`      | `"select"`                            |
| `select`      | [select](api-syntax-sel.md)           |
| `use_strings` | `boolean` (optional, default `false`) |
| `rows`        | `integer` (optional, default `1000`)  |

The server response to a `SELECT` action will start with a header packet, containing a JSON array of JSON object(s)
indicating the `name` of each column as a `string` and the XINA data type of each column as a `string`. This will be
followed by packet(s) containing the data, as a JSON array of of JSON array(s) of values. The optional `rows` property
sets the limit of rows per packet. If the `use_strings` property is `true`, all values will be stored as JSON strings
instead of their associated JSON type.

_Example_

Given a table `t` with two columns, `a` (`int(4)`), and `b` (`utf8text`), and three rows:

| `a` |  `b`  |
|-----|-------|
| `0` | `"x"` |
| `1` | `"y"` |
| `2` | `"z"` |

The following `SELECT` action:

```json
{
 "action": "select",
 "select": {
  "from": "t"
 },
 "rows": 2
}
```

Would return three server packets.

First, the header information:

`100`

```json
[
 {
  "name": "a",
  "type": "int(4)"
 },
 {
  "name": "b",
  "type": "utf8text"
 }
]
```

Second, the first two rows (limited by the `rows` property):

`100`

```json
[
 [ 0, "x" ],
 [ 1, "y" ]
]
```

Third, the last remaining row (with the status code indicating the end of the data):

`200`

```json
[
 [ 2, "z" ]
]
```

---

### `FETCH`

> Under Construction

The `FETCH` action reads data from the database in a more structured format than the `SELECT` action. The syntax differs
depending on what type of data is being fetched.

#### Records

Fetches records from a database.

| Property       | Value                                                           |
|----------------|-----------------------------------------------------------------|
| `action`       | `"fetch"`                                                       |
| `fetch`        | `"records"`                                                     |
| `database`     | [database](api-syntax-spec.md#database)                         |
| `records`      | [records](api-syntax-data.md#records) (optional)                |
| `where`        | [expression](api-syntax-ex.md) (optional)                       |
| `order`        | array of [order terms](api-syntax-sel.md#order-term) (optional) |
| `limit`        | [expression](api-syntax-ex.md) (optional)                       |
| `offset`       | [expression](api-syntax-ex.md) (optional)                       |

Fetched records are returned as JSON objects. Unlike the `SELECT` action there is no header packet. Each packet
will contain a JSON array of JSON object representations of records, with a `100` code if more are available, or a `200`
code if all records have been sent.

---

### `DOWNLOAD`

> Under Construction

## Write Actions

### `INSERT`

The `INSERT` action inserts one or more records into a XINA database.

By default, the action will fail if any records being inserted have duplicate key values already in the database. If a
different `on_duplicate` property is set, duplicate records will be updated according to the rules in the table. Only
fields explicitly set in the `INSERT` will be changed. This is analogous to an `INSERT ... ON DUPLICATE KEY UPDATE`
MySQL query.

| Property       | Value                                               |
|----------------|-----------------------------------------------------|
| `action`       | `"insert"`                                          |
| `database`     | [database](api-syntax-spec.md#database)             |
| `records`      | [records](api-syntax-data.md#records)               |
| `on_duplicate` | `"fail"` or `"update"` (optional, default `"fail"`) |

_Examples_

Given a starting database containing key field `k`, fields `v1`, `v2`, and `v3`, with tags enabled, containing the
following two records:

| `k` | `v1` | `v2` | `v3` | `tags` |
|-----|------|------|------|--------|
| a   | 1    | 2    | 3    | t1     |
| b   | 1    | 2    | 3    | t1     |

And inserting records:

```json
[
 { "k": "a", "v1": 4, "v2": null, "tags": ["t2"] },
 { "k": "c", "v1": 1, "v2": null, "tags": ["t2"] }
]
```

**`"on_duplicate": "fail"`**

Action fails due to duplicate key value `"a"`. No change occurs.

**`"on_duplicate": "update"`**

Record with key value `"a"` is updated, and record with key value `"c"` is inserted. Note that field `v3` of `"a"` is
unaffected because no inserted records specified an explicit value for `v3`.

| `k` | `v1` | `v2` | `v3` | `tags` |
|-----|------|------|------|--------|
| a   | <b style="color:blue">4</b> | <b style="color:blue">null</b> | 3 | t1, <b style="color:blue">t2</b> |
| b   | 1    | 2    | 3    | t1     |
| <b style="color:blue">c</b> | <b style="color:blue">1</b> | <b style="color:blue">null</b> | <b style="color:blue">null</b> | <b style="color:blue">t2</b> |

---

### `REPLACE`

The `REPLACE` action inserts one or more records into a XINA database and **overwrites** any existing records with
duplicate keys.

| Property       | Value                                                                                               |
|----------------|-----------------------------------------------------------------------------------------------------|
| `action`       | `"replace"`                                                                                         |
| `database`     | [database](api-syntax-spec.md#database)                                                             |
| `records`      | [records](api-syntax-data.md#records)                                                               |
| `on_duplicate` | `"update"`, `"delete"`, or `"trash"` (if trash enabled for database) (optional, default `"update"`) |

_Examples_

Given a starting database containing key field `k`, fields `v1`, `v2`, and `v3`, with tags enabled, containing the
following two records:

| `k` | `v1` | `v2` | `v3` | `tags` |
|-----|------|------|------|--------|
| a   | 1    | 2    | 3    | t1     |
| b   | 1    | 2    | 3    | t1     |

And replacing records:

```json
[
 { "k": "a", "v1": 4, "v2": null, "tags": ["t2"] },
 { "k": "c", "v1": 1, "v2": null, "tags": ["t2"] }
]
```

**`"on_duplicate": "update"`**

Record with key value `"a"` is updated, and record with key value `"c"` is inserted. Note that `v3` of `"a"` is now
`null` and `t1` is removed because all fields are overridden by the incoming record.

| `k` | `v1` | `v2` | `v3` | `tags` |
|-----|------|------|------|--------|
| a   | <b style="color:blue">4</b> | <b style="color:blue">null</b> | <b style="color:blue">null</b> | <b style="color:blue">t2</b> |
| b   | 1    | 2    | 3    | t1     |
| <b style="color:blue">c</b> | <b style="color:blue">1</b> | <b style="color:blue">null</b> | <b style="color:blue">null</b> | <b style="color:blue">t2</b> |

**`"on_duplicate": "trash"`** or **`"on_duplicate": "delete"`**

Existing record with key value `"a"` is deleted (or trashed), and new records `"a"` and `"c"` are inserted.

| `k` | `v1` | `v2` | `v3` | `tags` |
|-----|------|------|------|--------|
| b   | 1    | 2    | 3    | t1     |
| <b style="color:blue">a</b> | <b style="color:blue">4</b> | <b style="color:blue">null</b> | <b style="color:blue">null</b> | <b style="color:blue">t2</b> |
| <b style="color:blue">c</b> | <b style="color:blue">1</b> | <b style="color:blue">null</b> | <b style="color:blue">null</b> | <b style="color:blue">t2</b> |

If `"trash"` is used, the trash table now contains the original `"a"` record.

| `k` | `v1` | `v2` | `v3` | `tags` |
|-----|------|------|------|--------|
| <b style="color:blue">a</b> | <b style="color:blue">1</b> | <b style="color:blue">2</b> | <b style="color:blue">3</b> | <b style="color:blue">t1</b> |

---

### `SET`

The `SET` action sets a database to contain the provided records.


| Property       | Value                                                                                               |
|----------------|-----------------------------------------------------------------------------------------------------|
| `action`       | `"set"`                                                                                             |
| `database`     | [database](api-syntax-spec.md#database)                                                             |
| `records`      | [records](api-syntax-data.md#records)                                                               |
| `on_duplicate` | `"update"`, `"delete"`, or `"trash"` (if trash enabled for database) (optional, default `"update"`) |
| `on_remove`    | `"delete"` or `"trash"` (if trash enabled for database) (optional, default `"trash"` if enabled)    |

_Examples_

Given a starting database containing key field `k`, fields `v1`, `v2`, and `v3`, with tags enabled, containing the
following two records:

| `k` | `v1` | `v2` | `v3` | `tags` |
|-----|------|------|------|--------|
| a   | 1    | 2    | 3    | t1     |
| b   | 1    | 2    | 3    | t1     |

And setting records:

```json
[
 { "k": "a", "v1": 4, "v2": null, "tags": ["t2"] },
 { "k": "c", "v1": 1, "v2": null, "tags": ["t2"] }
]
```

**`"on_duplicate": "update"`**

Record `"a"` is updated, record `"c"` is inserted, and record `"b"` is deleted (or trashed, depending on `on_remove`).
Note that `v3` of `"a"` is now `null` and `t1` is removed because all fields are overridden by the incoming record.

| `k` | `v1` | `v2` | `v3` | `tags` |
|-----|------|------|------|--------|
| a   | <b style="color:blue">4</b> | <b style="color:blue">null</b> | <b style="color:blue">null</b> | <b style="color:blue">t2</b> |
| <b style="color:blue">c</b> | <b style="color:blue">1</b> | <b style="color:blue">null</b> | <b style="color:blue">null</b> | <b style="color:blue">t2</b> |

**`"on_duplicate": "trash"`** or **`"on_duplicate": "delete"`**

All existing records are deleted (or trashed, depending on `on_remove`), and new records `"a"` and `"c"` are inserted.

| `k` | `v1` | `v2` | `v3` | `tags` |
|-----|------|------|------|--------|
| <b style="color:blue">a</b> | <b style="color:blue">4</b> | <b style="color:blue">null</b> | <b style="color:blue">null</b> | <b style="color:blue">t2</b> |
| <b style="color:blue">c</b> | <b style="color:blue">1</b> | <b style="color:blue">null</b> | <b style="color:blue">null</b> | <b style="color:blue">t2</b> |

---

### `UPDATE`

> Under Construction

---

### `DELETE`

The `DELETE` action deletes one or more records from a database.

> Deleted records and all associated data are **permanently deleted** and cannot be restored.

This action requires the `DELETE` privilege.
                                                                 
| Property       | Value                                     |
|----------------|-------------------------------------------|
| `action`       | `"delete"`                                |
| `database`     | [database](api-syntax-spec.md#database)   |
| `records`      | [records](api-syntax-data.md#records)     |

---

### `TRASH`

The `TRASH` action moves one or more records into the trash table of a database. This is only available in databases
with the trash feature enabled, otherwise the action will fail.

This action requires the `TRASH` privilege.
                                                                 
| Property       | Value                                     |
|----------------|-------------------------------------------|
| `action`       | `"trash"`                                 |
| `database`     | [database](api-syntax-spec.md#database)   |
| `records`      | [records](api-syntax-data.md#records)     |

---

### `RESTORE`

The `RESTORE` action moves one or more records from the trash table of a database into the record table. This is only
available in databases with the trash feature enabled, otherwise the action will fail.

If any records being restored have duplicate keys as other records currently in the database the action will fail.

This action requires the `TRASH` privilege.
                                                                 
| Property       | Value                                     |
|----------------|-------------------------------------------|
| `action`       | `"restore"`                               |
| `database`     | [database](api-syntax-spec.md#database)   |
| `records`      | [records](api-syntax-data.md#records)     |

---

### `DISPOSE`

The `DISPOSE` action deletes one or more records from the trash table of a database.

> Deleted records and all associated data are **permanently deleted** and cannot be restored.

This action requires the `DELETE` privilege.
                                                                 
| Property       | Value                                     |
|----------------|-------------------------------------------|
| `action`       | `"dispose"`                               |
| `database`     | [database](api-syntax-spec.md#database)   |
| `records`      | [records](api-syntax-data.md#records)     |
---
