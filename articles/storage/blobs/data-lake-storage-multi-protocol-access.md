---
title: Azure Data Lake Storage のマルチプロトコル アクセス | Microsoft Docs
description: BLOB API と、BLOB API と Azure Data Lake Storage Gen2 を使用するアプリケーションを使用します。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/01/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: aebd7b58a2107ac109a03d3ce12f27d65ba0ab90
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769814"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Azure Data Lake Storage のマルチプロトコル アクセス

階層型名前空間があるアカウントで、BLOB API を使用できるようになりました。 これにより、階層型名前空間があるアカウントに対するいくつかの BLOB ストレージ機能だけでなく、ツール、アプリケーション、およびサービスのエコシステムを使用できます。

最近まで、オブジェクト ストレージと分析ストレージ用に別々のストレージ ソリューションを管理する必要がありました。 このため、Azure Data Lake Storage Gen2 によるエコシステムのサポートが制限されていました。 さらに、診断ログなどの BLOB サービスの機能へのアクセスも制限されていました。 さまざまなシナリオを実現するにはアカウント間でデータを移動する必要があるため、寸断されたストレージ ソリューションは管理が容易ではありません。 もう、それを行う必要はありません。

Data Lake Storage のマルチプロトコル アクセスでは、ツール、アプリケーション、およびサービスのエコシステムを使用してデータを操作できます。 これには、サードパーティのツールとアプリケーションも含まれています。 それらを変更することなく、それらで階層型名前空間があるアカウントをポイントすることができます。 BLOB API で階層型名前空間があるアカウントのデータを操作できるようになったため、これらのアプリケーションで BLOB API を呼び出した場合でも、*現状のままで*動作します。

階層型名前空間があるアカウントで、[診断ログ](../common/storage-analytics-logging.md)、[アクセス レベル](storage-blob-storage-tiers.md)、[BLOB ストレージ ライフサイクル管理ポリシー](storage-lifecycle-management-concepts.md)などの BLOB ストレージの機能が動作するようになりました。 そのため、これらの重要な機能へのアクセスを失うことなく、BLOB ストレージ アカウントで階層型名前空間を有効にできます。 

> [!NOTE]
> Data Lake Storage のマルチプロトコル アクセスは一般提供されており、すべてのリージョンで利用できます。 マルチプロトコル アクセスによって有効にされている一部の Azure サービスまたは BLOB ストレージ機能は、引き続きプレビュー段階です。 詳細については、この記事の最後のセクションにある表を参照してください。 

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Azure Data Lake Storage のマルチプロトコル アクセスの実行方法

階層型名前空間があるストレージ アカウントの同じデータに対して、BLOB API と Data Lake Storage Gen2 API を操作できます。 Data Lake Storage Gen2 では階層型名前空間を使用して BLOB API がルーティングされるため、第一級のディレクトリ操作と POSIX 準拠のアクセス制御リスト (ACL) のメリットを活用できます。 

![Azure Data Lake Storage のマルチプロトコル アクセスの概念](./media/data-lake-storage-interop/interop-concept.png) 

BLOB API を使用している既存のツールとアプリケーションでは、これらのメリットが自動的に活用されます。 開発者は、それらを変更する必要はありません。 Data Lake Storage Gen2 では、データにアクセスするためにツールとアプリケーションで使用されるプロトコルに関係なく、ディレクトリとファイルレベルの ACL が一貫して適用されます。 

## <a name="blob-storage-feature-support"></a>Blob ストレージ機能のサポート

Data Lake Storage でマルチプロトコル アクセスを使用すると、Data Lake Storage でより多くの BLOB ストレージ機能を使用できます。 次の表に、Data Lake Storage のマルチプロトコル アクセスによって有効になる機能の一覧を示します。 

この表に示されている項目は、BLOB ストレージ機能のサポートが引き続き拡張されるため、時間の経過と共に変化します。 

> [!NOTE]
> Data Lake Storage のマルチプロトコル アクセスは一般提供されていますが、一部の機能のサポートは引き続きプレビュー段階です。 

|Blob ストレージ機能 | サポート レベル |
|---|---|
|[クール アクセス層](storage-blob-storage-tiers.md)|一般公開|
|BLOB REST API|一般公開|
|BLOB SDK |一般公開|
|[PowerShell (BLOB)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-powershell) |一般公開|
|[CLI (BLOB)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-cli) |一般公開|
|BLOB SDK とファイル システム セマンティクス ([.NET](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-directory-file-acl-dotnet) &vert; [Python](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-directory-file-acl-python) &vert; [Java](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-directory-file-acl-java))|プレビュー|
|[PowerShell とファイル システム セマンティクス](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-directory-file-acl-powershell)|プレビュー|
|[CLI とファイル システム セマンティクス](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-directory-file-acl-cli)|プレビュー|
|[診断ログ](../common/storage-analytics-logging.md)| プレビュー|
|[ライフサイクル管理ポリシー](storage-lifecycle-management-concepts.md)| プレビュー|
|[Azure Event Grid 経由の通知](data-lake-storage-events.md)|プレビュー|
|[アーカイブ アクセス層](storage-blob-storage-tiers.md)| プレビュー|
|[Blobfuse](storage-how-to-mount-container-linux.md)|まだサポートされていません|
|[不変ストレージ](storage-blob-immutable-storage.md)|まだサポートされていません|
|[スナップショット](storage-blob-snapshots.md)|まだサポートされていません|
|[論理的な削除](storage-blob-soft-delete.md)|まだサポートされていません|
|[静的な Web サイト](storage-blob-static-website.md)|まだサポートされていません|

Azure Data Lake Storage Gen2 の既知の問題と制限事項については、「[既知の問題](data-lake-storage-known-issues.md)」を参照してください。

## <a name="azure-ecosystem-support"></a>Azure のエコシステムのサポート

Data Lake Storage でマルチプロトコル アクセスを使用すると、Data Lake Storage でより多くの Azure サービスに接続することもできます。 次の表に、Data Lake Storage のマルチプロトコル アクセスによって有効になるサービスの一覧を示します。 

サポートされている BLOB ストレージ機能の一覧と同様に、この表に示されている項目は、Azure サービスのサポートが引き続き拡張されるため、時間の経過と共に変化します。 

> [!NOTE]
> Data Lake Storage のマルチプロトコル アクセスは一般提供されていますが、一部のサービスのサポートは引き続きプレビュー段階です。 

|Azure サービス | サポート レベル |
|---|---|
|[Azure Data Box](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|一般公開|
|[Azure Event Hubs のキャプチャ](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|一般公開|
|[Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal)|一般公開|
|[IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|一般公開|
|[Logic Apps](https://azure.microsoft.com/services/logic-apps/)|一般公開|
|[Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|プレビュー|

Data Lake Storage Gen2 の Azure エコシステムのサポートの完全な一覧については、「[Azure Data Lake Storage と Azure のサービスを統合する](data-lake-storage-integrate-with-azure-services.md)」を参照してください。

Azure Data Lake Storage Gen2 の既知の問題と制限事項については、「[既知の問題](data-lake-storage-known-issues.md)」を参照してください。

## <a name="next-steps"></a>次の手順

[既知の問題](data-lake-storage-known-issues.md)を参照してください。




