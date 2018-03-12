---
title: "bcp を使用した SQL Data Warehouse へのデータの読み込み | Microsoft Docs"
description: "データ ウェアハウジングのシナリオに沿って、bcp の概要と、その使用方法を学習します。"
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
ms.date: 01/22/2018
ms.author: cakarst;barbkess
ms.openlocfilehash: 146c6fdada651551c05b2cbcadc3e1248a40b613
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2018
---
# <a name="load-data-with-bcp"></a>bcp を使用したデータの読み込み

**[bcp](/sql/tools/bcp-utility.md)** はコマンド ライン形式の一括読み込みユーティリティの一種であり、これを使用して SQL Server、データ ファイル、および SQL Data Warehouse の間でデータをコピーできます。 bcp を使用して SQL Data Warehouse のテーブルに多数の行をインポートしたり、SQL Server のテーブルからデータ ファイルにデータをエクスポートしたりします。 queryout オプションと併用する場合を除き、bcp を使用するときに Transact-SQL の知識は必要ありません。

bcp を使用すれば、すばやく、簡単に SQL Data Warehouse データベースとの間で小規模なデータ セットを読み込み/抽出できます。 bcp を使用して読み込み/抽出する場合に推奨される正確なデータ量は、Azure へのネットワーク接続によって異なります。  小さなディメンション テーブルは、bcp を使用して簡単に読み込みおよび抽出できます。 ただし、大量のデータの読み込みと抽出を行うためのツールとしては、bcp ではなく Polybase をお勧めします。 PolyBase は、SQL Data Warehouse の超並列処理アーキテクチャ向けに設計されています。

bcp では次のことができます。

* コマンド ライン ユーティリティを使用して、SQL Data Warehouse にデータを読み込みます。
* コマンド ライン ユーティリティを使用して、SQL Data Warehouse からデータを抽出します。

このチュートリアルの内容:

* bcp in コマンドを使用してテーブルにデータをインポートする
* bcp out コマンドを使用してテーブルからデータをエクスポートする

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Loading-data-into-Azure-SQL-Data-Warehouse-with-BCP/player]
> 
> 

## <a name="prerequisites"></a>前提条件
このチュートリアルを進めるには、次が必要です。

* SQL Data Warehouse データベース
* bcp および sqlcmd コマンド ラインユーティリティ。 これらは、[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=36433)からダウンロードできます。 

### <a name="data-in-ascii-or-utf-16-format"></a>ASCII または UTF-16 形式のデータ
自身のデータを使ってこのチュートリアルを試す場合、bcp では UTF-8 がサポートされないため、データには ASCII または UTF-16 エンコードを使用する必要があります。 

PolyBase では UTF-8 がサポートされていますが、UTF-16 はまだサポートされていません。 bcp をデータのエクスポート用に使用し、PolyBase をデータの読み込み用に使用するには、SQL Server からエクスポートしたデータを UTF-8 に変換する必要があります。 

## <a name="import-data-into-sql-data-warehouse"></a>SQL Data Warehouse へのデータのインポート
このチュートリアルでは、Azure SQL Data Warehouse でテーブルを作成し、そのデータをテーブルにインポートします。

### <a name="step-1-create-a-table-in-azure-sql-data-warehouse"></a>手順 1: Azure SQL Data Warehouse でテーブルを作成する
コマンド プロンプトで sqlcmd を使用して次のクエリを実行し、インスタンスにテーブルを作成します。

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

テーブルの作成の詳細については、[テーブルの概要](sql-data-warehouse-tables-overview.md)に関するページまたは [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse.md) 構文を参照してください。
 

### <a name="step-2-create-a-source-data-file"></a>手順 2: ソース データ ファイルを作成する
メモ帳を開き、データの以下の行を新しいテキスト ファイルにコピーして、このファイルをローカルの一時ディレクトリに保存します (C:\Temp\DimDate2.txt)。

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
> bcp.exe は、UTF-8 のファイルのエンコーディングをサポートしていないことに注意してください。 bcp.exe を使用する場合は、ASCII ファイルまたは UTF-16 エンコード ファイルを使用してください。
> 
> 

### <a name="step-3-connect-and-import-the-data"></a>手順 3: データに接続し、インポートする
bcp を使用して、次のコマンドでデータに接続し、インポートできます。値は適宜置き換えて使用してください。

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

sqlcmd を使用して次のクエリを実行すると、データが読み込まれたかどうかを確認することができます。

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

このクエリにより、次の結果が得られます。

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

### <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>手順 4: 新しくロードしたデータの統計を作成する
データを読み込んだ後、最後の手順として、統計を作成または更新します。 この手順は、クエリのパフォーマンスを向上させるのに役立ちます。 詳細については、[統計](sql-data-warehouse-tables-statistics.md)に関する記事を参照してください。 次の sqlcmd の例では、新しく読み込まれたデータを含むテーブルの統計を作成しています。


```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="export-data-from-sql-data-warehouse"></a>SQL Data Warehouse からのデータのエクスポート
このチュートリアルでは、SQL Data Warehouse 内のテーブルからデータ ファイルを作成します。 前のセクションでインポートしたデータをエクスポートします。 結果は DimDate2_export.txt という名前のファイルに保存されます。

### <a name="step-1-export-the-data"></a>手順 1: データをエクスポートする
bcp ユーティリティを使用して、次のコマンドでデータに接続し、エクスポートできます。値は適宜置き換えて使用してください。

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
新しいファイルを開き、データが正しくエクスポートされたことを確認できます。 ファイル内のデータは、次のテキストと一致する必要があります。

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
> 分散システムの性質上、データの順序は SQL Data Warehouse データベース全体で異なる場合があります。 別のオプションでは、bcp の **queryout** 関数を使用して、テーブル全体をエクスポートするのではなく、抽出するクエリを記述します。
> 
> 

## <a name="next-steps"></a>次の手順
読み込みプロセスを設計するために、[読み込みの概要](sql-data-warehouse-design-elt-data-loading)に関するページを参照します。  



<!--MSDN references-->



<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
