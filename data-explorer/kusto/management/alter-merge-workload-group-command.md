---
title: .alter-merge workload group command - Azure Data Explorer
description: Learn how to use the alter-merge workload group command to alter a workload group.
ms.reviewer: yonil
ms.topic: reference
ms.date: 04/20/2023

---
# .alter-merge workload_group

Alter a workload group.

## Permissions

You must have [Cluster AllDatabasesAdmin](access-control/role-based-access-control.md) permissions to run this command.

## Syntax

`.alter-merge` `workload_group` *WorkloadGroupName* *SerializedPolicyObject*

## Parameters

| Name                             | Type   | Required | Description                                                                                                                                                                                                                       |
|----------------------------------|--------|----------|-------------------------------------------------------------------------------------------|
| *WorkloadGroupName*              | string | &check;  | Name of the workload group. Can be specified with bracket notation ['WorkLoadGroupName']. |
| *SerializedPolicyObject*         | string | &check;  | JSON representation of the policy. `*`                                                    |

`*` The following policies apply to workload groups:

* [request classification](request-classification-policy.md)
* [request limits](request-limits-policy.md)
* [request rate limit](request-rate-limit-policy.md)
* [request rate limits enforcement](request-rate-limits-enforcement-policy.md).

## Returns

The command returns one row showing the details of the workload group.

Following is the schema of the output returned:

| Name              | Type   | Description                        |
|-------------------|--------|------------------------------------|
| WorkloadGroupName | string | Name of the workload group.        |
| WorkloadGroup     | string | JSON representation of the policy. |

## Examples

### Alter specific limits in the request limits policy

Alter specific limits in the request limits policy of the `default` workload group, while keeping previously defined limits unchanged.

~~~kusto
.alter-merge workload_group default ```
{
  "RequestLimitsPolicy": {
    "DataScope": {
      "IsRelaxable": false,
      "Value": "HotCache"
    },
    "MaxExecutionTime": {
      "IsRelaxable": false,
      "Value": "00:01:00"
    }
  }
} ```
~~~

**Output:**

| WorkloadGroupName | WorkloadGroup                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| default           | {"RequestLimitsPolicy":{"DataScope":{"IsRelaxable":false,"Value":"HotCache"},"MaxMemoryPerQueryPerNode":{"IsRelaxable":true,"Value":8589699072},"MaxMemoryPerIterator":{"IsRelaxable":true,"Value":5368709120},"MaxFanoutThreadsPercentage":{"IsRelaxable":true,"Value":100},"MaxFanoutNodesPercentage":{"IsRelaxable":true,"Value":100},"MaxResultRecords":{"IsRelaxable":true,"Value":500000},"MaxResultBytes":{"IsRelaxable":true,"Value":67108864},"MaxExecutionTime":{"IsRelaxable":false,"Value":"00:01:00"}},"RequestRateLimitPolicies":[{"IsEnabled":true,"Scope":"WorkloadGroup","LimitKind":"ConcurrentRequests","Properties":{"MaxConcurrentRequests":100}}],"RequestRateLimitsEnforcementPolicy":{"QueriesEnforcementLevel":"QueryHead","CommandsEnforcementLevel":"Database"}} |

### Alter the request rate limit policies

Alter the request rate limit policies of the `default` workload group, while keeping all of its other policies unchanged.

~~~kusto
.alter-merge workload_group default ```
{
  "RequestRateLimitPolicies": [
    {
      "IsEnabled": true,
      "Scope": "WorkloadGroup",
      "LimitKind": "ConcurrentRequests",
      "Properties": {
        "MaxConcurrentRequests": 100
      }
    }
  ]
} ```
~~~

**Output:**

| WorkloadGroupName | WorkloadGroup                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| default           | {"RequestLimitsPolicy":{"DataScope":{"IsRelaxable":false,"Value":"HotCache"},"MaxMemoryPerQueryPerNode":{"IsRelaxable":true,"Value":8589699072},"MaxMemoryPerIterator":{"IsRelaxable":true,"Value":5368709120},"MaxFanoutThreadsPercentage":{"IsRelaxable":true,"Value":100},"MaxFanoutNodesPercentage":{"IsRelaxable":true,"Value":100},"MaxResultRecords":{"IsRelaxable":true,"Value":500000},"MaxResultBytes":{"IsRelaxable":true,"Value":67108864},"MaxExecutionTime":{"IsRelaxable":false,"Value":"00:01:00"}},"RequestRateLimitPolicies":[{"IsEnabled":true,"Scope":"WorkloadGroup","LimitKind":"ConcurrentRequests","Properties":{"MaxConcurrentRequests":100}}],"RequestRateLimitsEnforcementPolicy":{"QueriesEnforcementLevel":"QueryHead","CommandsEnforcementLevel":"Database"}} |

