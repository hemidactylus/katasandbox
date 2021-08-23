This scenario requires a two-node cluster, that has been created for you.
Wait until the terminal prints a message such as _"Cassandra Cluster with nodes
`<IP_node_1>` and `<IP_node_2>` has started"_ before proceeding.

Verify the cluster is up and running with
```
nodetool status
```{{execute T1}}
The output should list _two_ nodes, each in the `UN` (Up, Normal) status.

### Initialization

Let us initialize all terminals of the scenario by running the following:
```
echo Initializing terminal 2
ssh $HOST1_IP
```{{execute T2}}

```
echo Initializing terminal 3
ssh $HOST1_IP
```{{execute T3}}

```
echo Initializing terminal 4
ssh $HOST1_IP
```{{execute T4}}

```
echo Initializing terminal 5
ssh $HOST2_IP
```{{execute T5}}

```
echo Initializing terminal 6
ssh $HOST2_IP
```{{execute T6}}

```
echo Initializing terminal 7
ssh $HOST2_IP
```{{execute T7}}


### Schema creation

Open the CQL Shell on both nodes:

```
cqlsh
```{{execute T3}}


```
cqlsh
```{{execute T6}}

The following commands can be run on either CQL shells - we will work
on node 1. First let us create a keyspace with replication factor of two,
i.e. such that _all rows_ be replicated on _both nodes_:
```
CREATE KEYSPACE chemistry WITH REPLICATION = {'class': 'SimpleStrategy', 'replication_factor': 2};
```{{execute T3}}

Set the newly-created keyspace as the default one for subsequent commands:
```
USE chemistry;
```{{execute T3}}

Finally create a table for storing the periodic table of elements:
```
CREATE TABLE elements (
    symbol TEXT PRIMARY KEY,
    name TEXT,
    atomic_mass DOUBLE,
    atomic_number INT
);
```{{execute T3}}

### Data insertion

A CSV file with the first hundred or so elements and their properties is
provided for use; to load its contents into the `elements` table, run the
following CQL command:
```
COPY elements FROM 'elements.csv' WITH HEADER=TRUE;
```{{execute T3}}

To verify the insertion has succeeded, let's try to query the table (from
  node 2, why not?). Let's look at some of the rows,
```
USE chemistry;
SELECT * FROM elements LIMIT 10;
```{{execute T6}}

and then count how many are there:

```
SELECT COUNT(*) FROM elements;
```{{execute T6}}
