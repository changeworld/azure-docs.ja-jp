<properties
   pageTitle="SQLCMD を使用したクエリ | Microsoft Azure"
   description="SQLCMD を使用して Data Warehouse に対するクエリを実行します。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/09/2016"
   ms.author="mausher;barbkess;sonyama"/>

# SQLCMD を使用したクエリ

> [AZURE.SELECTOR]
- [Power BI][]
- [Azure Machine Learning][]
- [SQLCMD][]

このチュートリアルでは、sqlcmd.exe ユーティリティを使用して Azure SQL Data Warehouse に対するクエリを実行する方法を示します。

## 前提条件

+ [sqlcmd.exe][] をダウンロードするには、[Microsoft Command Line Utilities 11 for SQL Server][] のページを参照してください。

## 接続

sqlcmd の使用を開始するには、コマンド プロンプトを開き、「**sqlcmd**」と入力し、続けて SQL Data Warehouse データベースの接続文字列を入力します。接続文字列では、次の必須パラメーターが必要になります。

+ **サーバー (-S):** サーバー。`<`Server Name`>`.database.windows.net の形式で指定します
+ **データベース (-d):** データベース名。
+ **ユーザー (-U):** サーバーのユーザー。`<`User`>` の形式で指定します
+ **パスワード (-P):** ユーザーに関連付けられているパスワード。
+ **引用符で囲まれた ID の有効化 (-I):** SQL Data Warehouse インスタンスに接続するには、引用符で囲まれた ID を有効にする必要があります。

たとえば、接続文字列は次のようになります。

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

> [AZURE.NOTE] 現在、SQL Data Warehouse に接続するには、引用符で囲まれた識別子を有効にする -I オプションが必要になります。

## クエリ

接続後、インスタンスに対してサポートされているすべての Transact-SQL ステートメントを発行できます。この例では、クエリは対話モードで送信されます。

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

以下の例は、-Q オプションを使用する方法または SQL を sqlcmd にパイプ処理で渡す方法を使用して、バッチ モードでクエリを実行する方法を示しています。

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
C:\>"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## 次のステップ

sqlcmd オプションの完全な一覧については、[sqlcmd のドキュメント][sqlcmd.exe]を参照してください。

<!--Articles-->
[connecting with PowerBI]: ./sql-data-warehouse-integrate-power-bi.md
[Visual Studio]: ./sql-data-warehouse-get-started-connect.md
[Power BI]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md
[Azure Machine Learning]: ./sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md
[SQLCMD]: ./sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other-->
[sqlcmd.exe]: https://msdn.microsoft.com/ja-JP/library/ms162773.aspx
[Microsoft Command Line Utilities 11 for SQL Server]: http://go.microsoft.com/fwlink/?LinkId=321501
[Azure portal]: https://portal.azure.com

<!--Image references-->

<!---HONumber=AcomDC_0615_2016-->