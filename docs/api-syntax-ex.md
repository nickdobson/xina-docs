---
id: api-syntax-ex
title: Expression Syntax 
---

XINA expressions translate to MySQL expressions, which are evaluated as a query is executed.

## Literals

Literal expressions represent a single, discrete value.

### Null

The MySQL `NULL` value. May also be specified with the JSON `null` value.

| Property   | Value                               |
|------------|-------------------------------------|
| `type`     | `"null"`                             |

_Example_

```
{ "type": "null" }
```

### Number

A numeric literal value. The value may be provided as a native JSON number, or encoded as a string.
May also be provided directly as a JSON `number` value.

| Property   | Value                               |
|------------|-------------------------------------|
| `type`     | `"number"`                          |
| `value`    | `number` or `string`                |

_Example (as object)_

```
{
 "type"  : "number",
 "value" : 123
}
```

### String

A string literal value. May also be provided directly as a JSON `string`.

| Property   | Value                               |
|------------|-------------------------------------|
| `type`     | `"string"`                          |
| `value`    | ``string`                           |

_Example (as object)_

```
{
 "type"  : "string",
 "value" : "foo"
}
```

### Datetime

A datetime literal value.

 {
  "type": "datetime",
  "value": <string>
 }

The value must be encoded according to the following syntax, taken from the ISO8601 standard:

```text
date-opt-time     = date-element ['T' [time-element] [offset]]
date-element      = std-date-element | ord-date-element | week-date-element
std-date-element  = yyyy ['-' MM ['-' dd]]
ord-date-element  = yyyy ['-' DDD]
week-date-element = xxxx '-W' ww ['-' e]
time-element      = HH [minute-element] | [fraction]
minute-element    = ':' mm [second-element] | [fraction]
second-element    = ':' ss [fraction]
fraction          = ('.' | ',') digit+
offset            = 'Z' | (('+' | '-') HH [':' mm [':' ss [('.' | ',') SSS]]])
```

If the offset is not provided the timezone will be assumed to be UTC.

### Local Datetime

A local datetime literal value.

 {
  "type": "localdatetime",
  "value": <string>
 }

The value must be encoded according to the same syntax as the datetime literal, except with the offset omitted.

## Columns

'''Column''' expressions represent a column of a table.

=== System Parameter Column ===

Specifies a column of a system table.

 {
  "type":      "column_system",
  "table":     <string>,
  "parameter": <string>
 }

=== Database Attribute Column ===

Specifies an attribute column of a database table.

 {
  "type":      "column_database_parameter",
  "database":  <database specifier>,
  "table":     <string>,
  "parameter": <string>
 }

=== Database Field Column ===

Specifies a field column of an entry table.

 {
  "type":     "column_database_field",
  "database": <[[XINA API :: Specifier Syntax#Database|database specifier]]>
  "field":    <[[XINA API :: Specifier Syntax#Field|field specifier]]>
 }

### Alias

Although the alias is not technically a column, it can refer directly by name to any column in the source, or to an alias of a result column.

 {
  "type": "alias",
  "alias": <string>
 }

## Evaluations

Evaluations are expressions evaluated by MySQL.

### Between

Returns true if the expression <code>e</code> is between <code>min</code> and <code>max</code>. See [http://dev.mysql.com/doc/refman/5.5/en/comparison-operators.html#operator_between here] for more information.

 {
   "type": "between",
   "e": <expression>,
   "min": <expression>,
   "max": <expression>
 }

### Binary

Binary operation, evaluated as <code>e1</code> <code>op</code> <code>e2</code>.

 {
   "type": "binary",
   "op": <string>,
   "e1": <expression>,
   "e2": <expression>
 }

Valid binary operators are as follows:

```text
 AND     (logical AND)
 OR      (logical OR)
 =       (equal)
 !=      (not equal)
 >       (greater)
 >=      (greater or equal)
 <       (less)
 <=      (less or equal)
 IS      (test against NULL)
 LIKE    (simple pattern matching, see [http://dev.mysql.com/doc/refman/5.5/en/string-comparison-functions.html#operator_like here])
 REGEXP  (advanced pattern matching, see [http://dev.mysql.com/doc/refman/5.5/en/regexp.html#operator_regexp here])
 +       (addition)
 -       (subtraction)
 *       (multiplication)
 /       (division)
 %       (modulus)
 &       (bit-wise AND)
 |       (bit-wise OR)
 <<      (left shift)
 >>      (right shift)
```


### Case

Case logic expression. If the <code>base</code> is provided, returns the <code>then</code> expression of the first case in which <code>when</code> = <code>base</code>. Otherwise returns the first case in which <code>when</code> is <code>true</code>. If no case is satisfied returns <code>else</code> if it is provided, or <code>null</code> otherwise.

 {
  "type": "case",
  "base": <expression>, (optional)
  "cases": [
   {
    "when": <expression>,
    "then": <expression>
   }, ...
  ],
  "else": <expression> (optional)
 }

### Count Rows

Performs the MySQL `COUNT(*)` function.

 {
  "type": "count_rows"
 }

### Exists

Returns true if the enclosed query returns at least one row.

 {
  "type": "exists",
  "select": <select>
 }

### Function

Performs a MySQL function. The number of arguments varies depending on the function,

 {
  "type": "function",
  "function": <string>,
  "args": [ <expression>, ... ]
 }

Available functions are:

{| class="wikitable"
!Name
!Args
!Aggregate
!Description
|-
|<code>AVG</code>
|1
|yes
|arithmetic average
|-
|<code>AVG_DISTINCT</code>
|1
|yes
|arithmetic average of distinct values of argument
|-
|<code>BIT_AND</code>
|1
|yes
|bit-wise AND
|-
|<code>BIT_OR</code>
|1
|yes
|bit-wise OR
|-
|<code>BIT_XOR</code>
|1
|yes
|bit-wise XOR
|-
|<code>CEIL</code>
|1
|yes
|returns the smallest integer value not less than the argument
|-
|<code>COUNT</code>
|1
|yes
|returns the number of rows in the which the argument is not <code>NULL</code>
|-
|<code>COUNT_DISTINCT</code>
|n
|yes
|returns the number of distinct value(s) of the arguments
|-
|<code>FLOOR</code>
|1
|yes
|returns the largest integer value not greater than the argument
|-
|<code>MAX</code>
|1
|yes
|returns the maximum value of the argument
|-
|<code>MIN</code>
|1
|yes
|returns the minimum value of the argument
|-
|<code>POW</code>
|2
|no
|
|-
|<code>STDDEV_POP</code>
|1
|yes
|returns the population standard deviation of the argument
|-
|<code>STDDEV_SAMP</code>
|1
|yes
|returns the sample standard deviation of the argument
|-
|<code>SUM</code>
|1
|yes
|returns the sum of the argument
|-
|<code>SUM_DISTINCT</code>
|1
|yes
|returns the sum of the distinct values of the argument
|-
|<code>TRUNCATE</code>
|2
|no
|
|-
|<code>VAR_POP</code>
|1
|yes
|returns the population variance of the argument
|-
|<code>VAR_SAMP</code>
|1
|yes
|returns the sample variance of the argument
|}

=== In ===

Returns true if an expression is within a set of values.

 {
  "type": "in",
  "e": <expression>,
  "values": [ <expression>, ... ]
 }

=== In Select ===

Returns true if <code>e</code> is in the result of the <code>select</code> query.

 {
  "type": "in_select",
  "e": <expression>,
  "select": <[[XINA API :: Select Syntax#Select|select]]>
 }

=== Select Expression ===

Returns the value of the first column in the first row of the result set of the query.

 {
  "type": "select",
  "select": <[[XINA API :: Select Syntax#Select|select]]>
 }
