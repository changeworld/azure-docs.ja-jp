---
title: Azure Search インデックスの再構築または検索可能なコンテンツの更新 | Microsoft Docs
description: 完全に再構築された、または一部増分インデックスに新しい要素を追加、既存の要素またはドキュメントを更新、または古いドキュメントを削除して、Azure Search インデックスを更新します。
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: heidist
ms.openlocfilehash: 374e7601169647f0eb7d3a214cf15567b7b11090
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2018
ms.locfileid: "34641426"
---
# <a name="how-to-rebuild-an-azure-search-index"></a>Azure Search インデックスを再構築する方法

インデックスを再構築するとその構造が変更され、Azure Search サービス内のインデックスの物理的表現が変更されます。 反対に、インデックスの更新は、コンテンツのみの更新で、関連する外部データ ソースからの最新の変更が反映されます。 この記事では、構造的および実質的にインデックスを更新する方法について説明します。

インデックスを更新するためには、サービスレベルでの読み取り/書き込みアクセス許可が必要です。 プログラミングにより、更新オプションを指定するパラメーターを使用して REST または .NET を呼び出して、コンテンツの完全再構築または増分インデックスを作成することができます。 

一般的に、インデックスへの更新はオンデマンドベースで実行されます。 ただし、ソース固有の[インデクサー](search-indexer-overview.md)を使用して作成されたインデックスの場合、組み込みのスケジューラを使用することができます。 スケジューラでは、必要に応じて任意の間隔およびパターンと、最大頻度 15 分ごとのドキュメントの更新が可能です。 更新頻度をこれより高くする場合は、トランザクションの二重書き込み、外部データ ソースと Azure Search インデックスの両方を同時に更新するなどして、インデックス更新を手動でプッシュする必要があります。

## <a name="full-rebuilds"></a>完全な再構築

多くの種類の更新で、完全な再構築が必要です。 完全な再構築とは、データとメタデータの両方のインデックスを削除してから、外部データ ソースからインデックスを再作成することを意味します。 プログラミングにより、インデックスを[削除](https://docs.microsoft.com/rest/api/searchservice/delete-index)、[作成](https://docs.microsoft.com/rest/api/searchservice/create-index)、および[再読み込み](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)してインデックスを再構築します。 

再構築後は、クエリ パターンとスコアリング プロファイルをテストしていて、基になるコンテンツが変更された場合は、クエリに変更がある可能性があることに留意してください。

## <a name="when-to-rebuild"></a>再構築するタイミング

インデックス スキーマが不安定な状態にある場合は、開発中に、頻繁に完全な再構築を実行することを計画してください。

| 変更 | 再構築状態|
|--------------|---------------|
| フィールド名、データ型、またはその[インデックス属性](https://docs.microsoft.com/rest/api/searchservice/create-index)の変更 | フィールドの定義を変更すると、Retrievable、SearchAnalyzer、SynonymMaps の[インデックス属性](https://docs.microsoft.com/rest/api/searchservice/create-index)を除き、通常は再構築ペナルティが発生します。 既存のフィールドに Retrievable、SearchAnalyzer、および SynonymMaps 属性を追加するために、インデックスを再構築する必要はありません。|
| フィールドの追加 | 再構築する上で厳密な要件はありません。 既存のインデックス付きドキュメントの新しいフィールドには null 値が設定されます。 その後、インデックスを再作成したときには、Azure Search によって追加された null 値はソース データからの値に置き換えられます。 |
| フィールドの削除 | Azure Search インデックスからフィールドを直接削除することはできません。 代わりに、"削除済み" フィールドを無視して使用しないよう、アプリケーションを設定してください。 物理的には、そのフィールドを無視するスキーマを使用して次回インデックスが再構築されるまで、インデックス内のフィールドの定義とコンテンツはそのまま維持されます。|

> [!Note]
> 階層を切り替えた場合も、再構築が必要です。 ある時点で、より多くの容量が必要だと判断した場合も、一括アップグレードはありません。 新しい容量ポイントで新しいサービスを作成する必要があり新しいサービスで最初からインデックスを構築する必要があります。 

## <a name="partial-or-incremental-indexing"></a>部分または増分インデックスの作成

インデックスが作成されると、通常はサービスを明らかに停止することなく、増分インデックスにフォーカスが移ります。 部分または増分インデックスの作成は、関連データ ソース内のコンテンツの状態を反映して検索インデックスのコンテンツを同期するコンテンツのみのワークロードです。 ソースで追加または削除されたドキュメントは、インデックスに追加またはインデックスから削除されます。 コードでは、[Add、Update または Delete Documents](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) 操作を呼び出すか、.NET で同等の関数を呼び出します。

> [!Note]
> 外部データ ソースをクロールするインデクサーを使用する場合は、ソース システムの変更追跡メカニズムを利用して増分インデックスが作成されます。 [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection)では、`lastModified` フィールドを使用します。 [Azure Table Storage](search-howto-indexing-azure-tables.md#incremental-indexing-and-deletion-detection) では、`timestamp` が同じ役割を果たします。 同様に、[Azure SQL Database インデクサー](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows)と [Azure Cosmos DB インデクサー](search-howto-index-cosmosdb.md#indexing-changed-documents)の両方に行更新のフラグ付けのためのフィールドがあります。 インデクサーの詳細については、[インデクサーの概要](search-indexer-overview.md)に関するページを参照してください。


## <a name="see-also"></a>関連項目

+ [インデクサーの概要](search-indexer-overview.md)
+ [大規模なデータ セットに大規模にインデックスを付ける](search-howto-large-index.md)
+ [ポータル内でのインデックス作成](search-import-data-portal.md)
+ [Azure SQL Database インデクサー](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB インデクサー](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage インデクサー](search-howto-indexing-azure-blob-storage.md)
+ [Azure Table Storage インデクサー](search-howto-indexing-azure-tables.md)
+ [Azure Search のセキュリティ](search-security-overview.md)