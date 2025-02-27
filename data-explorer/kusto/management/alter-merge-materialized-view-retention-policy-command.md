---
title: .alter-merge materialized-view retention policy command- Azure Data Explorer
description: Learn how to use the .alter-merge materialized-view retention policy command to change the materialized view's retention policy.
ms.reviewer: yonil
ms.topic: reference
ms.date: 04/20/2023
---
# .alter-merge materialized-view retention policy

Changes the materialized-view's [retention policy](retentionpolicy.md). The retention policy controls the mechanism that automatically removes data from tables or materialized views. It is used to remove data whose relevance is age-based.

## Permissions

You must have at least [Database Admin](access-control/role-based-access-control.md) permissions to run this command.

## Syntax

`.alter-merge` `materialized-view` *MaterializedViewName* `policy` `retention` *PolicyParameters*

## Parameters

|Name|Type|Required|Description|
|--|--|--|--|
|*MaterializedViewName*|string|&check;|The name of the materialized view.|
|*PolicyParameters*|string|&check;|One or more policy parameters. For more information, see [retention policy](retentionpolicy.md).|

### Example

Sets a retention policy with a 10-day soft-delete period, and disables data recoverability:

```kusto
.alter-merge materialized-view View1 policy retention softdelete = 10d recoverability = disabled
```
