**Now something unexpected happens**:
_a heavy task is planned for the next hours on your Cassandra cluster,
such as a massive data migration. Unfortunately you cannot avoid doing that
in production - and the application cannot be simply shut down!_

_So your company decides that, for a little while, the read timeout will have to
be raised to 15 seconds: users may occasionally experience terrible latency
on some pages, but it is expected that this way there will be no outright
application failures._

You sure don't want to edit `cassandra.yaml` and restart the nodes, so you
decide to change this setting on the fly, with:
```
nodetool settimeout read 15000
```{{execute T1}}

Now, does the `cassandra.yaml` automatically reflect this change?
```
grep "read_request_timeout_in_ms:" /etc/cassandra/cassandra.yaml
```{{execute T1}}

Does `nodetool` itself?
```
nodetool gettimeout read
```{{execute T1}}

Does the virtual-table method give you the newly-set value of 15000?
```
SELECT * FROM system_views.settings WHERE name = 'read_request_timeout_in_ms';
```{{execute T2}}
