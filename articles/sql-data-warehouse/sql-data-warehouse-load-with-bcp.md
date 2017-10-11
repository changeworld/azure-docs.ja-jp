---
title: "Bcp を使用して、SQL データ ウェアハウスにデータを読み込む |Microsoft ドキュメント"
description: "どのような bcp とデータ ウェアハウスのシナリオを使用する方法について説明します。"
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: f9467d11-fcd6-4131-a65a-2022d2c32d24
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: cakarst;barbkess
ms.openlocfilehash: 7596eac10fdf53380d85128265430ce07b551fe3
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2017
---
# <a name="load-data-with-bcp"></a>Bcp を使用したデータの読み込み
> [!div class="op_single_selector"]
> * [Redgate](sql-data-warehouse-load-with-redgate.md)  
> * [データ ファクトリ](sql-data-warehouse-get-started-load-with-azure-data-factory.md)  
> * [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)  
> * [BCP](sql-data-warehouse-load-with-bcp.md)
> 
> 

**[bcp] [ bcp]** コマンド ライン一括読み込みユーティリティで、SQL サーバー、データ ファイル、および SQL データ ウェアハウスの間でデータをコピーすることができます。 SQL データ ウェアハウスのテーブルに大量の行をインポートするか、SQL Server テーブルからデータ ファイルにデータをエクスポートするのには、bcp を使用します。 Queryout オプションで使用する場合、を除き bcp の TRANSACT-SQL の知識は必要ありません。

bcp は、SQL データ ウェアハウス データベースに出入りするサイズの小さいデータ セットを移動する迅速かつ簡単な方法です。 正確な bcp 経由でのロード/抽出することが推奨されるデータ量が依存している Azure データ センターへの接続のネットワークにします。  一般に、ディメンション テーブルがロードされ、bcp で簡単に抽出することができます、ただし、bcp は推奨されませんの読み込みや、大量のデータを抽出します。  Polybase は、読み込みと同様に的確に SQL データ ウェアハウスの膨大な並列処理のアーキテクチャを活用することに大量のデータを抽出するための推奨ツールです。

Bcp では、次のことができます。

* SQL データ ウェアハウスにデータを読み込むには、単純なコマンド ライン ユーティリティを使用します。
* SQL データ ウェアハウスからデータを抽出するのにには、単純なコマンド ライン ユーティリティを使用します。

このチュートリアル方法を説明します。

* コマンドで、bcp を使用してテーブルにデータをインポートします。
* テーブルの使用、bcp コマンドをからデータをエクスポートします。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Loading-data-into-Azure-SQL-Data-Warehouse-with-BCP/player]
> 
> 

## <a name="prerequisites"></a>前提条件
このチュートリアルの手順が必要です。

* SQL データ ウェアハウス データベース
* インストールされている、bcp コマンド ライン ユーティリティ
* インストールされている SQLCMD コマンド ライン ユーティリティ

> [!NOTE]
> Bcp および sqlcmd ユーティリティをダウンロードすることができます、 [Microsoft ダウンロード センター][Microsoft Download Center]です。
> 
> 

## <a name="import-data-into-sql-data-warehouse"></a>SQL データ ウェアハウスにデータをインポートします。
このチュートリアルでは、Azure SQL Data Warehouse にテーブルを作成し、テーブルにデータをインポートします。

### <a name="step-1-create-a-table-in-azure-sql-data-warehouse"></a>手順 1: Azure SQL Data Warehouse にテーブルを作成します。
コマンド プロンプトから sqlcmd を使用して、インスタンスでテーブルを作成する次のクエリを実行します。

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    WITH
    (
        CLUSTERED COLUMNSTORE INDEX,
        DISTRIBUTION = ROUND_ROBIN
    );
"
```

> [!NOTE]
> 参照してください[テーブルの概要][ Table Overview]または[CREATE TABLE 構文][ CREATE TABLE syntax]詳細については、SQL データ ウェアハウスと、WITH 句で使用可能なオプションでテーブルを作成します。
> 
> 

### <a name="step-2-create-a-source-data-file"></a>手順 2: ソース データ ファイルを作成します。
メモ帳を開いて新しいテキスト ファイルに次の行のデータをコピーして、ローカル一時ディレクトリに C:\Temp\DimDate2.txt このファイルを保存します。

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

> [!NOTE]
> ことが重要、bcp.exe が utf-8 のファイル エンコードをサポートしていませんに注意してください。 Bcp.exe を使用する場合に、ASCII ファイルまたは utf-16 でエンコードされたファイルを使用してください。
> 
> 

### <a name="step-3-connect-and-import-the-data"></a>手順 3: 接続し、データのインポート
Bcp を使用して、接続し、必要に応じて値を置き換える次のコマンドを使用してデータをインポートできます。

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

Sqlcmd を使用して、次のクエリを実行して、データが読み込まれたことを確認することができます。

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

これには、次の結果が返されます。

| DateId | CalendarQuarter | FiscalQuarter |
| --- | --- | --- |
| 20150101 |1 |3 |
| 20150201 |1 |3 |
| 20150301 |1 |3 |
| 20150401 |2 |4 |
| 20150501 |2 |4 |
| 20150601 |2 |4 |
| 20150701 |3 |1 |
| 20150801 |3 |1 |
| 20150801 |3 |1 |
| 20151001 |4 |2 |
| 20151101 |4 |2 |
| 20151201 |4 |2 |

### <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>手順 4: 新しく読み込まれたデータに統計を作成します。
Azure SQL Data Warehouse では、サポートの自動作成] または [統計の更新を自動まだされていません。 クエリから最高のパフォーマンスを取得するためには、最初の読み込み後にすべてのテーブルのすべての列に統計を作成することが重要またはデータに大幅な変更が発生します。 統計情報の詳細については、次を参照してください。、[統計][ Statistics]トピックの開発グループ内のトピックです。 この例では読み込まれて、テーブルの統計を作成する方法の簡単な例を次に示します

Sqlcmd プロンプトから次の CREATE STATISTICS ステートメントを実行します。

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="export-data-from-sql-data-warehouse"></a>SQL データ ウェアハウスからデータをエクスポートします。
このチュートリアルでは、SQL データ ウェアハウス内のテーブルからデータ ファイルを作成します。 上で作成したデータをエクスポートする DimDate2_export.txt と呼ばれる新しいデータ ファイルにします。

### <a name="step-1-export-the-data"></a>手順 1: データをエクスポートします。
Bcp ユーティリティを使用して、接続し、適切な値を置換する次のコマンドを使用してデータをエクスポートできます。

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
新しいファイルを開くことによってデータが正しくエクスポートされたことを確認することができます。 ファイル内のデータは、次のテキストに一致する必要があります。

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

> [!NOTE]
> 分散システムの性質上、データの順序できない可能性があります、同じ SQL データ ウェアハウスのデータベース間でします。 別のオプションは、使用する、 **queryout**のクエリを記述する bcp 関数を抽出ではなくテーブル全体をエクスポートします。
> 
> 

## <a name="next-steps"></a>次のステップ
読み込みの詳細については、次を参照してください。 [SQL データ ウェアハウスにデータを読み込む][Load data into SQL Data Warehouse]です。
開発の詳細については、次を参照してください。 [SQL データ ウェアハウスの開発の概要][SQL Data Warehouse development overview]です。

<!--Image references-->

<!--Article references-->

[Load data into SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
[Table Overview]: ./sql-data-warehouse-tables-overview.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
