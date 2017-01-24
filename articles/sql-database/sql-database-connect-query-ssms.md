---
title: "SQL Database への接続 - SQL Server Management Studio | Microsoft Docs"
description: "SQL Server Management Studio (SSMS) を使用して Azure で SQL Database に接続する方法について説明します。 次に、TRANSACT-SQL (T-SQL) を使用して、サンプル クエリを実行します。"
metacanonical: 
keywords: "SQL データベースへの接続、SQL Server Management Studio"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 7cd2a114-c13c-4ace-9088-97bd9d68de12
ms.service: sql-database
ms.custom: development
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: sstein;carlrab
translationtype: Human Translation
ms.sourcegitcommit: 867f06c1fae3715ab03ae4a3ff4ec381603e32f7
ms.openlocfilehash: c0d085d03c1eebe0cff4f0ad48825991ea40619c


---
# <a name="connect-to-sql-database-with-sql-server-management-studio-and-execute-a-sample-t-sql-query"></a>SQL Server Management Studio を使用して SQL Database に接続し、T-SQL サンプル クエリを実行する
> [!div class="op_single_selector"]
> * [Visual Studio](sql-database-connect-query.md)
> * [SSMS](sql-database-connect-query-ssms.md)
> * [Excel](sql-database-connect-excel.md)
> 

この記事では、SQL Server Management Studio (SSMS) を使用して Azure SQL データベースに接続する方法について説明します。 正常に接続した後、単純な Transact-SQL (T-SQL) クエリを実行して、データベースとの通信を確認します。

[!INCLUDE [SSMS Install](../../includes/sql-server-management-studio-install.md)]

1. 最新バージョンの SSMS をまだインストールしていない場合は、[SQL Server Management Studio のダウンロード](https://msdn.microsoft.com/library/mt238290.aspx)に関するページで最新バージョンの SSMS をダウンロードしてインストールしてください。 SSMS は、最新の状態を保つために、新しいバージョンのダウンロードが可能になると、更新を求めるメッセージを表示します。

2. インストールしたら、Windows 検索ボックスに「**Microsoft SQL Server Management Studio**」と入力し、**Enter** キーを押して SSMS を開きます。

    ![SQL Server Management Studio](./media/sql-database-get-started/ssms.png)
3. [サーバーへの接続] ダイアログ ボックスで、SQL Server 認証を使用して SQL Server に接続するために必要な情報を入力します。

    ![[サーバーへの接続]](./media/sql-database-get-started/connect-to-server.png)
4. **[接続]**をクリックします。

    ![サーバーに接続されました](./media/sql-database-get-started/connected-to-server.png)
5. オブジェクト エクスプローラーで **[データベース]** を展開し、任意のデータベースを展開してそのデータベース内のオブジェクトを表示します。

    ![新しいサンプル データベースのオブジェクト (SSMS)](./media/sql-database-get-started/new-sample-db-objects-ssms.png)
6. このデータベースを右クリックし、**[新しいクエリ]** をクリックします。

    ![新しいサンプル データベースのクエリ (SSMS)](./media/sql-database-get-started/new-sample-db-query-ssms.png)
7. クエリ ウィンドウで、次のクエリを入力します。

   ```select * from sys.objects```
   
8.  ツール バーの **[実行]** をクリックすると、サンプル データベース内のすべてのシステム オブジェクトの一覧が返されます。

    ![新しいサンプル データベースのシステム オブジェクトに対するクエリ実行 (SSMS)](./media/sql-database-get-started/new-sample-db-query-objects-ssms.png)

> [!Tip]
> チュートリアルについては、[サーバー、サーバーレベルのファイアウォール規則、サンプル データベース、データベースレベルのファイアウォール規則の作成と SQL Server への接続に関する SQL Database のチュートリアル](sql-database-get-started.md)を参照してください。    
>

## <a name="next-steps"></a>次のステップ

- SQL Server で可能な方法とほぼ同じように、T-SQL ステートメントを使用して Azure にデータベースを作成して管理することもできます。 SQL Server で T-SQL を使用するのに慣れている場合は、「 [Azure SQL Database TRANSACT-SQL 情報](sql-database-transact-sql-information.md) 」で相違点の概要を参照してください。
- T-SQL を初めて使用する場合は、「[チュートリアル: TRANSACT-SQL ステートメントの作成](https://msdn.microsoft.com/library/ms365303.aspx)」と「[TRANSACT-SQL リファレンス (データベース エンジン)](https://msdn.microsoft.com/library/bb510741.aspx)」を参照してください。
- データベース ユーザーおよびデータベース ユーザー管理者の作成の概要については、 [Azure SQL Database セキュリティの概要](sql-database-get-started-security.md)
- SSMS の詳細については、「 [SQL Server Management Studio の使用](https://msdn.microsoft.com/library/ms174173.aspx)」を参照してください。




<!--HONumber=Dec16_HO2-->


