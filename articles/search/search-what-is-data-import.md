---
title: Azure Search におけるデータのインポート | Microsoft Docs
description: Azure Search のインデックスにデータをアップロードする方法について説明します。
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: heidist
ms.openlocfilehash: 6c3756e721847a8cb746f5dc755ccd90bfebe9f0
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008276"
---
# <a name="indexing-in-azure-search"></a>Azure Search でのインデックス作成
> [!div class="op_single_selector"]
> * [概要](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
> 
> 

Azure Search では、[検索インデックス](search-what-is-an-index.md)に読み込まれたコンテンツに対してクエリが実行されます。 この記事では、インデックスにコンテンツを読み込む 2 つの基本的な手法について詳しく見ていきます。1 つは、プログラムからデータをインデックスに "*プッシュ*" する方法、もう 1 つは、[Azure Search インデクサー](search-indexer-overview.md)からサポート対象データ ソースを参照してデータを "*プル*" する方法です。

## <a name="pushing-data-to-an-index"></a>インデックスにデータをプッシュする
プログラムから Azure Search にデータを送信するプッシュ モデルは、最も柔軟なアプローチです。 まず、データ ソースの種類に関して制限がありません。 JSON ドキュメントから成るすべてのデータセットは、そこに含まれる各ドキュメントのフィールドが、インデックスのスキーマに定義されているフィールドに対応していれば、Azure Search インデックスにプッシュすることができます。 実行の頻度にも制限はありません。 インデックスには、必要に応じて何度でも変更をプッシュすることができます。 待機時間の要件が非常に厳しいアプリケーションの場合 (たとえば、検索操作を動的な在庫データベースと同期する必要がある場合) は、プッシュ モデルしか利用できません。

このアプローチでは、ドキュメントを個別に、またはバッチでアップロードできるため、プル モデルよりも柔軟です ("バッチごとに最大 1,000 個" と "16 MB" のうち、どちらか先に達した制限が適用されます)。 プッシュ モデルでは、データのある場所にかかわらず、ドキュメントを Azure Search にアップロードすることもできます。

### <a name="how-to-push-data-to-an-azure-search-index"></a>Azure Search インデックスにデータをプッシュする方法

次の API を使用して、1 つまたは複数のドキュメントをインデックスに読み込むことができます。

+ [ドキュメントの追加、更新、削除 (REST API)](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [indexAction クラス](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet)または [indexBatch クラス](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 

現在では、ポータルを使用してデータをプッシュするためのツール サポートはありません。

それぞれの方法の概要については、[REST を使用したデータのインポート](search-import-data-rest-api.md)または [.NET を使用したデータのインポート](search-import-data-dotnet.md)に関するページを参照してください。


## <a name="pulling-data-into-an-index"></a>インデックスへのデータのプル
プル モデルは、サポートされているデータ ソースをクロールし、データをインデックスに自動的にアップロードします。 Azure Search では、この機能が "*インデクサー*" を使用して実装され、現時点では次のプラットフォームで利用可能です。

+ [Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](http://aka.ms/documentdb-search-indexer)
+ [Azure SQL Database と Azure VM の SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

インデクサーは、インデックスをデータ ソース (通常はテーブル、ビュー、または同等の構造体) に接続し、ソース フィールドをインデックスの同等のフィールドにマップします。 実行中、行セットが自動的に JSON に変換され、指定したインデックスに読み込まれます。 すべてのインデクサーはスケジューリングをサポートしているため、データの更新頻度を指定できます。 ほとんどのインデクサーは、変更の追跡を提供します (データ ソースでサポートされている場合)。 インデクサーは、新しいドキュメントを認識するだけでなく、既存のドキュメントの変更と削除を追跡するため、インデックス内のデータをアクティブに管理する必要がありません。 


### <a name="how-to-pull-data-into-an-azure-search-index"></a>Azure Search インデックスにデータをプルする方法

インデクサーの機能は、[Azure Portal](search-import-data-portal.md)、[REST API](/rest/api/searchservice/Indexer-operations)、および [.NET SDK](/dotnet/api/microsoft.azure.search.indexersoperations) で公開されています。 

ポータルを使用する利点は、通常、Azure Search でソース データセットのメタデータを読み取って既定のインデックス スキーマを生成できることです。 生成されたインデックスは、インデックスが処理されるまでは変更可能です。インデックスが処理された後は、インデックスの再作成を必要としないスキーマの編集のみが許可されます。 加えようとしている変更がスキーマに直接影響する場合、インデックスを再構築する必要があります。 

## <a name="verify-data-import-with-search-explorer"></a>Search エクスプローラーを使用してデータのインポートを検証する

ドキュメントのアップロード時に事前チェックを実行する簡単な方法は、ポータルで **Search エクスプローラー**を使用することです。 エクスプローラーを使用すると、コードを記述することなくインデックスを照会できます。 検索エクスペリエンスは、既定の設定 ([単純構文](/rest/api/searchservice/simple-query-syntax-in-azure-search)、既定の [searchMode クエリ パラメーター](/rest/api/searchservice/search-documents)など) に基づきます。 結果は JSON で返されるため、ドキュメント全体を確認できます。

> [!TIP]
> 数多くの [Azure Search コード サンプル](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search)に埋め込みデータセットやすぐに利用できるデータセットが含まれているため、簡単に使い始めることができます。 ポータルには、("realestate-us-sample" という名前の) 小さな不動産データセットで構成されるサンプルのインデクサーとデータ ソースも用意されています。 サンプル データ ソースで事前構成済みのインデクサーを実行すると、インデックスが作成され、ドキュメントが読み込まれます。このドキュメントは、Search エクスプローラーまたは自分で作成したコードで照会できます。

## <a name="see-also"></a>関連項目

+ [インデクサーの概要](search-indexer-overview.md)
+ [ポータルのチュートリアル: インデックスの作成、読み込み、クエリ](search-get-started-portal.md)