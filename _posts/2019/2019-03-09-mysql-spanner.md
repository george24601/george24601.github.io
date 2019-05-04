---
layout: post
title: "Migrating from MySql to Google Cloud Spanner"
description: ""
category: 
tags: []
---

For existing MySql project, the migration cost to Spanner is often too high, including lock-in with GCP and code changes

Main cost changes:
* No incremental id generation
* HAVE to specify index to use for EACH query
* No official JPA support. Have to change code to use JDBC directly
* Change child table schema so that the PK of the parent table is the prefix of child table’s PK
* Limit the index creation to no more than 3 per DAY per db
* Avoid more than 30 DDL statements that require validation or index backfill in a given 7-day period

Migration process (copied from the official docs):
* Convert your schema and data model.
* Translate any SQL queries.
* Migrate your application to use Cloud Spanner in addition to MySQL.
* Bulk export your data from MySQL and import your data into Cloud Spanner using Cloud Dataflow.
* Maintain consistency between both databases during your migration.
* Migrate your application away from MySQL.

