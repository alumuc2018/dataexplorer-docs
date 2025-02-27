---
title: .replace extents - Azure Data Explorer
description: This article describes the replace extents command in Azure Data Explorer.
ms.reviewer: orspodek
ms.topic: reference
ms.date: 04/30/2023
---
# .replace extents

This command runs in the context of a specific database.
It moves the specified extents from their source tables to the destination table,
and then drops the specified extents from the destination table.
All of the drop and move operations are done in a single transaction.

> [!NOTE]
> Data shards are called **extents**, and all commands use "extent" or "extents" as a synonym.
> For more information on extents, see [Extents (data shards) overview](extents-overview.md).

## Permissions

You must have at least [Table Admin](../management/access-control/role-based-access-control.md) permissions for the source and destination tables.

## Syntax

`.replace` [`async`] `extents` `in` `table` *DestinationTableName* `with` `(` `extentCreatedOnFrom` `=` *FromDate*`,` `extentCreatedOnTo` `=`*ToDate* `)` `<|`
`{`*ExtentsToDropQuery*`},{`*ExtentsToMoveQuery*`}`

## Parameters

|Name|Type|Required|Description|
|--|--|--|--|
|`async`|string||If specified, the command runs asynchronously.|
|*DestinationTableName*|string|&check;|The name of the table to which to move the extents.|
|*FromDate*|datetime||The query window start date.|
|*ToDate*|datetime||The query window end date.|
|*ExtentsToDropQuery*|string|&check;|The results of this query specify the extent IDs that should be dropped from the destination table. Should return a recordset with a column called "ExtentId".|
|*ExtentsToMoveQuery*|string|&check;|The results of this query specify the extent IDs in the source tables that should be moved to the destination table. Should return a recordset with a column called "ExtentId".|

> [!NOTE]
> For better performance, set extentCreatedOnFrom and extentCreatedOnTo parameters to the smallest possible range.

## Restrictions

* Both source and destination tables must be in the context database.
* All extents specified by the *ExtentsToDropQuery* are expected to belong to the destination table.
* All columns in the source tables are expected to exist in the destination table with the same name and data type.

## Returns

When the command is run synchronously, a table with the following schema is returned.

| Output parameter | Type | Description |
|--|--|--|
| OriginalExtentId | string | A unique identifier (GUID) for the original extent in the source table that has been moved to the destination table, or the extent in the destination table that has been dropped. |
| ResultExtentId | string | A unique identifier (GUID) for the result extent that has been moved from the source table to the destination table. Empty, if the extent was dropped from the destination table. Upon failure: "Failed". |
| Details | string | Includes the failure details if the operation fails. |

When the command is run asynchronously, an operation ID (GUID) is returned. Monitor the operation's status with the [.show operations](operations.md#show-operations) command, and retrieve the results of a successful execution with the [.show operation details](operations.md#show-operation-details) command.

> [!NOTE]
> The command will fail if extents returned by the *ExtentsToDropQuery* query don't exist in the destination table. This may happen if the extents were merged before the replace command was executed.
> To make sure the command fails on missing extents, check that the query returns the expected ExtentIds. Example #1 below will fail if the extent to drop doesn't exist in table *MyOtherTable*. Example #2, however, will succeed even though the extent to drop doesn't exist, since the query to drop didn't return any extent IDs.

## Examples

### Move all extents in a specified creation time range from two tables 

Move all extents from two specific tables (`MyTable1`, `MyTable2`) in a specified creation time range to table `MyOtherTable`, and drop all extents in `MyOtherTable` tagged with `drop-by:MyTag`:

```kusto
.replace extents in table MyOtherTable with (extentCreatedOnFrom=datetime(2023-03-10), extentCreatedOnTo=datetime(2023-03-12)) <|
    {
        .show table MyOtherTable extents where tags has 'drop-by:MyTag'
    },
    {
        .show tables (MyTable1,MyTable2) extents
    }
```

#### Sample output

|OriginalExtentId |ResultExtentId |Details
|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df| 

### Move all extents in a specified creation time range from one table to another, drop specific extent

Move all extents in a specified creation time range from one specific table (`MyTable1`) to table `MyOtherTable`, and drop a specific extent in `MyOtherTable`, by its ID:

```kusto
.replace extents in table MyOtherTable with (extentCreatedOnFrom=datetime(2023-03-10), extentCreatedOnTo=datetime(2023-03-12)) <|
    {
        print ExtentId = "2cca5844-8f0d-454e-bdad-299e978be5df"
    },
    {
        .show table MyTable1 extents 
    }
```

```kusto
.replace extents in table MyOtherTable with (extentCreatedOnFrom=datetime(2023-03-10), extentCreatedOnTo=datetime(2023-03-12)) <|
    {
        .show table MyOtherTable extents
        | where ExtentId == guid(2cca5844-8f0d-454e-bdad-299e978be5df) 
    },
    {
        .show table MyTable1 extents 
    }
```

### Implement an idempotent logic

Implement an idempotent logic so that Kusto drops extents from table `t_dest` only if there are extents to move from table `t_source` to table `t_dest`:

```kusto
.replace async extents in table t_dest with (extentCreatedOnFrom=datetime(2023-03-10), extentCreatedOnTo=datetime(2023-03-12)) <|
{
    let any_extents_to_move = toscalar( 
        t_source
        | where extent_tags() has 'drop-by:blue'
        | summarize count() > 0
    );
    let extents_to_drop =
        t_dest
        | where any_extents_to_move and extent_tags() has 'drop-by:blue'
        | summarize by ExtentId = extent_id()
    ;
    extents_to_drop
},
{
    let extents_to_move = 
        t_source
        | where extent_tags() has 'drop-by:blue'
        | summarize by ExtentId = extent_id()
    ;
    extents_to_move
}
```
