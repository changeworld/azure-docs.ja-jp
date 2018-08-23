---
title: ETL の代わりに Azure SQL Data Warehouse 用の ELT を設計する | Microsoft Docs
description: ETL の代わりに、データの読み込みまたは Azure SQL Data Warehouse 用に抽出、読み込み、変換 (ELT) プロセスを設計します。
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 33e4a405547fcdd797ddfdf6aba6c6c1c126b742
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2018
ms.locfileid: "42146225"
---
# <a name="designing-extract-load-and-transform-elt-for-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse 用の抽出、読み込み、変換 (ELT) の設計

抽出、変換、読み込み (ETL) の代わりに、データを Azure SQL Data Warehouse に読み込むための抽出、読み込み、変換 (ELT) プロセスを設計します。 この記事では、Azure データ ウェアハウスにデータを移動する ELT プロセスを設計する方法について説明します。

## <a name="what-is-elt"></a>ELT とは?

抽出、読み込み、変換 (ELT) は、ソース システムから目的のデータ ウェアハウスにデータを移動するプロセスです。 このプロセスは定期的に実行され (1 時間ごと、1 日ごとなど)、新たに生成されたデータをデータ ウェアハウスに取得します。 ソースからデータ ウェアハウスにデータを取得するのに最適な方法は、PolyBase を使用して SQL Data Warehouse にデータを読み込む ELT プロセスを開発することです。

ELT では最初にデータを読み込んでから変換するのに対し、抽出、変換、読み込み (ETL) ではデータを読み込む前に変換します。 ETL の代わりに ELT を実行すると、データを読み込む前に変換するための独自のリソースを用意するコストが削減されます。 SQL Data Warehouse を使用している場合、ELT は MPP システムを利用して変換を実行します。

SQL Data Warehouse 用 ELT の実装にはさまざまな方法がありますが、基本的な手順は次のとおりです。  

1. ソース データをテキスト ファイルに抽出します。
2. そのデータを Azure Blob Storage または Azure Data Lake Store に配置します。
3. 読み込むデータを準備します。
2. PolyBase を使用して、SQL Data Warehouse ステージング テーブルにデータを読み込みます。
3. データを変換します。
4. 運用環境テーブルにデータを挿入します。


読み込みのチュートリアルについては、「[PolyBase を使用して Azure Blob Storage から Azure SQL Data Warehouse にデータを読み込む](load-data-from-azure-blob-storage-using-polybase.md)」を参照してください。

