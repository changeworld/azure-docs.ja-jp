---
title: Azure Synapse Data Explorer の Event Grid データ接続 (プレビュー)
description: この記事では、Event Grid からデータを Azure Synapse Data Explorer に取り込む (読み込む) 方法の概要を説明します。
ms.topic: how-to
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 14b5129a29e7d09e86ebfd8874654e55be6f1cb6
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132715836"
---
# <a name="event-grid-data-connection-preview"></a>Event Grid データ接続 (プレビュー)

Event Grid インジェストは、Azure ストレージをリッスンし、サブスクライブしたイベントが発生したときに情報をプルするように Azure Data Explorer を更新するパイプラインです。 Data Explorer では、BLOB 作成または BLOB 名前変更の通知に対する [Azure Event Grid](../../../event-grid/overview.md) サブスクリプションを使用して Azure Storage (Blob Storage と ADLSv2) からの継続的なインジェストが提供され、イベント ハブを介してそれらの通知が Data Explorer にストリーミングされます。

Event Grid のインジェスト パイプラインでは、いくつかの手順が実行されます。 [特定の形式のデータ](#data-format)が取り込まれるターゲット テーブルを Data Explorer に作成します。 次に、Data Explorer で Event Grid データ接続を作成します。 Event Grid データ接続では、データを送信するテーブルやテーブルのマッピングなど、[イベントのルーティング](#events-routing)情報を把握している必要があります。 また、取り込まれるデータ、ターゲット テーブル、およびマッピングを記述する[インジェスト プロパティ](#ingestion-properties)も指定します。 サンプル データを生成し、[BLOB をアップロード](#upload-blobs)するか、[BLOB を名前変更](#rename-blobs)して、接続をテストできます。 インジェスト後、[BLOB を削除](#delete-blobs-using-storage-lifecycle)します。 このプロセスは、[Azure portal](data-explorer-ingest-event-grid-portal.md) を使用して管理することができます。 <!-- , using [one-click ingestion](one-click-ingestion-new-table.md), programmatically with [C#](data-connection-event-grid-csharp.md) or [Python](data-connection-event-grid-python.md), or with the [Azure Resource Manager template](data-connection-event-grid-resource-manager.md). -->

<!-- For general information about data ingestion in Data Explorer, see [Data Explorer data ingestion overview](ingest-data-overview.md). -->

## <a name="data-format"></a>データ形式

- [サポートされる形式](data-explorer-ingest-data-supported-formats.md)を確認してください。
- [サポートされる圧縮](data-explorer-ingest-data-supported-formats.md#supported-data-compression-formats)を確認してください。
    - 元の非圧縮データ サイズは、BLOB メタデータの一部である必要があります。それ以外の場合は、Data Explorer によって推定されます。 ファイルごとのインジェストの非圧縮サイズの制限は 4 GB です。

> [!NOTE]
> Event Grid 通知サブスクリプションは、`BlobStorage`、`StorageV2`、または [Data Lake Storage Gen2](../../../storage/blobs/data-lake-storage-introduction.md) に対して Azure Storage アカウントで設定できます。

## <a name="ingestion-properties"></a>インジェストのプロパティ

BLOB メタデータを使用して、BLOB インジェストの[インジェストのプロパティ](data-explorer-ingest-data-properties.md)を指定できます。
以下のプロパティを設定できます。

[!INCLUDE [ingestion-properties-event-grid](../includes/data-explorer-event-grid-ingestion-properties.md)]

## <a name="events-routing"></a>イベント ルーティング

Data Explorer クラスターへの BLOB ストレージ接続を設定するときに、ターゲット テーブルのプロパティを指定します。

- テーブル名
- データ形式
- mapping

この設定は、データの既定のルーティングです (`static routing` と呼ばれることもあります)。
BLOB メタデータを使用して、各 BLOB のターゲット テーブルのプロパティを指定することもできます。 データは、[インジェスト プロパティ](#ingestion-properties)の指定に従って、動的にルーティングされます。

次の例は、BLOB メタデータをアップロードする前にインジェスト プロパティを設定する方法を示しています。 BLOB は異なるテーブルにルーティングされます。

詳細については、「[BLOB をアップロードする](#upload-blobs)」を参照してください。

```csharp
// Blob is dynamically routed to table `Events`, ingested using `EventsMapping` data mapping
blob = container.GetBlockBlobReference(blobName2);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoTable", "Events");
blob.Metadata.Add("kustoDataFormat", "json");
blob.Metadata.Add("kustoIngestionMappingReference", "EventsMapping");
blob.UploadFromFile(jsonCompressedLocalFileName);
```

## <a name="upload-blobs"></a>BLOB をアップロードする

ローカル ファイルから BLOB を作成し、インジェストのプロパティを BLOB メタデータに設定して、それをアップロードすることができます。 例については、「[Event Grid の通知をサブスクライブすることで Data Explorer に BLOB を取り込む](data-explorer-ingest-event-grid-portal.md#generate-sample-data)」のページを参照してください。

> [!NOTE]
> - `BlockBlob` を使用してデータを生成します。 `AppendBlob` がサポートされていません。
> - Azure Data Lake Gen2 ストレージの SDK を使用するには、ファイルのアップロードに `CreateFile` を使用し、最後の `Flush` で close パラメーターを「true」に設定する必要があります。
<!-- > For a detailed example of Data Lake Gen2 SDK correct usage, see [upload file using Azure Data Lake SDK](data-connection-event-grid-csharp.md#upload-file-using-azure-data-lake-sdk). -->
> - イベント ハブ エンドポイントでイベントの受信が認識されない場合、Azure Event Grid によって再試行メカニズムがアクティブ化されます。 この再試行配信に失敗した場合、配信されなかったイベントは Event Grid の "*配信不能*" プロセスを使用してストレージ アカウントに配信できます。 詳細については、[Event Grid のメッセージの配信と再試行](../../../event-grid/delivery-and-retry.md)に関する記事を参照してください。

## <a name="rename-blobs"></a>BLOB を名前変更する

ADLSv2 を使用する場合、BLOB を名前変更して、Data Explorer への BLOB インジェストをトリガーできます。 例については、「[Event Grid の通知をサブスクライブすることで Data Explorer に BLOB を取り込む](data-explorer-ingest-event-grid-portal.md#generate-sample-data)」のページを参照してください。

> [!NOTE]
> - ディレクトリの名前変更は ADLSv2 で実行できますが、"*名前変更された BLOB*" イベントおよびディレクトリ内への BLOB のインジェストはトリガーされません。 名前変更後に BLOB を取り込むには、目的の BLOB を直接名前変更します。
> - [データ接続を作成](data-explorer-ingest-event-grid-portal.md#create-an-event-grid-data-connection)するとき、特定のサブジェクトを追跡するためのフィルターを定義した場合。<!-- or while creating [Event Grid resources manually](ingest-data-event-grid-manual.md#create-an-event-grid-subscription), these filters are applied on the destination file path. -->

## <a name="delete-blobs-using-storage-lifecycle"></a>ストレージ ライフサイクルを使用した BLOB の削除

Data Explorer では、取り込み後に BLOB は削除されません。 [Azure Blob Storage のライフサイクル](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal)を使用して、BLOB の削除を管理してください。 BLOB は 3 日から 5 日間保持することをお勧めします。

## <a name="known-event-grid-issues"></a>Event Grid の既知の問題

- Data Explorer を使用して、Event Grid のインジェストに使用されるファイルを[エクスポート](/azure/data-explorer/kusto/management/data-export/export-data-to-storage?context=/azure/synapse-analytics/context/context)する場合は、次の点に注意してください。 
    - エクスポート コマンドに指定された接続文字列、または[外部テーブル](/azure/data-explorer/kusto/management/data-export/export-data-to-an-external-table?context=/azure/synapse-analytics/context/context)に指定された接続文字列が [ADLS Gen2 形式](/azure/data-explorer/kusto/api/connection-strings/storage?context=/azure/synapse-analytics/context/context#azure-data-lake-storage-gen2)の接続文字列 (`abfss://filesystem@accountname.dfs.core.windows.net`など) であるが、ストレージ アカウントが階層型名前空間に対して有効になっていない場合、Event Grid 通知はトリガーされません。
    - アカウントが階層型名前空間に対して有効でない場合、接続文字列で [Blob Storage](/azure/data-explorer/kusto/api/connection-strings/storage?context=/azure/synapse-analytics/context/context#azure-blob-storage) 形式 (たとえば、`https://accountname.blob.core.windows.net`) を使用する必要があります。 ADLS Gen2 接続文字列を使用している場合でもエクスポートは想定どおりに動作しますが、通知はトリガーされず、Event Grid インジェストは機能しません。

## <a name="next-steps"></a>次の手順

- [Event Grid の通知をサブスクライブすることで Data Explorer に BLOB を取り込む](data-explorer-ingest-event-grid-portal.md)
<!-- - [Create an Event Grid data connection for Data Explorer by using C#](data-connection-event-grid-csharp.md)
- [Create an Event Grid data connection for Data Explorer by using Python](data-connection-event-grid-python.md)
- [Create an Event Grid data connection for Data Explorer by using Azure Resource Manager template](data-connection-event-grid-resource-manager.md)
- [Use one-click ingestion to ingest CSV data from a container to a new table in Data Explorer](one-click-ingestion-new-table.md) -->
