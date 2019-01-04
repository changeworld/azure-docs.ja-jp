---
title: Azure Data Lake Storage Gen2 に関する既知の問題 | Microsoft Docs
description: Azure Data Lake Storage Gen2 に関する制限と既知の問題について説明します
services: storage
author: normesta
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: normesta
ms.openlocfilehash: bc6f59922286663435782623a72345c19adb494c
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/03/2019
ms.locfileid: "53995036"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 に関する既知の問題

この記事では、Azure Data Lake Storage Gen2 の既知の問題と一時的な制限事項について説明します。

## <a name="api-interoperability"></a>API の相互運用性

Blob Storage API と Azure Data Lake Gen2 API 間には相互運用性がありません。

アカウントにアップロードするすべてのコンテンツの操作に同じツールを使用する必要がある場合、このような API 間の相互運用が可能になるまでは、BLOB ストレージ アカウントで階層型名前空間を有効にしないでください。 階層型名前空間なしでストレージ アカウントを使用すると、ディレクトリやファイルシステムのアクセス制御リストなど、Data Lake Storage Gen2 固有の機能にアクセスできなくなります。

## <a name="blob-storage-apis"></a>BLOB ストレージ API

BLOB ストレージ API は、Azure Data Lake Storage Gen2 アカウントではまだ使用できません。

BLOB Storage API と Azure Data Lake Gen2 API 間の相互運用性がまだ不十分なために発生する可能性のある不適切なデータ アクセスの問題を防ぐために、これらの API は無効になっています。

これらの API を使用して、データが無効にされる前に、データを読み込んでおり、そのデータにアクセスする運用上の要件がある場合は、次の情報を用意して Microsoft サポートにお問い合わせください。

* サブスクリプション ID (GUID、名前でなく)

* ストレージ アカウント名

* 運用に大きな影響があるかどうか。その場合は、どのストレージ アカウントか。

* 運用に大きな影響がない場合でも、何らかの理由でこのデータを別のストレージ アカウントにコピーする必要があるかどうか、ある場合はその理由をお伝えください。

このような状況では、階層型名前空間が有効にされていないストレージ アカウントにこのデータをコピーできるように、一定の期間 BLOB API へのアクセスを復元できます。

アンマネージド仮想マシン (VM) ディスクは、無効にされた Blob Storage API に依存しているため、ストレージ アカウントで階層型名前空間を有効にする場合は、アンマネージド VM ディスクを階層型名前空間が有効ではないストレージ アカウントに配置することを検討してください。

## <a name="azure-storage-explorer"></a>Azure ストレージ エクスプローラー

Azure Storage Explorer を使用して Data Lake Storage Gen2 アカウントを表示または管理するには、[無料ダウンロード](https://azure.microsoft.com/features/storage-explorer/)として入手可能なバージョン `1.6.0` 以上のツールが必要です。

Azure portal に埋め込まれている Storage Explorer のバージョンでは、階層型名前空間が有効な Data Lake Storage Gen2 アカウントの表示や管理を現在サポートしていません。

## <a name="blob-viewing-tool"></a>BLOB 表示ツール

Azure Data Lake Storage Gen2 では、Azure portal の BLOB 表示ツールの一部の機能のみがサポートされています。

## <a name="third-party-applications"></a>サードパーティ アプリケーション

サードパーティ アプリケーションは、Azure Data Lake Storage Gen2 をサポートしていない可能性があります。

サポートは、各サードパーティ アプリケーション プロバイダーの判断で行われます。 現在、Blob Storage API と Azure Data Lake Storage Gen2 API を使用して同じコンテンツを管理することはできません。 Microsoft はこの相互運用性を実現するために取り組んでいるため、多くのサードパーティ ツールが自然と Azure Data Lake Storage Gen2 をサポートする可能性があります。

## <a name="azcopy-support"></a>AzCopy のサポート

AzCopy バージョン 8 は、Azure Data Lake Storage Gen2 をサポートしていません。

代わりに、最新のプレビュー バージョンの AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)) を使用してください (Azure Data Lake Storage Gen2 エンドポイントをサポートしています)。

## <a name="azure-event-grid"></a>Azure Event Grid

Azure Data Lake Gen2 アカウントからイベントがまだ生成されていないため、[Azure Event Grid](https://azure.microsoft.com/services/event-grid/) はそのイベントを受信していません。  

## <a name="soft-delete-and-snapshots"></a>ソフト削除とスナップショット

Azure Data Lake Storage Gen2 アカウントでは、ソフト削除とスナップショットを使用できません。

階層型名前空間が有効なストレージ アカウントでは、[スナップショット](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)と[ソフト削除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)を含むすべてのバージョン管理機能をまだ使用できません。

## <a name="object-level-storage-tiers"></a>オブジェクト レベルのストレージ層

Azure Data Lake Storage Gen 2 アカウントでは、オブジェクト レベルのストレージ層 (ホット、コールド、アーカイブ) をまだ使用できませんが、階層型空間が有効ではないストレージ アカウントでは使用できます。

## <a name="azure-blob-storage-lifecycle-management-preview-policies"></a>Azure Blob Storage ライフサイクル管理 (プレビュー) ポリシー

Azure Data Lake Storage Gen2 アカウントでは、Azure Blob Storage ライフサイクル管理 (プレビュー) ポリシーをまだ使用できません。

このようなポリシーは、階層型空間が有効ではないストレージ アカウントに使用できます。

## <a name="diagnostic-logs"></a>診断ログ

診断ログは、Azure Data Lake Storage Gen2 アカウントでは使用できません。

診断ログを要求するには、Azure サポートにお問い合わせください。 ログが必要なアカウント名と期間を伝えてください。
