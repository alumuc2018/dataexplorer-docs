---
title: 'Share queries from Azure Data Explorer web UI'
description: In this guide, you'll learn how to share queries from the Azure Data Explorer web UI.
ms.topic: how-to
ms.date: 03/22/2023
---

# Share queries from Azure Data Explorer web UI

This article describes the process of sharing queries from the [Azure Data Explorer web UI](https://dataexplorer.azure.com/home). By the end of this article, you'll know how to share a query link, share the query results, or even pin it to a dashboard.

To learn how to run queries, see [Quickstart: Query data in the Azure Data Explorer web UI](web-query-data.md).

## Prerequisites

* A Microsoft account or an Azure Active Directory user identity. An Azure subscription isn't required.
* Access to an Azure Data Explorer cluster and database. You may use the publicly available [**help** cluster](https://dataexplorer.azure.com/help) or [create a cluster and database](create-cluster-database-portal.md).

## Share options

The following table outlines the many options for how to share a query.

|Action|Description|
|--|--|
|[Pin to dashboard](#pin-to-dashboard)|Display the query in an [Azure Data Explorer dashboard](azure-data-explorer-dashboards.md).|
|[Link to clipboard](#link-to-clipboard)|Copy a link that can be used to run the query.|
|[Link, query to clipboard](#link-query-to-clipboard)|Copy a link that can be used to run the query and the text of the query.|
|[Link, query, results to clipboard](#link-query-results-to-clipboard)|Copy a link that can be used to run the query, the text of the query, and the results of the query.|
|[Download](#download)|Download a KQL file of the query.|
|[Export to CSV](#export-to-csv)|Download a CSV of the query results.|

## Pin to dashboard

To pin a query to a dashboard for continuous monitoring, follow these steps:

1. In the query window, select the query that you want to pin.

1. Select **Pin to dashboard**.

    :::image type="content" source="media/web-share-query/pin-to-dashboard.png" alt-text="Screenshot of the pin to dashboard button.":::

1. In the **Pin to dashboard** pane:
    1. Provide a **Tile name**.
    1. Select **Use existing** or **Create new**.
    1. Provide the **Dashboard name**.
    1. Select the **View dashboard after creation** checkbox (if it's a new dashboard).
    1. Select **Pin**.

> [!NOTE]
> **Pin to dashboard** only pins the selected query. To create the dashboard data source and translate render commands to a visual in the dashboard, the relevant database must be selected in the database list.

## Link to clipboard

To copy a link to share with others, follow these steps:

1. In the query window, select the query that you want to share.

1. Under **Copy**, select **Link to clipboard**.

    :::image type="content" source="media/web-share-query/link-to-clipboard.png" alt-text="Screenshot of the link to clipboard button.":::

1. Paste the link into a new browser window to run the query.

> [!NOTE]
> The user must have access to the cluster to run the query.

## Link, query to clipboard

To copy a link to share with others and the text of the query, follow these steps:

1. In the query window, select the query that you want to share.

1. Under **Copy**, select **Link, query to clipboard**.

    :::image type="content" source="media/web-share-query/link-query-to-clipboard.png" alt-text="Screenshot of the link, query to clipboard button.":::

1. Paste to share. The output lists the link followed by the query text.

## Link, query, results to clipboard

To copy a link to share with others, the text of the query, and the results of the query, follow these steps:

1. In the query window, select the query that you want to share.

1. Under **Copy**, select **Link, query, results to clipboard**.

    :::image type="content" source="media/web-share-query/link-query-results-to-clipboard.png" alt-text="Screenshot of the link, query, results to clipboard button.":::

1. Paste to share. The output lists the link, query text, and query results.

## Download

To download a KQL file of the query, follow these steps:

1. In the query window, select the query that you want to download.

1. Select **Export** > **Download**.

    :::image type="content" source="media/web-share-query/download.png" alt-text="Screenshot of the download button.":::

## Export to CSV

To export the query results to a CSV file, follow these steps:

1. In the query window, select the query that you want to export.

1. Select **Export** > **Export to CSV**.

    :::image type="content" source="media/web-share-query/export-to-csv.png" alt-text="Screenshot of the export to CSV button.":::
