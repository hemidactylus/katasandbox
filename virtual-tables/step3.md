Virtual tables, and their containing keyspaces, impose several restrictions
on the allowed operations. Let's try a few modifications on table
`system_views.settings`.

Can you add a column to a virtual table?

```
ALTER TABLE settings ADD comment TEXT;
```{{execute T2}}

Can you upsert a new row to a virtual table?
```
INSERT INTO settings (name , value ) VALUES ( 'MaxNumberOfGlorxes', '137');
```{{execute T2}}

Can you clear the contents of a virtual table?
```
TRUNCATE settings ;
```{{execute T2}}

Can you create an index? (or, for that matter, a materialized view)
```
CREATE INDEX ON settings (value) ;
```{{execute T2}}

Virtual tables can be queried with the same syntax as regular tables.
Suppose we want to list (Boolean) settings that are "true".
```
SELECT name FROM settings WHERE value='true';
```{{execute T2}}

The query above, however, would fail with a message about _data filtering_
(which is another way of telling you to avoid full-cluster scans).
The fact is, virtual tables are **not** actually distributed: it is then
perfectly fine to add the `ALLOW FILTERING` clause to such a query
(indeed, this is one of the very few cases it makes sense to):

```
SELECT name FROM settings WHERE value='true' ALLOW FILTERING ;
```{{execute T2}}

This observation will come handy in next step.