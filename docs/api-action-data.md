---
id: api-action-data
title: Data Actions
---

> Under Construction

Data actions read from or write to XINA databases.

## Read Actions

### `SELECT`

The `SELECT` action is the primary read action for XINA. The syntax closely resembles the MySQL SELECT format.

| Property | Value         |
|----------|---------------|
| `action` | `"select"`    |
| `select` | 

```
{
 "action" : "select",
 "select" : {},
 "use_strings" : 
}
```