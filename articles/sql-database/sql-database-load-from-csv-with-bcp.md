<properties
   pageTitle="CSV ファイルから Azure SQL Database へのデータの読み込み (bcp) | Microsoft Azure"
   description="データ サイズが小さい場合は、bcp を使用して Azure SQL Database にデータをインポートできます。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/30/2016"
   ms.author="carlrab"/>


# CSV から Azure SQL Data Warehouse へのデータの読み込み (フラット ファイル)

bcp コマンドライン ユーティリティを使用して、CSV ファイルから Azure SQL Database にデータをインポートできます。

## 開始する前に

### 前提条件

このチュートリアルを進めるには、次が必要です。

- Azure SQL Database の論理サーバーとデータベース
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
    ;
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


## 次のステップ

SQL Server データベースを移行するには、[SQL Server データベースの移行](sql-database-cloud-migrate.md)に関するページを参照してください。

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft ダウンロード センター]: https://www.microsoft.com/download/details.aspx?id=36433

<!---HONumber=AcomDC_0803_2016-->