詳細については、[読み込みパターンに関するブログ](http://blogs.msdn.microsoft.com/sqlcat/2017/05/17/azure-sql-data-warehouse-loading-patterns-and-strategies/)をご覧ください。 

## <a name="options-for-loading-with-polybase"></a>PolyBase を使用してデータを読み込むためのオプション

PolyBase は、T-SQL 言語を使用してデータベースの外部にあるデータにアクセスするテクノロジです。 SQL Data Warehouse にデータを読み込むのに最適な方法です。 PolyBase では、データはデータ ソースから直接コンピューティング ノードに同時に読み込まれます。 

PolyBase を使用してデータを読み込むには、次のいずれかの読み込みオプションを使用します。

- [T-SQL を使用した PolyBase](load-data-from-azure-blob-storage-using-polybase.md) - データが Azure Blob Storage または Azure Data Lake Store 内にある場合に最適です。 読み込みプロセスを細かく制御できますが、外部データ オブジェクトの定義も必要となります。 その他の方法では、外部データ オブジェクトは、ソース テーブルを移行先テーブルにマップするときにバック グラウンドで定義されます。  T-SQL の読み込みを調整するには、Azure Data Factory、SSIS、または Azure Functions を使用します。 
- [SSIS を使用した PolyBase](/sql/integration-services/load-data-to-sql-data-warehouse) - ソース データが SQL Server にある場合に有効です。SQL Server の場所は、オンプレミス、クラウドを問いません。 SSIS は、移動元テーブルと移動先テーブルのマッピングを定義するほか、読み込みの調整も行います。 SSIS パッケージが既にある場合、そのパッケージが移動先の新しいデータ ウェアハウスで機能するように変更できます。 
- [Azure Data Factory (ADF) を使用した PolyBase](sql-data-warehouse-load-with-data-factory.md) - もう 1 つのオーケストレーション ツールです。  このツールはパイプラインを定義し、ジョブのスケジュールを設定します。 
- [Azure DataBricks を使用した PolyBase](../azure-databricks/databricks-extract-load-sql-data-warehouse.md) は、SQL Data Warehouse のテーブルから Databricks のデータフレームにデータを転送したり、Databricks のデータフレームから SQL Data Warehouse のテーブルにデータを書き込んだりします。

### <a name="polybase-external-file-formats"></a>PolyBase の外部ファイル形式

PolyBase は、UTF-8 と UTF-16 でエンコードされた区切りテキスト ファイルからデータを読み込みます。 この区切りテキスト ファイルに加え、Hadoop ファイル形式の RC ファイル、ORC、Parquet からも読み込みます。 PolyBase は、Gzip や Snappy の圧縮ファイルからデータを読み込むことができます。 PolyBase では現在、拡張 ASCII、固定幅形式、および WinZip、JSON、XML などの入れ子形式はサポートされていません。

### <a name="non-polybase-loading-options"></a>PolyBase 以外の読み込みオプション
使用するデータが PolyBase と互換性がない場合は、[bcp](/sql/tools/bcp-utility) または [SQLBulkCopy API](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx) を使用できます。 bcp では、Azure Blob Storage を通さずに直接 SQL Data Warehouse に読み込みます。また、小規模の読み込みのみを対象とします。 これらのオプションの読み込みパフォーマンスは、PolyBase と比べてはるかに低速であることに注意してください。 


## <a name="extract-source-data"></a>ソース データの抽出

ソース システムからのデータの取得方法は、ソースによって異なります。  最終的な目標は、区切りテキスト ファイルにデータを移動することです。 SQL Server を使用している場合は、[bcp コマンド ライン ツール](/sql/tools/bcp-utility)を使ってデータをエクスポートできます。  

## <a name="land-data-to-azure-storage"></a>Azure Storage へのデータの配置

Azure Storage にデータを配置するには、[Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) または [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) にデータを移動します。 どちらの場合も、データはテキスト ファイルに格納されます。 PolyBase では、どちらの場所からでも読み込みが可能です。

Azure Storage へのデータの移動で使用できるツールやサービスは、次のとおりです。

- [Azure ExpressRoute](../expressroute/expressroute-introduction.md) サービス - ネットワークのスループット、パフォーマンス、予測可能性を向上させます。 ExpressRoute は、専用プライベート接続を通してデータを Azure にルーティングするサービスです。 ExpressRoute 接続では、パブリック インターネットを通してデータをルーティングすることはありません。 ExpressRoute 接続は、パブリック インターネットを通る一般的な接続に比べて安全性と信頼性が高く、待機時間も短く、高速です。
- [AZCopy ユーティリティ](../storage/common/storage-moving-data.md) - パブリック インターネットを通してデータを Azure Storage に移動します。 このユーティリティは、データ サイズが 10 TB より小さい場合に機能します。 AZCopy を使用して読み込みを定期的に実行するには、ネットワーク速度をテストして、許容可能かどうかを確認してください。 
- [Azure Data Factory (ADF)](../data-factory/introduction.md) - ゲートウェイをローカル サーバーにインストールできます。 その後、ローカル サーバーから Azure Storage にデータを移動するためのパイプラインを作成できます。 SQL Data Warehouse での Data Factory の使用については、[SQL Data Warehouse へのデータの読み込み](/azure/data-factory/load-azure-sql-data-warehouse)に関する記事をご覧ください。

## <a name="prepare-data"></a>データを準備する

SQL Data Warehouse に読み込む前に、ストレージ アカウント内でデータを準備し、整理する必要がある場合があります。 データの準備は、データがソース内にあるとき、データをテキスト ファイルにエクスポートするとき、またはデータが Azure Storage に配置された後に実施できます。  データの操作は、プロセスの早い段階の方が、最も簡単に行えます。  

### <a name="define-external-tables"></a>外部テーブルを定義する
データを読み込む前に、データ ウェアハウスに外部テーブルを定義する必要があります。 PolyBase は、外部テーブルを使用して Azure Storage のデータを定義し、それにアクセスします。 外部テーブルは通常のテーブルとほとんど同じです。 主な違いは、外部テーブルはデータ ウェアハウスの外部に格納されたデータをポイントする点です。 

外部テーブルを定義するには、データ ソース、テキスト ファイルの形式、テーブル定義を指定する必要があります。 必要な T-SQL 構文のトピックは次のとおりです。
- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql)

