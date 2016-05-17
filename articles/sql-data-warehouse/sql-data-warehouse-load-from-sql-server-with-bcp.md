<properties
   pageTitle="SQL Server から Azure SQL Data Warehouse へのデータの読み込み (bcp) | Microsoft Azure"
   description="データのサイズが小さい場合は、bcp を使用して SQL Server からフラット ファイルにデータをエクスポートし、そのデータを Azure SQL Data Warehouse に直接読み込むことができます。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/08/2016"
   ms.author="lodipalm;barbkess;sonyama"/>


# SQL Server から Azure SQL Data Warehouse へのデータの読み込み (フラット ファイル)

> [AZURE.SELECTOR]
- [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
- [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
- [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)

データ セットが小さい場合は、bcp コマンド ライン ユーティリティを使用して SQL Server からデータをエクスポートした後、そのデータを Azure SQL Data Warehouse に直接読み込むことができます。

このチュートリアルでは、bcp を使用して次の操作を行います。

- bcp の out コマンドを使用して SQL Server からテーブルをエクスポートする (または単純なサンプル ファイルを作成する)。
- テーブルをフラット ファイルから SQL Data Warehouse にインポートする。
- 読み込んだデータの統計を作成する。

>[AZURE.VIDEO loading-data-into-azure-sql-data-warehouse-with-bcp]

## 開始する前に

### 前提条件

このチュートリアルを進めるには、次が必要です。

- SQL Data Warehouse データベース
- インストールされた bcp コマンド ライン ユーティリティ
- インストールされた sqlcmd コマンド ライン ユーティリティ

bcp および sqlcmd ユーティリティは [Microsoft ダウンロード センター][]からダウンロードできます。

### ASCII または UTF-16 形式のデータ

自身のデータを使ってこのチュートリアルを試す場合、bcp では UTF-8 がサポートされないため、データには ASCII または UTF-16 エンコードを使用する必要があります。

PolyBase では UTF-8 がサポートされていますが、UTF-16 はまだサポートされていません。bcp と PolyBase を組み合わせて使用する場合は、SQL Server からエクスポートしたデータを UTF-8 に変換する必要があります。


## 1\.ターゲット テーブルを作成する

読み込み操作のターゲット テーブルとなるテーブルを SQL Data Warehouse 内に定義します。テーブル内の各列は、データ ファイルの各行のデータに対応する必要があります。

テーブルを作成するには、コマンド プロンプトを開き、sqlcmd.exe を使用して次のコマンドを実行します。


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


## 2\.ソース データ ファイルを作成する

メモ帳を開き、データの以下の行を新しいテキスト ファイルにコピーして、このファイルをローカルの一時ディレクトリに保存します (C:\\Temp\\DimDate2.txt)。このデータは ASCII 形式です。

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

(オプション) 自身のデータを SQL Server データベースからエクスポートするには、コマンド プロンプトを開き、次のコマンドを実行します。TableName、ServerName、DatabaseName、Username、および Password を自身の情報に置き換えてください。

```sql
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t ','
```



## 3\.データを読み込む
データを読み込むには、コマンド プロンプトを開き、次のコマンドを実行します。ここでは、ServerName、DatabaseName、Username、および Password を自身の情報に置き換えます。

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ','
```

次のコマンドを使用して、データが正しく読み込まれたことを確認します。

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

結果は次のようになります。

DateId |CalendarQuarter |FiscalQuarter
----------- |--------------- |-------------
20150101 |1 |3
20150201 |1 |3
20150301 |1 |3
20150401 |2 |4
20150501 |2 |4
20150601 |2 |4
20150701 |3 |1
20150801 |3 |1
20150801 |3 |1
20151001 |4 |2
20151101 |4 |2
20151201 |4 |2

## 4\.統計を作成する

SQL Data Warehouse は、統計の自動作成または自動更新をまだサポートしていません。最適なクエリ パフォーマンスを得るには、最初に読み込んだ後またはデータに大きな変更が加えられた後に、すべてのテーブルのすべての列で統計を作成することが重要です。統計の詳細については、[統計][]に関する記事を参照してください。

次のコマンドを実行して、新しく読み込んだテーブルの統計を作成します。

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## 5\.SQL Data Warehouse からのデータのエクスポート
前の手順で読み込んだデータは、SQL Data Warehouse からエクスポートすることができます。エクスポートするためのコマンドは、SQL Server からエクスポートするときとまったく同じです。

ただし、結果に違いがあります。データは SQL Data Warehouse 内の分散した場所に格納されているため、データをエクスポートすると、各コンピューティング ノードがそのデータを出力ファイルに書き込みます。出力ファイル内でのデータの順序は、入力ファイル内のデータの順序と異なる可能性が高くなります。

### テーブルをエクスポートしてその結果を比較する

エクスポートされたデータを表示するには、コマンド プロンプトを開き、次のコマンドを実行します。このとき、パラメーターを適切な情報に置き換えてください。ServerName は、Azure の論理 SQL Server の名前です。

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
新しいファイルを開き、データが正しくエクスポートされたことを確認できます。ファイル内のデータは次のテキストに一致していても、別の順序で並べ替えられている可能性があります。

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

### クエリの結果をエクスポートする

bcp の **queryout** 関数を使用すると、テーブル全体をエクスポートする代わりに、クエリの結果をエクスポートすることができます。

## 次のステップ
読み込みの概要については、「[Load data into SQL Data Warehouse (SQL Data Warehouse へのデータの読み込み)][]」を参照してください。開発に関するその他のヒントについては、「[SQL Data Warehouse development overview (SQL Data Warehouse の開発の概要)][]」をご覧ください。SQL Data Warehouse でのテーブルの作成の詳細については、[テーブルのデザイン][]に関する記事または [CREATE TABLE 構文][]に関するページを参照してください。

<!--Image references-->

<!--Article references-->

[Load data into SQL Data Warehouse (SQL Data Warehouse へのデータの読み込み)]: sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview (SQL Data Warehouse の開発の概要)]: sql-data-warehouse-overview-develop.md
[テーブルのデザイン]: sql-data-warehouse-develop-table-design.md
[統計]: sql-data-warehouse-develop-statistics.md

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE 構文]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft ダウンロード センター]: https://www.microsoft.com/download/details.aspx?id=36433

<!---HONumber=AcomDC_0511_2016-->