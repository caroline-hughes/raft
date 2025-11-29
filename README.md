# Raft-Based Key-Value Store

This is a distributed key-value store built on top of the Raft consensus protocol. It runs as a cluster of replicas and provides fault-tolerant `get/put` operations, ensuring strong consistency under node failures or network instability.

## Overview

-   Each replica runs a process:  
    `./kvstore <UDP port> <my ID> <ID of replica 2> [<ID of replica 3> ...]`
-   The system elects a leader among the replicas. The leader handles all client requests; followers redirect clients to the leader.
-   Write requests (`put`) are appended to the leader’s log and then replicated to a majority of replicas via Raft’s `AppendEntries` RPCs.
-   Read requests (`get`) return the current committed state.

The implementation includes support for:

-   Leader election with randomized timeouts and `RequestVote` RPCs
-   Heartbeat and `AppendEntries` RPCs for log replication and liveness detection
-   Redirection logic: if a follower receives a client request, it directs the client to the current leader
-   Consistent replication across a majority — guaranteeing that committed entries are durable even if some replicas fail

## Usage

```bash
# start 3-node cluster example
./kvstore 9000 1 2 3 &
./kvstore 9001 2 1 3 &
./kvstore 9002 3 1 2 &
```
