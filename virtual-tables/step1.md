In this step you will verify that Cassandra has been installed and is running as a service.
Next, you will connect using *cqlsh* and create a keyspace and table.

During startup, this scenario uses *apt-get* to install and start a single Cassandra node running as a service.
This process may take a few minutes. Wait until you see `Cassandra has started!` before you continue.

Once Cassandra has started, click to verify the cluster status with *nodetool*.
```
nodetool status
```{{execute}}

---
<p>
<span style="color:teal">**Status:**</span> 
Look at the first two characters of the status. 
Each character has an individual meaning. 
The sequence `UN` means the node's status is `Up` and state is `Normal`.
</p>
---
