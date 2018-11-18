---
id: api-action-admin
title: Admin Actions
---

> Under Construction

Administrative actions create, modify, or delete XINA data structures, perform user management, or perform task management.

## Structural Actions

### `CREATE`

The `CREATE` action is used to create new groups, teams, and databases.

#### `CREATE GROUP`
```
{
  "action" : "create",
  "create" : "group",
  "parent" : <group (optional)>
  "group"  : <group definition>
}
```

#### `CREATE DATABASE`
```
{
  "action"   : "create",
  "create"   : "database",
  "parent"   : <group or database>
  "database" : <database definition>
}
```


#### `CREATE TEAM`
```
{
  "action" : "create",
  "create" : "team",
  "team"   : <team definition>
}
```



---


### `ALTER`

The **ALTER** action changes the definition of a group, database, or user.

---

### `DROP`

The **ALTER** action changes the definition of a group, database, or user.

---

## User Actions


## Task Actions


### `cancel`

The **CANCEL** action cancels task(s) assigned to the XINA Run application.

```
{
  "action" : "cancel",
  "tasks"  : [ <task ID>, ... ]
}
```