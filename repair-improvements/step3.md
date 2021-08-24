We will now perform some changes on the data (namely, deleting most rows)
with one of the two nodes turned off: the goal is to artificially create
a mismatch in the data. But beware: we have to also neutralize the
_hinted handoff_ mechanism to actually achieve data misalignment.

### Bringing Node2 down

Let us gracefully bring Cassandra down on Node2 first:
on its admin console, execute the following command and wait for
it to acknowledge successful shutdown:
```
nodetool stopdaemon
```{{execute T5}}

When Node2 is completely offline, it will have a status of `DN` (Down, Normal)
as reported by `nodetool` (run this command on the console of Node1):
```
nodetool status
```{{execute T3}}

### Deleting rows

At this point the cluster has a single node up - it will still be able
to accept writes as long as their consistency level is `ONE`.

First let us check what is the (default) consistency level in `cqlsh`:
```
CONSISTENCY;
```{{execute T4}}

We can then delete from the table all chemical elements that are _not_ a gas.
Fortunately, the delete script has been prepared by you (it is simply a list of
  individual `DELETE` statements). Execute it from within `cqlsh` with
```
SOURCE 'delete_nongases.cql';
```{{execute T4}}

(Alternatively, you could run it from a system shell with
  `cqlsh $HOST1_IP -f delete_nongases.cql`.)

Which elements are now left on the table, and how many?
```
SELECT * FROM elements;
```{{execute T4}}

### Removing hints

But wait! Cassandra tries hard to maintain data consistency; so,
while the rows were being deleted, Node1 noticed it could not propagate
the tombstones to Node2 and saved them all in a "hints" file, ready for
when Node2 will be reachable again. If we want to really induce a data mismatch,
we have to delete the hints as well.

Let us see the hints file that has just been created on Node1:
```
ls /usr/share/cassandra/data/hints/
```{{execute T3}}

And now let us remove it:
```
rm /usr/share/cassandra/data/hints/*.hints
```{{execute T3}}

**Important note:** We are intentionally engineering a disruption in
SSTable consistency between the two nodes for demonstration purposes.
Manually tinkering with the contents of the data directories, and in particular
deleting files contained therein, is a very unwise action on a production
cluster (unless one knows very well what they are doing). DO NOT DO THIS
ON PRODUCTION as permanent data loss may ensue!

### Restarting Node2

Let us bring Node2 up again: in its admin console, launch the Cassandra
executable with
```
/usr/share/cassandra/bin/cassandra -R
```{{execute T5}}

Node startup will take about one minute and will be extensively logged in
the console. Eventually the node will announce it is operational again
in the ring ("_JOINING: Finish joining ring_");
you can also check in the usual way from Node1, looking for
both nodes in status `UN` in the output of:
```
nodetool status
```{{execute T3}}

### Querying data

Are the results for the `elements` table different on the two nodes?
Let us count the rows as seen on the first node:
```
SELECT COUNT(*) FROM elements;
```{{execute T4}}

We would like to run the same query on Node2, but most likely, as a result
of the restart, `cqlsh` has lost its connection: to ensure it is operational,
exit `cqlsh`
```
EXIT;
```{{execute T7}}
and re-launch it:
```
cqlsh $HOST2_IP
```{{execute T7}}

Now we can compare the element count with the results from Node2 for the
_same_ query:
```
USE chemistry;
SELECT COUNT(*) FROM elements;
```{{execute T7}}

### Recap

We have applied some mutations to the data on a table with one node
down (and extra care to prevent other Cassandra self-healing mechanisms).
We could explicitly check that now data consistency is violated, i.e.
the two nodes give different results from the same query. It is time to
perform a repair!