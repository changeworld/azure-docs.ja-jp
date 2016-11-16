---
title: "Azure SQL Data Warehouse に対するクエリ (sqlcmd) | Microsoft Docs"
description: "sqlcmd コマンド ライン ユーティリティを使用して Azure SQL Data Warehouse に対してクエリを実行します。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 6e2b69e5-4806-4e91-9ea1-e2b63bf28c46
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1f2cf8003e46a1df30810a2594bc1d380bc13bcf


---
# <a name="query-azure-sql-data-warehouse-sqlcmd"></a>Azure SQL Data Warehouse に対するクエリ (sqlcmd)
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

このチュートリアルでは、[sqlcmd][sqlcmd] コマンド ライン ユーティリティを使用して Azure SQL Data Warehouse に対してクエリを実行します。  

## <a name="1-connect"></a>1.接続
[sqlcmd][sqlcmd] の使用を開始するには、コマンド プロンプトを開いて「**sqlcmd**」と入力し、続けて SQL Data Warehouse データベースの接続文字列を入力します。 接続文字列では、次のパラメーターが必要になります。

* **サーバー (-S):** サーバー。`<`Server Name`>`.database.windows.net の形式で指定します。
* **データベース (-d):** データベース名。
* **引用符で囲まれた ID の有効化 (-I):** SQL Data Warehouse インスタンスに接続するには、引用符で囲まれた ID を有効にする必要があります。

SQL Server 認証を使用するには、ユーザー名とパスワードのパラメーターを追加する必要があります。

* **ユーザー (-U):** サーバーのユーザー。`<`User`>` の形式で指定します。
* **パスワード (-P):** ユーザーに関連付けられているパスワード。

たとえば、接続文字列は次のようになります。

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Azure Active Directory 統合認証を使用するには、Azure Active Directory パラメーターを追加する必要があります。

* **Azure Active Directory Authentication (-G):** Azure Active Directory を認証に使用します。

たとえば、接続文字列は次のようになります。

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> Active Directory を使用して認証を行うには、 [Azure Active Directory 認証を有効にする](sql-data-warehouse-authentication.md) 必要があります。
> 
> 

## <a name="2-query"></a>2.クエリ
接続後、インスタンスに対してサポートされているすべての Transact-SQL ステートメントを発行できます。  この例では、クエリは対話モードで送信されます。

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

以下の例は、-Q オプションを使用する方法または SQL を sqlcmd にパイプ処理で渡す方法を使用して、バッチ モードでクエリを実行する方法を示しています。

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## <a name="next-steps"></a>次のステップ
sqlcmd で使用可能なオプションの詳細については、 [sqlcmd に関するドキュメント][sqlcmd] を参照してください。

<!--Image references-->

<!--Article references-->

<!--MSDN references--> 
[sqlcmd]: https://msdn.microsoft.com/library/ms162773.aspx
[Azure ポータル]: https://portal.azure.com

<!--Other Web references-->



<!--HONumber=Nov16_HO2-->


