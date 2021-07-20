_Ok, the heavy task is over and the situation is back to normal._

You could simply rever the `nodetool settimeout read` invocation, but
as an experiment let's try restarting Cassandra on this node and see
if this resets the timeout to the default of 5 seconds:

```
systemctl restart cassandra
```{{execute T1}}

Wait until `nodetool status` reports the node back to UN (=Up, Normal):
```
nodetool status
```{{execute T1}}

Now let's look at the timeout value as read through the `settings` virtual table:
```
SELECT * FROM system_views.settings WHERE name = 'read_request_timeout_in_ms';
```{{execute T2}}

Equivalently, look at the output you get now from
```
nodetool gettimeout read
```{{execute T1}}

Has the setting been reverted to its default?
