---
layout: base
title: Zero Downtime Migrations
summary: How to ensure zero downtime while making changes to DB schema in production
posted_date: 2018-September-21
author: Sourya
---

*Constraints: I will only talk about PostgreSQL, since I have not worked with any other databases in production much.*

## Introduction

For every field you want to add/remove/update in your relational database, you will want to create a migration file that is run against the DB.

For example, if you want to rename the field `name` to `full_name`, you have some SQL code that looks like this:

`ALTER TABLE table_name RENAME COLUMN name TO full_name;`

Now, if you have to make these changes in the DB, you will have to run this code against the DB. For example, by opening the SQL client and typing in the above command.

However, once you rename all your columns to `full_name`, your application will start failing wherever `name` is used. So, you will need to update your application code to use `full_name` instead of `name` everywhere.

From running the SQL command on the DB to updating the application code, there is a delay, however small. During this period, your application will look for the field `name`, which does not exist in the database. For this short period of time, your application will be broken. If you have multiple servers using the same DB, the application will be broken for even longer (depending on the deployment strategy).

Since these migrations in the databases cause downtimes, something we cannot afford in a production environment, we will need to create strategic migrations.

## Cases


### Renaming a column

We have already discussed this in the beginning of the article.

#### Fix
1. we need to add a new column named `full_name`, while keeping the column `name` intact.
2. Add code in application to save data to both `full_name` and `name`.
2. For all rows, we will have to populate the `full_name` column with the value of the `name` column.
3. Once the data is copied, we deploy all the instances of applications using the DB to use column `full_name` only.
4. We can now drop the column `name`.

### Changing a column type

This problem's solution is similar to renaming a column.

#### Fix

1. Add the new column with the new column type.
2. Add code in application to write to both the columns.
3. Copy data from old column to new column.
4. Remove the usage of old column from application code.
5. Drop the old column from DB.

### Adding a new column with a default

If a new column is added with a default, it causes the table to lock for a time depending on the size of the table, disallowing any writes to the table.

#### Fix
1. Add the column without any default value, and without the non-null constraint.
2. Create another migration file (or SQL statement) that now adds a default. (This does not update the existing rows.)
3. Write a script that updates the existing rows with the default value.
4. Add the not-null constraint if required.

Some application frameworks like Django do not set a default on the DB at all. They handle it in the application.

### Adding an index

When indices are added, they lock the entire table to prevent non-read operations. For large tables, this can be an issue.

#### Fix
Use concurrent indexing. This has some [side-effects](https://www.postgresql.org/docs/9.1/static/sql-createindex.html#SQL-CREATEINDEX-CONCURRENTLY), however.

### Removing a column

#### Fix
Change your application code to stop using the column that is to be removed. Once done and deployed, drop the column from the DB.

### Renaming a table

It is similar to renaming a column, except that copying all the data from one table to another with the desired name is not very feasible.

#### Fix
Using [PostgreSQL views](https://www.postgresql.org/docs/9.2/static/sql-createview.html)

Say we want to rename `table_name` to `new_table_name`.

1. Create a table view with the name `new_table_name`.
2. Deploy the application code that uses the new table name instead of the old.
3. Rename the table in the DB.
4. Delete the view.

## How to implement?

While knowing the above-mentioned cases and their fixes is important, the errors might still creep into your code, and lead to some downtime. In order to automate this, there are a few libraries that change the migration files for you, in order to ensure zero downtime.

However, none of these libraries handle all of the above cases.

What I have found helpful is to write a few tests instead, that check in your migrations for the aforementioned red-flags. The tests fail if they do. The author has to either refactor the migrations in a "non-downtime" manner, or add a flag in the migration file like `IGNORE_MIGRATION_CHECKS`, so that the tests pass anyway.