### Alter the request queuing policy

Turn on request queuing for the `default` workload group, while keeping its request limits policy and request rate limit policies unchanged.

~~~kusto
.alter-merge workload_group default ```
{
  "RequestQueuingPolicy": {
      "IsEnabled": true
  }
} ```
~~~

**Output:**

| WorkloadGroupName | WorkloadGroup                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| default           | {"RequestLimitsPolicy":{"DataScope":{"IsRelaxable":false,"Value":"HotCache"},"MaxMemoryPerQueryPerNode":{"IsRelaxable":true,"Value":8589699072},"MaxMemoryPerIterator":{"IsRelaxable":true,"Value":5368709120},"MaxFanoutThreadsPercentage":{"IsRelaxable":true,"Value":100},"MaxFanoutNodesPercentage":{"IsRelaxable":true,"Value":100},"MaxResultRecords":{"IsRelaxable":true,"Value":500000},"MaxResultBytes":{"IsRelaxable":true,"Value":67108864},"MaxExecutionTime":{"IsRelaxable":false,"Value":"00:01:00"}},"RequestRateLimitPolicies":[{"IsEnabled":true,"Scope":"WorkloadGroup","LimitKind":"ConcurrentRequests","Properties":{"MaxConcurrentRequests":100}}],"RequestQueuingPolicy":{"IsEnabled":true},"RequestRateLimitsEnforcementPolicy":{"QueriesEnforcementLevel":\n"QueryHead","CommandsEnforcementLevel":"Database"}} |

### Alter the request rate limits enforcement policy

Turn on request rate limits enforcement policy for the `default` workload group, while keeping all of its other policies unchanged.

~~~kusto
.alter-merge workload_group default ```
{
  "RequestRateLimitsEnforcementpolicy": {
      "QueriesEnforcementLevel": "QueryHead",
      "CommandsEnforcementLevel": "Cluster"
  }
} ```
~~~

**Output:**

| WorkloadGroupName | WorkloadGroup                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|-------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| default           | {"RequestLimitsPolicy":{"DataScope":{"IsRelaxable":false,"Value":"HotCache"},"MaxMemoryPerQueryPerNode":{"IsRelaxable":true,"Value":8589699072},"MaxMemoryPerIterator":{"IsRelaxable":true,"Value":5368709120},"MaxFanoutThreadsPercentage":{"IsRelaxable":true,"Value":100},"MaxFanoutNodesPercentage":{"IsRelaxable":true,"Value":100},"MaxResultRecords":{"IsRelaxable":true,"Value":500000},"MaxResultBytes":{"IsRelaxable":true,"Value":67108864},"MaxExecutionTime":{"IsRelaxable":false,"Value":"00:01:00"}},"RequestRateLimitPolicies":[{"IsEnabled":true,"Scope":"WorkloadGroup","LimitKind":"ConcurrentRequests","Properties":{"MaxConcurrentRequests":100}}],"RequestQueuingPolicy":{"IsEnabled":true},"RequestRateLimitsEnforcementPolicy":{"QueriesEnforcementLevel":"QueryHead","CommandsEnforcementLevel":"Cluster"}} |

### Alter the query consistency policy

Specify the applicable option for the query consistency model.

~~~kusto
.alter-merge workload_group default ```
{
  "QueryConsistencyPolicy": {
     "QueryConsistency": {
        "IsRelaxable": true,
        "Value": "Weak"
     }
  }
} ```
~~~

**Output:**

| WorkloadGroupName | WorkloadGroup                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
|-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| default           | {"RequestLimitsPolicy":{"DataScope":{"IsRelaxable":false,"Value":"HotCache"},"MaxMemoryPerQueryPerNode":{"IsRelaxable":true,"Value":8589699072},"MaxMemoryPerIterator":{"IsRelaxable":true,"Value":5368709120},"MaxFanoutThreadsPercentage":{"IsRelaxable":true,"Value":100},"MaxFanoutNodesPercentage":{"IsRelaxable":true,"Value":100},"MaxResultRecords":{"IsRelaxable":true,"Value":500000},"MaxResultBytes":{"IsRelaxable":true,"Value":67108864},"MaxExecutionTime":{"IsRelaxable":false,"Value":"00:01:00"}},"RequestRateLimitPolicies":[{"IsEnabled":true,"Scope":"WorkloadGroup","LimitKind":"ConcurrentRequests","Properties":{"MaxConcurrentRequests":100}}],"RequestQueuingPolicy":{"IsEnabled":true},"RequestRateLimitsEnforcementPolicy":{"QueriesEnforcementLevel":"QueryHead","CommandsEnforcementLevel":"Cluster"},"QueryConsistencyPolicy":{"QueryConsistency":{"IsRelaxable":true,"Value":"Weak"}}} |
