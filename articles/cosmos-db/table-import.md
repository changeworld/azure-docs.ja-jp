---
title: "Azure Cosmos DB Table API で使用するデータのインポート | Microsoft Docs"
description: "Azure Cosmos DB Table API で使用するデータのインポート方法を説明します。"
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: 
ms.assetid: b60743e2-0227-43ab-965a-0ae3ebacd917
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: 984510b2dae99849507953163f94ad2f925651cd
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="import-data-for-use-with-the-azure-cosmos-db-table-api"></a>Azure Cosmos DB Table API で使用するデータのインポート

このチュートリアルでは、Azure Cosmos DB [Table API](table-introduction.md) で使用するデータをインポートする手順を示します。 Azure Table Storage に格納されたデータがある場合は、データ移行ツールまたは AzCopy を使用してデータを Azure Cosmos DB にインポートできます。 データがインポートされたら、Azure Cosmos DB によって提供される高度な機能 (ターンキー グローバル配信、専用スループット、99 パーセンタイルで 10 ミリ秒未満の待ち時間、高可用性の保証、および自動セカンダリ インデックス作成など) を活用できるようになります。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * データ移行ツールを使用したデータのインポート
> * AzCopy を使用したデータのインポート

## <a name="data-migration-tool"></a>データ移行ツール

Azure Cosmos DB のデータ移行ツールは、既存の Azure Table Storage データをインポートするためのオプションの 1 つです。 ツールを使用してデータをインポートするには、Azure Table Storage をソースとして、Azure Cosmos DB Table API をターゲットとして選択し、ツールの要求に従ってその他の情報を指定します。 データは一括でエクスポートするか、シーケンシャル レコード インポートを使用できます。 

Azure Table Storage をソースとして、また Table API をターゲットとして定義するための情報は、次のセクションに記載されています。
- [データ移行のソースとして Azure Table Storage を使用する](import-data.md#AzureTableSource) 
- [一括インポートで Table API にエクスポートする](import-data.md#tableapibulkexport)
- [シーケンシャル レコード インポートで Table API にエクスポートする](import-data.md#tableapiseqtarget)

## <a name="azcopy-command"></a>AzCopy コマンド

AzCopy コマンド ライン ユーティリティを使用することが、Azure Table Sorage から Azure Cosmos DB Table API にデータを移行するためのもう 1 つのオプションです。 AzCopy を使用するには、まず「[Table Storage からデータをエクスポートする](../storage/common/storage-use-azcopy.md#export-data-from-table-storage)」の説明に従ってマニフェスト ファイルにデータをエクスポートし、次にマニフェスト ファイルから [Azure Cosmos DB Table API](../storage/common/storage-use-azcopy.md#import-data-into-table-storage) にデータをインポートします。

## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * データ移行ツールを使用したデータのインポート
> * AzCopy を使用したデータのインポート

次のチュートリアルに進み、Azure Cosmos DB Table API を使用してデータにクエリを実行する方法を学ぶことができます。 

> [!div class="nextstepaction"]
>[データにクエリを実行する方法](../cosmos-db/tutorial-query-table.md)



