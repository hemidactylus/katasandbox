We now want to look at the clients currently connected to this node through CQL.
To do so, let's look at the virtual table `system_views.clients`:

```
SELECT port, connection_stage, driver_name, protocol_version, username FROM clients ;
```{{execute T2}}

Wait a minute ... who are they? It turns out that `cqlsh` uses the Python drivers
and that, internally, it keeps two connections on two different ports
(whose exact number is dynamically determined based on the available ones).
So you are simply looking at the connection between your own `cqlsh` and the node.

Now let's start a Python interpreter shell and connect to the node from there.
Go to the third terminal and type
```
python3
```{{execute T3}}

Now import the Python drivers and use them to connect to the local node
(which is the default, so there's no need to provide IP addresses when
connecting):
```
from dse.cluster import Cluster
cluster = Cluster(protocol_version=4)
session = cluster.connect()
```{{execute T3}}

(Note: the drivers `dse-driver==2.11.1` have been preinstalled in Python for
this scenario).

In the Python REPL, try the following loop - which achieves the same effect
as the query you ran earlier in `cqlsh`:
```
rows = session.execute('SELECT port, connection_stage, driver_name, protocol_version FROM system_views.clients')
for row in rows:
    print(row.port, row.connection_stage, row.driver_name, row.protocol_version)
```{{execute T3}}

Now how many rows are there? Look at the ports used and the protocol versions:
do the latter match the required version specified in the Python connection?

Now suppose you want to make sure all your clients have been upgraded to the
more recent protocol version 5: a good way to check could be running, in `cqlsh`,
the following query:
```
SELECT address, protocol_version, username FROM clients WHERE protocol_version < 5 ALLOW FILTERING ;
```{{execute T2}}

Recall that for virtual tables you don't have to worry about full-cluster scans.