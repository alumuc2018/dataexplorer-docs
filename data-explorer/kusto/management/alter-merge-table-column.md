---
title: .alter-merge column-docstrings - Azure Data Explorer
description: Learn how to use the .alter-merge column-docstrings command to set the `DocString` property for one or more columns in a specified table.
ms.reviewer: orspodek
ms.topic: reference
ms.date: 04/20/2023
---
# .alter-merge column-docstrings

Sets the `DocString` property for one or more columns of a specified table. Columns not explicitly defined will keep any previous definition for this property, if it exists.

## Permissions

You must have at least [Table Admin](access-control/role-based-access-control.md) permissions to run this command.

## Syntax

`.alter-merge` `table` *TableName* `column-docstrings` `(` *Col1* `:` *DocString1* [`,` *Col2* `:` *DocString2*]... `)`

## Parameters

|Name|Type|Required|Description|
|--|--|--|--|
|*TableName*|string|&check;|The name of the table on which the operation is performed.|
|*Col*|string|&check;|The column on which the operation is performed.|
| *DocString*|string|&check;|Free text that you can attach to a table/function/column to describe the entity. This string is presented in various UX settings next to the entity names.|

## Example

```kusto
.alter-merge table Table1 column-docstrings (Column1:"DocString1", Column2:"DocString2")
```
