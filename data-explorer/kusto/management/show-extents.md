---
title: .show extents - Azure Data Explorer
description: This article describes the show extents command in Azure Data Explorer.
ms.reviewer: orspodek
ms.topic: reference
ms.date: 05/08/2023
---

# .show extents

> [!NOTE]
> Data shards are called **extents**, and all commands use "extent" or "extents" as a synonym.
> For more information on extents, see [Extents (Data Shards) Overview](extents-overview.md).

The types of `.show extents` commands are as follows:

* Show some or all extents for a specific [table scope](#table-scope)
* Show some or all extents for a specific [database scope](#database-scope)
* Show some or all extents for the entire [cluster](#cluster-scope)

> [!NOTE]
> The `.show extents` command may consume a lot of resources if it runs on a scope
> (such as a database or a cluster) with many extents. We recommended
> using the command variant at the lowest possible scope. Table-scope
> is preferable over database-scope, and database-scope over cluster-scope. The
> command variant that includes filtering extents is preferable to filtering the results
> of the command using another query.

## Permissions

To see extents on the cluster, you must have AllDatabasesMonitor permissions.

To see extents on a database, you must have Database User, Database Viewer, or Database Monitor permissions.

For more information, see [role-based access control](access-control/role-based-access-control.md).

## Table scope

### Syntax

Shows information about extents (data shards) that are present in the specified tables. The database is taken from the command's context.
If `hot` is specified, shows only extents that are expected to be in the hot cache.

`.show` `table` *TableName* `extents` [`(` *ExtentId* [`,` ...]`)`] [`hot`] [`where` `tags` (`has`|`contains`|`!has`|`!contains`) *TagName* [`and` `tags` (`has`|`contains`|`!has`|`!contains`) *TagName* [`,` ...]]]

`.show` `tables` `(`*TableName* [`,` ...]`)` `extents` [`(` *ExtentId* [`,` ...]`)`] [`hot`] [`where` `tags` (`has`|`contains`|`!has`|`!contains`) *TagName* [`and` `tags` (`has`|`contains`|`!has`|`!contains`) *TagName* [`,` ...]]]

### Parameters

|Name|Type|Required|Description|
|--|--|--|--|
|*TableName*|string|&check;|The name of the table.|
|*ExtentId*|string||The ID of the extent to show.|
|*Tag*|string||The name of a tag to filter by as specified.|

### Recommendations

* Using built-in filtering capabilities in the command is preferred over adding
  a query-based filter (such as adding `| where DatabaseName == '...'` and `TableName == '...'`).
* If the optional list of extent IDs is provided, the returned data set is limited to those extents only.
    * This method is much faster than filtering (adding `| where ExtentId in(...)`) to the results of "bare" commands.
* If `tags` filters are specified:
    * The returned list is limited to those extents whose tags collection obeys *all* of the provided tags filters.
    * This method is much faster than filtering (adding `| where Tags has '...' and Tags contains '...'` to) the results of "bare" commands.
    * `has` filters are equality filters. Extents that aren't tagged with either of the specified tags will be filtered out.
    * `!has` filters are equality negative filters. Extents that are tagged with either of the specified tags will be filtered out.
    * `contains` filters are case-insensitive substring filters. Extents that don't have the specified strings as a substring of any of their tags will be filtered out.
    * `!contains` filters are case-insensitive substring negative filters. Extents that have the specified strings as a substring of any of their tags will be filtered out.

## Database scope

Shows information about extents (data shards) that are present in the specified database.
If `hot` is specified - shows only extents that expected to be in the hot cache.

### Syntax

`.show` `database` *DatabaseName* `extents` [`(` *ExtentId* [`,` ...]`)`] [`hot`] [`where` `tags` (`has`|`contains`|`!has`|`!contains`) *TagName* [`and` `tags` (`has`|`contains`|`!has`|`!contains`) *TagName* [`,` ...]]]

### Parameters

|Name|Type|Required|Description|
|--|--|--|--|
|*DatabaseName*|string|&check;|The name of the database.|
|*ExtentId*|string||The ID of the extent to show.|
|*Tag*|string||The name of a tag to filter by as specified.|

## Cluster scope

`.show` `cluster` `extents` [`hot`]

Shows information about extents (data shards) that are present in the cluster.
If `hot` is specified - shows only extents that are expected to be in the hot cache.

## Returns

|Output parameter |Type |Description |
|---|---|---|
|ExtentId |Guid |ID of the extent
|DatabaseName |String |Database that the extent belongs to
|TableName |String |Table that the extents belong to
|MaxCreatedOn |DateTime |Date-time when the extent was created. For a merged extent, the maximum of creation times among source extents
|OriginalSize |Double |Original size in bytes of the extent data
|ExtentSize |Double |Size of the extent in memory (compressed + index)
|CompressedSize |Double |Compressed size of the extent data in memory
|IndexSize |Double |Index size of the extent data
|Blocks |Long |Number of data blocks in the extent
|Segments |Long |Number of data segments in the extent
|ExtentContainerId |String | ID of the extent container the extent is in
|RowCount |Long |Number of rows in the extent
|MinCreatedOn |DateTime |Date-time when the extent was created. For a merged extent, the minimum of creation times among the source extents
|Tags|String|Tags, if any, defined for the extent
|Kind|String|The kind of the storage engine that created the extent ("StorageV2" or "StorageV3")
|DeletedRowCount|Long|Number of deleted rows in the extent

## Examples

### Tagged extent

Extent `E` in table `T` is tagged with tags `aaa`, `BBB`, and `ccc`.

* This query will return `E`:
    
   ```kusto
    .show table T extents where tags has 'aaa' and tags contains 'bb'
   ```
   
* This query won't return `E` since it isn't tagged with `aa`:
    
   ```kusto
    .show table T extents where tags has 'aa' and tags contains 'bb'
   ```
    
* This query will return `E`:
    
   ```kusto
    .show table T extents where tags contains 'aaa' and tags contains 'bb' 
   ```

### Show volume of extents created

Show volume of extents being created per hour in a specific database

```kusto 
.show database MyDatabase extents | summarize count(ExtentId) by MaxCreatedOn bin=time(1h) | render timechart  
```

### Show volume of data arriving by table per hour

```kusto 
.show database MyDatabase extents  
| summarize sum(OriginalSize) by TableName, MaxCreatedOn bin=time(1h)  
| render timechart
```

### Show data size distribution by table

```kusto 
.show database MyDatabase extents | summarize sum(OriginalSize) by TableName
```

### Show all extents in the database named 'GamesDB'

```kusto 
.show database GamesDB extents
```

### Show all extents in the table named 'Games'

```kusto 
.show table Games extents
```

### Show all extents in specific tables

Show all extents in the tables named 'TaggingGames1' and 'TaggingGames2', tagged with both 'tag1' and 'tag2'

```kusto 
.show tables (TaggingGames1,TaggingGames2) extents where tags has 'tag1' and tags has 'tag2'
```
