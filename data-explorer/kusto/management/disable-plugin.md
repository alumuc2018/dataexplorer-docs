---
title: Disable plugin commands- Azure Data Explorer
description: This article describes plugins management command .disable plugin in Azure Data Explorer.
ms.reviewer: alexans
ms.topic: reference
ms.date: 04/24/2023
---
# .disable plugin

Disables a plugin.

## Permissions

You must have [AllDatabasesAdmin](access-control/role-based-access-control.md) permissions to run this command.

## Syntax

`.disable` `plugin` *PluginName*

## Parameters

|Name|Type|Required|Description|
|--|--|--|--|
|*PluginName*|string|&check;|The name of the plugin to disable.|

## Example

The following command disables the autocluster plugin on the cluster.

```kusto
.disable plugin autocluster
```

## See also

* [`.show plugins`](show-plugins.md)
* [`.enable plugin`](enable-plugin.md)
