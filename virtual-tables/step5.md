Virtual tables are also a great way to access all sorts of settings and
configuration parameters for the Cassandra node you are querying.

Let's turn our attention to the **read request timeout**, a quantity specifying
how long this node will wait before timing out when it's acting as read query
coordinator.

You can look for the setting directly in the `cassandra.yaml` file:
```
grep "read_request_timeout_in_ms:" /etc/cassandra/cassandra.yaml
```{{execute T1}}

Alternatively you can use the corresponding "get*"
operations offered by `nodetool`:
```
nodetool gettimeout read
```{{execute T1}}

With virtual tables, you are now able to find the current value of this timeout
with a SELECT:
```
SELECT * FROM system_views.settings WHERE name = 'read_request_timeout_in_ms';
```{{execute T2}}

In most situations, the default setting (5000 milliseconds)
is perfectly fine; however, there may be exceptions, as we will soon see.

