<properties
   pageTitle="SQL Data Warehouse | Microsoft Azure での名前変更"
   description="ソリューションを開発するための Azure SQL Data Warehouse でのオブジェクトとデータベースの名前変更に関するヒント。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/21/2015"
   ms.author="twounder;JRJ@BigBangData.co.uk;barbkess"/>

# SDL Data Warehouse での名前変更
SQL Server でデータベースの名前を変更するには、ストアド プロシージャ ```sp_renamedb``` を使用します。SQL Data Warehouse は、同じ目標を実現するために、DDL 構文を使用します。DDL コマンドは ```RENAME DATABASE``` です。

## データベースの名前変更

データベースの名前を変更するには、次のコマンドを実行します。

```
RENAME DATABASE AdventureWorks TO Contoso;
```

オブジェクトやデータベースは、他のユーザーがそれらに接続していたり使用している場合は、名前変更ができないことを覚えておくことは重要です。最初に、開いているセッションを終了する必要があります。セッションを終了するには、 [KILL](https://msdn.microsoft.com/library/ms173730.aspx) コマンドを使用する必要があります。```KILL``` を使用するときは注意してください。いったん実行されると、対象のセッションは終了し、コミットされていない作業はロールバックされます。

> [AZURE.NOTE]SQL Data Warehouse のセッションで KILL コマンドを呼び出す際には、接頭辞の ’SID’ とセッション番号を付加してください。たとえば、```KILL 'SID1234'``` はセッション1234 を強制終了します (それを実行する正当なアクセス許可を持っている場合)。

## セッションの強制終了
データベースの名前を変更するのには、SQL Data Warehouse に接続されているセッションを強制終了する必要があります。次のクエリでは、接続をクリアにする(現在のセッションは保存) ための、KILL コマンドのリストを生成します。

```
SELECT
    'KILL ''' + session_id + ''''
FROM
    sys.dm_pdw_exec_requests r
WHERE
    r.session_id <> SESSION_ID()
    AND EXISTS
    (
        SELECT
            *
        FROM
            sys.dm_pdw_lock_waits w
        WHERE
            r.session_id = w.session_id
    )
GROUP BY
    session_id;
```

## スキーマの切り替え
オブジェクトが属するスキーマを変更したい場合は、`ALTER SCHEMA` を使用して達成できます。

```
ALTER SCHEMA dbo TRANSFER OBJECT::product.item;
```

## 次のステップ
開発のその他のヒントについては、[開発の概要][]に関するページをご覧ください。

<!--Image references-->

<!--Article references-->
[開発の概要]: sql-data-warehouse-overview-develop.md

<!---HONumber=Oct15_HO4-->