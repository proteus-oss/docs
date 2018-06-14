---
title: "Proteus: A Groovy DSL to safely run Cassandra migrations"
layout: article
---

## Migrating Schema

Migrating schema can be done in the DSL with the schema_migration() method.
This is the default DSL when running the `proteus.generate` task:

```bash
$ cat cassandra/migrate/20180523125343-new-migration.groovy

schema_migration('20180523125343') {

  // Example of adding a new table
  change {
    create_table('my_new_table_name' ) {
      primary_keys (['id', 'added_date' ])
      clustering_order 'added_date DESC'
      column 'id', 'integer'
      column 'title', 'text'
      column 'uniqueId', 'uuid'
      column 'added_date', 'timestamp'
    }
  }
}
```

All schema migrations must have a version, passed to the schema_migration() function. In this example,
it is the string: `'20180523125343'`. Versions can be any string, but it is highly
recommended to use the default format of `YYYYMMddHHmmSS`.

All schema migrations are wrapped in the schema_migration method:

```bash
schema_migration('20180523125343') {

  // specific instructions
}
```

#### `change` method

The `change` method 


##### `create_table` method

###### `add_colu`


#### `raw_cql` method

The `raq_cql` method allows you to run any valid CQL. It requires two methods:
