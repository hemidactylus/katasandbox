We are about to bring the cluster to the conditions that warrant a data
repair; but first, we have to make sure all recently-inserted rows, probably
still lingering in memory (in the memtables), are flushed to disk in the
form of SSTables.

### Flushing data

Each time a table is created, it gets an ID that is used, among other things,
also in the name of the directory containing the corresponding data.
To identify the full name of the data directory for `elements`, look at
the result of this command on node 1:
```
ls /usr/share/cassandra/data/data/chemistry/
```{{Execute T3}}
The output will be something similar to
`elements-8f40e960043011ec8f376feadc8291b4`.

Since the rows we just inserted are just a few, probably the data directory
is still empty: this can be verified with (**NOTE**: copy and paste the
  actual ID for the command to succeed):
```
ls /usr/share/cassandra/data/data/chemistry/elements-<TABLE_ID>
```{{Execute T3}}

Now we can force a flush of all insertions to disk, by executing the following
command on both nodes:
```
nodetool flush  # node 1
```{{Execute T3}}

```
nodetool flush  # node 2
```{{Execute T6}}

This time, inspection of the data directory will confirm that at least
one SSTable has been created: remember that you can use the up-arrow key to
bring up a command you already typed, and re-execute the following:
```
ls /usr/share/cassandra/data/data/chemistry/elements-<TABLE_ID>
```{{Execute T3}}
You will now see the SSTable files.
Notice in particular a file named `[...]-Data.db`, where the actual contents
  of the table are stored.

### Examining SSTables

You can examine the repair status of this brand new SSTable with the following
command (**NOTE**: again, replace the actual table ID and the SSTable
  file name for the command to work):
```
sstablemetadata /usr/share/cassandra/data/data/chemistry/elements-<TABLE_ID>/<SSTABLE_ROOT_NAME>-Data.db
```{{Execute T3}}

Look for the repair information in the output: there should be two lines such as:
```
...
Repaired at: 0
Pending repair: --
...
```

meaning that the table has never been repaired before, and is not currently
in the pending-repair pool of any running repair respectively.