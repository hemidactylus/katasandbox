Let's have a look at the virtual tables available, and the keyspaces containing
them.

First, list the existing keyspaces in the CQL console with:
```
DESCRIBE KEYSPACES;
```{{execute T2}}

Make sure keyspaces `system_virtual_schema` and `system_views` are there: they
are special-purpose keyspaces, designed to host virtual tables only.

We will work with the `system_views` keyspace, so let us make it the default
one for subsequent operations:
```
USE system_views;
```{{execute T2}}

A virtual table that can provide valuable insights about read performance
is `tombstones_per_read`: for each table in the database, it gives you
statistics on how many tombstones are encountered while reading.
Have a closer look at the table structure with:
```
DESCRIBE TABLE tombstones_per_read;
```{{execute T2}}

Remember that the output of this command is for reference only, since
(as we will soon see) you cannot directly alter these tables.

Another very important table is `settings`: it provides a way to
programmatically access the whole configuration as specified in file
`cassandra.yaml`. Try reading the table in its entirety:
```
SELECT * FROM settings;
```{{execute T2}}

How many rows does the table contain? (Hint: press _Enter_ to get the next page
of results).

Try looking for a particular setting:
```
SELECT value FROM settings where name = 'num_tokens';
```{{execute T2}}

Is this node configured with virtual nodes (vnodes)? If so, how many are there?

Can you find out what _data type_ the `value` column is? How?
Do you have an explanation for this choice of data type?
