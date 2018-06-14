---
title: "Proteus: A Groovy DSL to safely run Cassandra migrations"
layout: article
---

## Guide list

[Proteus documentation](https://github.com/#TBD) is organized as a number of guides, covering all kinds of topics.

We recommend that you read these guides, if possible, in this order:

### [Getting started](/articles/getting_started.html)

An overview of Proteus with a quick tutorial that helps you get started with it. It 
should take about X minutes to read and study the provided code samples. 

### [Migrating Schema](/articles/schema.html)

An overview of how Proteus can be used to migrate schema changes in a safe way: allowing
safe operations, and putting safe practices on dangerous operations.

### [Migrating Data](/articles/data.html)

An overview of how Proteus can be used to add data to new tables and new columns, in order
to allow new tables to be queried. 

### [Deploying Artifacts to a Maven Repository](/artifacts/maven-artifacts.html)

By default, Proteus assumes your files are on a file system. In a production pipeline environment,
these files will want to be versioned artifacts. This guide covers how to build a sample project
to upload these artifacts to an internal maven repository, and how to pull these down and apply them
to an arbitrary cassandra server.

### [Production Pipeline with AWS Lambda](/articles/aws_lambda_deployment.html)

A final guide uses AWS Lambda to run proteus in a serverless environment: Proteus can read credentials
from a secret store and run your versioned artifacts. By running in AWS Lambda you have all the access
that you need: run in whatever VPC is required, leveraging your existing cloud infrastructure.

This guide uses Hashicorp Terraform and Vault to configure the cloud environment and mange secrets.

### [Extending the DSL](/articles/extending_dsl.html)

An overview of how Proteus can be used to add data to new tables and new columns, in order
to allow new tables to be queried. 
