---
id: api-syntax-spec
title: Specifier Syntax 
---

> Under Construction

**Specifiers** are objects which specify schema or data elements.

In general a specifier is an object with a `type` property indicating the type of the specifier. Some specifiers
provide a shorthand version by substituting a different JSON data type.

## Common

There are several common specifiers used by multiple components.

### All

Specifies all elements in the current context.

| Property   | Value                               |
|------------|-------------------------------------|
| `type`     | `"all"`                             |

#### Example

```json
{ "type": "all" }
```

---

### ID

Specifies an element by ID. The value may be provided directly as a JSON number.

| Property   | Value                               |
|------------|-------------------------------------|
| `type`     | `"id"`                              |
| `id`       | `number` or `string`                |

#### Examples

_As JSON object:_

```json
{
 "type" : "id",
 "id"   : 123
}
```

_As JSON number:_

```json
123
```

---

### Name

Specifies an element by name. The value may be provided directly as a JSON string.

| Property   | Value                               |
|------------|-------------------------------------|
| `type`     | `"name"`                            |
| `name`     | `string`                            |

#### Examples

_As JSON object:_

```json
{
 "type" : "name",
 "name" : "foo"
}
```

_As JSON string:_

```json
"foo"
```

---

### Where

Specifies element(s) meeting a condition provided by an [expression](api-syntax-ex.md).

The source against which the expression is used depends on the context, but in general can be represented as `SELECT`
`[elements]` `FROM` `[source]` `WHERE` `[expression]`, where source is the table containing the element.

| Property   | Value                               |
|------------|-------------------------------------|
| `type`     | `"where"`                           |
| `where`    | [expression](api-syntax-ex.md)      |

---

### Array

Specifies elements using an array of singular specifiers. The value may be provided directly as a JSON array.

The types of the individual specifiers depend on the element, but in general unless otherwise noted all singular
specifier types for the element may be used. Specifier types may also be intermingled. 

| Property   | Value                               |
|------------|-------------------------------------|
| `type`     | `"array"`                           |
| `array`    | `array` of specifier(s)             |

#### Examples

_As JSON object:_

```json
{
 "type"  : "array",
 "array" : [ 123, "foo" ]
}
```

_As JSON array:_

```json
[ 123, "foo" ]
```

---

## Schema Elements

### Groups

Specifies one or more groups. [Group specifiers](#group) are also valid groups specifiers.

* [`All`](#all)
* [`Array`](#array)

---

### Group

Specifies a single group.

* [`ID`](#id)
* [`Name`](#name)

---

### Databases

Specifies one or more databases. [Database specifiers](#database) are also valid databases specifiers.

* [`All`](#all)
* [`Array`](#array)

---

### Database

Specifies a single database.

* [`ID`](#id)
* [`Name`](#name)

---

### Fields

Specifies one or more fields. [Field specifiers](#field) are also valid fields specifiers.

* [`All`](#all)
* [`Array`](#array)

---

### Field

Specifies a single field.

* [`ID`](#id)
* [`Name`](#name)

---

### Walls

Specifies one or more walls. [Wall specifiers](#wall) are also valid walls specifiers.

* [`Array`](#array)

---

### Wall

Specifies a single wall.

#### Group Wall

Specifies the wall of single group.

| Property   | Value                               |
|------------|-------------------------------------|
| `type`     | `"group"`                           |
| `group`    | [group specifier](#group)           |

_Example_

```json
{
 "type"  : "group",
 "group" : "foo"
}
```

---

#### Database Wall

Specifies the wall of single database.

| Property   | Value                               |
|------------|-------------------------------------|
| `type`     | `"database"`                        |
| `database` | [database specifier](#database)     |

_Example_

```json
{
 "type"     : "database",
 "database" : "foo"
}
```

---

#### Record Wall

Specifies the wall of single record.

| Property   | Value                               |
|------------|-------------------------------------|
| `type`     | `"record"`                          |
| `database` | [database specifier](#database)     |
| `record`   | [record specifier](#record)         |

_Example_

```json
{
 "type"     : "database",
 "database" : "foo",
 "record"   : 123
}
```
 
#### User Wall

Specifies the wall of single user.

| Property   | Value                               |
|------------|-------------------------------------|
| `type`     | `"user"`                            |
| `user`     | [user specifier](#user)             |

_Example_

```json
{
 "type" : "user",
 "user" : "foo"
}
```

---

## Data Elements

### Records

Specifies a set of records in a single database. [Wall specifiers](#wall) are also valid records specifiers.

* [`All`](#all)
* [`Array`](#array)
* [`Where`](#where)

---

### Record

Specifies a single record in a database.

* [`ID`](#id)

#### Key

Specifies a single record by a set of key value(s).

 {
  "type" : "key",
  "key"  : <[[XINA API :: Data Syntax#Fields|fields]]>
 }

Each key must specify a non-null value for each key field of the database.

---

### Tags

Specifies tags. [Tag specifiers](#tag) are also valid tags specifiers.

* [`All`](#all)
* [`Array`](#array)
* [`Where`](#where)

---

### Tag

Specifies a single tag.

> Note that `name` in this case refers to the tag itself.

* [`Name`](#name)

---

### Posts

Specifies a set of posts. [Post specifiers](#post) are also valid posts specifiers.

* [`All`](#all)
* [`Array`](#array)
* [`Where`](#where)

---

### Post

Specifies a single post.

* [`ID`](#id)

## Administrative

### Users

Specifies a set of users. [User specifiers](#user) are also valid users specifiers.

* [`All`](#all)
* [`Array`](#array)
* [`Where`](#where)

---

### User

Specifies a single user.

> Note that `name` in this case refers to the username, not the user's full name.

* [`ID`](#id)
* [`Name`](#name)

---

### Group Privileges

Specifies a set of group privileges.

* [`All`](#all)
* [`Array`](#array)

---

### Group Privilege

Specifies a single group privilege as a JSON string. The valid group privileges are:

* `"select"`
* `"post"`
* `"reply"`
* `"alter"`
* `"grant"`

---

### Database Privileges

Specifies a set of database privileges.

* [`All`](#all)
* [`Array`](#array)

---

### Database Privilege

Specifies a single database privilege as a JSON string. The valid database privileges are:

* `"select"`
* `"post"`
* `"reply"`
* `"update"`
* `"insert"`
* `"trash"`
* `"delete"`
* `"lock"`
* `"alter"`
* `"grant"`
