# OpenDaylight Cluster Application and Console

ODL Application (including GUI) to manage/view stats and metrics specific to the operation of a cluster of ODL Nodes.

## Team Members
- Daniel Chai
- Jan Medved
- Chris Metz
- Daniel Malachovsky
- Tyler Levine

## Overview - Metz

- add text on requirement/solution
- add text/picture on architecture
- include components (database, tsdr, stats reflector, collector)
- general flow of data

## Stats Reflector
The stats reflector takes in requests to collect stats from a particular ODL node in the cluster. This is needed because the UI cannot directly make requests to every ODL node due to browser security issues. Therefore, the stats reflector runs on one of the ODL nodes (on which the UI also runs), and re-routes requests to the specified ODL node.

The following RPCs are available to use to collect stats (all are POST requests). The header is the same for all:
```
Accept: application/json
Authorization: Basic YWRtaW46YWRtaW4=
Content-Type: application/json
```

### Instrumentation RPC
Returns performance information of all the other RPCs.

Request URL: `http://localhost:8181/restconf/operations/stats-reflector:get-instrumentation`  

Body: 
```
{
  "input": {}
}
```

Output:
```
{
  "output": {
    "requests": [
      {
        "request-name": "GetGeneralRuntimeInfo",
        "number-of-requests": 2,
        "average-latency": "14.5 ms"
      },
      {
        "request-name": "GetShard",
        "number-of-requests": 3,
        "average-latency": "10.333333333333334 ms"
      },
      {
        "request-name": "GetClusterMembers",
        "number-of-requests": 1,
        "average-latency": "28.0 ms"
      },
      {
        "request-name": "GetTsdrStats",
        "number-of-requests": 2,
        "average-latency": "16.5 ms"
      }
    ]
  }
}
```

### Cluster Members RPC
Returns cluster members, their IP addresses, and local shards.

Request URL: `http://localhost:8181/restconf/operations/stats-reflector:get-cluster-members`  

Body: 
```
{
  "input": {}
}
```

Output:
```
{
  "output": {
    "cluster-members": [
      {
        "member-name": "member-2",
        "member-address": "172.17.0.3",
        "local-shards": [
          "member-2-shard-toaster-operational",
          "member-2-shard-topology-operational",
          "member-2-shard-inventory-operational",
          "member-2-shard-default-operational",
          "member-2-shard-entity-ownership-operational"
        ],
        "voter": true
      },
      {
        "member-name": "member-3",
        "member-address": "172.17.0.4",
        "local-shards": [
          "member-3-shard-toaster-operational",
          "member-3-shard-inventory-operational",
          "member-3-shard-default-operational",
          "member-3-shard-topology-operational",
          "member-3-shard-entity-ownership-operational"
        ],
        "voter": true
      },
      {
        "member-name": "member-1",
        "member-address": "172.17.0.2",
        "local-shards": [
          "member-1-shard-topology-operational",
          "member-1-shard-toaster-operational",
          "member-1-shard-default-operational",
          "member-1-shard-entity-ownership-operational",
          "member-1-shard-inventory-operational"
        ],
        "voter": true
      }
    ]
  }
}
```

### TSDR Stats RPC
Returns TSDR stats for particular cluster member. Note: TSDR collects stats every 5 seconds. A request to the stats reflector returns all new data points since the last request was made.

Request URL: `http://localhost:8181/restconf/operations/stats-reflector:get-stats`  

Body: 
```
{
  "input": {
    "cluster-member": "172.17.0.2",
    "data-category": "EXTERNAL",
    "metric-name": "Heap:Memory:Usage",
    "node-id": "Controller:172.17.0.2"
  }
}
```

Output:
```
{
  "output": {
    "data-points": [
      {
        "metric-name": "Heap:Memory:Usage",
        "metric-value": "684140536",
        "time-stamp": "Tue Aug 23 17:36:03 UTC 2016",
        "data-category": "EXTERNAL",
        "node-id": "Controller:172.17.0.2"
      }
    ]
  }
}
```

### GeneralRuntimeInfo MBean RPC
Returns GeneralRuntimeInfo MBean for particular cluster member.

Request URL: `http://localhost:8181/restconf/operations/stats-reflector:get-general-runtime-info-mbean`  

Body: 
```
{
  "input": {
    "member-address": "172.17.0.2",
    "status": "operational"
  }
}
```

Output:
```
{
  "output": {
    "transaction-creation-rate-limit": "100.0",
    "timestamp": "1471973775"
  }
}
```

### Shard MBean RPC
Returns Shard MBean for particular cluster member.

Request URL: `http://localhost:8181/restconf/operations/stats-reflector:get-shard-mbean`  

Body: 
```
{
  "input": {
    "member-address": "172.17.0.2",
    "member-name": "member-1",
    "status": "operational",
    "shard": "inventory"
  }
}
```

Output:
```
{
  "output": {
    "follower-info": [
      {
        "time-since-last-activity": "00:00:00.037",
        "match-index": 0,
        "id": "member-3-shard-inventory-operational"
      },
      {
        "time-since-last-activity": "00:00:00.037",
        "match-index": 0,
        "id": "member-2-shard-inventory-operational"
      }
    ],
    "in-memory-journal-log-size": 1,
    "timestamp": "1471973782",
    "last-applied": 0,
    "last-index": 0,
    "pending-tx-commit-queue-size": 0,
    "commit-index": 0,
    "replicated-to-all-index": -1,
    "raft-state": "Leader"
  }
}
```

## GUI - Metz

- mockups
- screenshots

## Pointers to Additional References