外部オブジェクトの作成の例については、読み込みのチュートリアルの[外部テーブルの作成](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data)に関する手順をご覧ください。

### <a name="format-text-files"></a>テキスト ファイルの書式設定

外部オブジェクトを定義したら、外部テーブルおよびファイル形式の定義に合わせて、テキスト ファイルの行を配置する必要があります。 テキスト ファイルの各行のデータは、テーブル定義と一致させる必要があります。

テキスト ファイルを書式設定するには、次の処理を行います。

- データが非リレーショナル ソースから読み込まれる場合は、データを行と列に変換する必要があります。 データの読み込み元がリレーショナル ソースの場合も、非リレーショナル ソースの場合も、データを読み込むテーブルの列定義に合わせてデータを変換する必要があります。 
- SQL Data Warehouse の変換先テーブルの列とデータ型に合わせて、テキスト ファイルのデータを書式設定します。 外部テキスト ファイルとデータ ウェアハウス テーブルの間でデータ型の不整合があると、読み込みの際に行が拒否されます。
- テキスト ファイル内のフィールドは終端記号で区切ります。  ソース データに含まれていない文字または文字シーケンスを使用するようにしてください。 [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql) で指定した終端記号を使用します。

## <a name="load-to-a-staging-table"></a>ステージング テーブルへの読み込み
データ ウェアハウスにデータを取得するには、最初にデータをステージング テーブルに読み込むと効果的です。 ステージング テーブルを使用することにより、運用環境テーブルに干渉せずにエラーを処理し、運用環境テーブルへのロールバック操作を回避できます。 ステージング テーブルを利用すると、運用環境テーブルに挿入する前に、SQL Data Warehouse を使用してデータを変換することもできます。

T-SQL で読み込みを行うには、T-SQL ステートメント [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) を実行します。 このコマンドを実行すると、select ステートメントの結果が新しいテーブルに挿入されます。 このステートメントが外部テーブルから選択すると、外部データがインポートされます。 

次の例では、ext.Date が外部テーブルとなっています。 dbo.Date という新しいテーブルにすべての行がインポートされます。

```sql
CREATE TABLE [dbo].[Date]
WITH
( 
    CLUSTERED COLUMNSTORE INDEX
)
AS SELECT * FROM [ext].[Date]
;
```

## <a name="transform-the-data"></a>データの変換
データがステージング テーブルにある間に、ワークロードに必要な変換を実行します。 その後、運用環境テーブルにデータを移動します。

## <a name="insert-data-into-production-table"></a>運用環境テーブルへのデータの挿入

INSERT INTO ...SELECT ステートメントを実行すると、データがステージング テーブルから永続テーブルに移動します。 

ETL プロセスを設計する際は、小規模のテスト サンプルでプロセスを実行してみてください。 テーブルから 1 つのファイルに 1000 行を抽出し、Azure に移動してから、ステージング テーブルに読み込んでみてください。 

## <a name="partner-loading-solutions"></a>パートナー読み込みソリューション
パートナーの多くが読み込みソリューションを提供しています。 詳細については、[ソリューション パートナー](sql-data-warehouse-partner-business-intelligence.md)の一覧をご覧ください。 

## <a name="next-steps"></a>次の手順
読み込みのガイダンスについては、[データ読み込みのガイダンス](guidance-for-loading-data.md)に関するページをご覧ください。


