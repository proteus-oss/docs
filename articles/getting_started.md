---
title: "Proteus: A Groovy DSL to safely run Cassandra migrations"
layout: article
---

## About this guide

This guide covers an overview of Proteus with a quick tutorial that helps you get started with it. It 
should take about 5 minutes to read and study the provided code samples. This guide covers:

 * Features of Proteus 
 * Required dependencies
 * How to add Proteus to your projects
 * A very brief introduction to Proteus capabilities
 * Examples of the most common operations with Proteus

This work is licensed under a <a rel="license" href="#">Some License 2.0</a>. The source is available 
[on Github](https://github.com/#TBD).

### What version of Proteus does this guide cover?

This guide covers Proteus 1.x releases

### Proteus Overview

Proteus is a Groovy domain specific language (DSL) for applying data and schema migrations to Cassandra. It
is analagous to Ruby-on-Rails ActiveRecord migrations. Proteus runs as a groovy plugin in your development
environment providing fast feedback loops.

Proteus supports keeping track of what changes have been deployed to an environment and when, and allows you to 
limit operations to 'safe' operations. For example, adding a table is a safe operation, but deleting one is not.

### Supported Cassandra Versions

Proteus requires Cassandra 3.x+. The latest stable release is recommended. Cassandra is required to have native
protocol port enabled and accessible to Proteus (default 9042).

### Supported Java Versions

Proteus uses Gradle to handle all dependencies, but does require a Java Development Kit (JDK) to be installed. Proteus
requires JDK 1.8.x+. The latest stable release is recommended

Note that this does not require your project to be in Java or Groovy! You are able to use this gradle plugin independent
of what your own build system is.

## Adding Proteus To Your Project

Proteus artifacts are [released to Maven Central](https://maven.apache.org/repository/), a popular repository
for Java open-source software. 

In order to add proteus to your project you will need to do a short installation:

### Gradle based installation

This is the easiest and recommended way is to start by [installing Gradle](https://gradle.org/install/):

```bash
$ gradle -v

------------------------------------------------------------
Gradle 4.7
------------------------------------------------------------
```

Aftewards, you can run a gradle init on your project:

```bash
$ cd /my/project/dir
$ gradle init
```

This creates a new gradle projects -- `.gradle` directory can be excluded from your SCM. It creates two files which you
can edit:

```bash
./build.gradle
./settings.gradle
```

Add the following to `build.gradle`

```bash
apply plugin: 'tools.pingdev.proteus-plugin'
cassandra {
    keyspace = "proteus_test"
}
```

### File-based installation

If you are not comfortable installing build tools, you may simply copy the required files to support a 
'gradle wrapper' installation. This does not require an out-of-band installation of tools and can be 
directly checked into your repository.

You can do this by copying the following project:

```bash
$ git clone github.com/X/Y
$ cd Y
$ rm -rf .git
$ cp -r * /my/project/dir
$ cd /my/project/dir
$ ./gradlew -v

------------------------------------------------------------
Gradle 4.7
------------------------------------------------------------
```

### Connecting Over Native Protocol

Proteus requires that your cassandra server has native protocol port enabled and accessible (default port: 9042)


## Sample Demo

There is a sample demo showing basic features of proteus. To use it, clone this repository:

https://github.com/#TBD

```bash
git clone git@github.com/TBD
```

Then run a cassandra image:

```bash
$ docker pull cassandra:3.11
$ docker run --name proteus-cassandra -d -p 9042:9042 cassandra:3.11
```

You can connect with [cqlsh](https://pypi.org/project/cqlsh/)

```bash
Connected to Test Cluster at 127.0.0.1:9042.
[cqlsh 5.0.1 | Cassandra 3.11.2 | CQL spec 3.4.4 | Native protocol v4]
Use HELP for help.
cqlsh> describe keyspaces

system_traces  system_schema  system_auth  system  system_distributed
```

Note that these are system default keyspaces

Edit `build.gradle` and uncomment this section:

```bash
#cassandra {
#    keyspace = "proteus_test"
#}
```

#### Listing tasks
Use `./gradlew tasks` --all to list all tasks:

```bash
All tasks runnable from root project
# [...]

Other tasks
-----------
proteus.generate
proteus.local.apply
proteus.local.bootstrap
```

#### Bootstrapping the keyspace

This creates the keyspace, if it does not exist, suitable for a local environment:

```bash
$ ./gradlew proteus.local.bootstrap

> Task :dsl.local.bootstrap
bootstrapping cassandra
Bootstrapping keyspace cassandra_migration_test
        CREATE KEYSPACE IF NOT EXISTS proteus_tst WITH replication = {'class': 'SimpleStrategy', 'replication_factor': '1'}  AND durable_writes = true;
```


#### Adding a new table

To generate a new DSL file, use the `proteus.generate` task:

```bash
$ ./gradlew proteus.generate

> Task :proteus.generate
Generating new migration file
Created file "cassandra/migrate/20180523125343-new-migration.groovy" -- please see inside for details.
DSL info: https://cassandra.pingdev.tools
```

This generates a new Groovy DSL file in the `cassandra/migrate` folder, describing how to
generate a new table as part of a `schema_migration`:

```groovy
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

You can then run this on your local cassandra with the `proteus.local.apply` task:

```bash
./gradlew proteus.local.apply

./gradlew proteus.local.apply

> Task :proteus.local.apply
Applying migrations to local environment
Found file cassandra/migrate/20180516144558-new-migration.groovy
CQL to run: CREATE TABLE my_new_table_name (
id text,
title text,
uniqueId text,
added_date text,
PRIMARY KEY (id,added_date)
)
WITH CLUSTERING ORDER BY (added_date DESC)
AND COMMENT='Auto generated by Proteus';
Returned results: [status:successfully migrated, previous_schema_version:, current_schema_version:20180516144558, updates_applied:[20180516144558]]


BUILD SUCCESSFUL in 1s
1 actionable task: 1 executed
```

#### Cassandra connectivity

Cassandra must have native port open (default: 9042). By default it assumes host `localhost`, port `9042`, and
no authentication. You can configure this with options in your `build.gradle` file:

```groovy
cassandra {
    keyspace = "proteus_test"
    host = "cassandra.internal.example.com"
    port = 19042
    user = "admin"
    password = "password"
}
```

### What to Read Next

The documentation is organized as [a number of guides](/articles/guides.html), covering different topics in depth:

 * [Migrating Schema](/articles/migrating_schema.html)
 * [Migrating Data](/articles/migrating_data.html)
 * [Deploying Artifacts to a Maven Repository](/artifacts/maven-artifacts.html)
 * [Production Pipeline with AWS Lambda](/articles/aws_lambda_deployment.html)
 * [Extending the DSL](/articles/extending_dsl.html)

They also cover topics from this guide in more detail.
