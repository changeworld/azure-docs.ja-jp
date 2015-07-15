<properties
   pageTitle="SQL Data Warehouse | Microsoft Azure での名前変更"
   description="ソリューションを開発するための Azure SQL Data Warehouse でのオブジェクトとデータベースの名前変更に関するヒント。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/26/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# SDL Data Warehouse での名前変更
SQL Server は、オブジェクトとデータベースの名前変更を、ストアドプロシージャの sp_rename と sp_renamedb をそれぞれ使用してサポートします。

SQL Data Warehouse は、同じ目標を実現するために、DDL 構文を使用します。DDL コマンドは、RENAME OBJECT と RENAME DATABASE です。

## オブジェクトの名前変更

これは、名前は名前変更されるオブジェクトにのみ影響します 。 名前変更の伝達がないことを理解しておくことは重要です。つまり、オブジェクトの古い名前を使用するビューは、古い名前のオブジェクトが作成されるまで無効になります。その結果、 `RENAME OBJECT` がペアで表示されます。

```
RENAME OBJECT product.item to item_old;
RENAME OBJECT product.item_new to item;
```

## データベースの名前変更

データベースの名前変更は、オブジェクトの名前変更と非常に類似しています。

```
RENAME DATABASE AdventureWorks TO Contoso;
```

オブジェクトやデータベースは、他のユーザーがそれらに接続していたり使用している場合は、名前変更ができないことを覚えておくことは重要です。最初に、開いているセッションを終了する必要があります。セッションを終了するには、 [KILL][] コマンドを使用する必要があります。KILL を使用する際は注意してください。いったん実行されると、対象のセッションは終了し、コミットされていない作業はロールバックされます。

> [AZURE.NOTE]SQL Data Warehouse のセッションで KILL コマンドを呼び出す際には、接頭辞の ’SID’ とセッション番号を付加してください。たとえば、KILL 'SID1234' はセッション1234 を強制終了します (それを実行する正当なアクセス許可を持っていると仮定した場合)。

## セッションの強制終了
データベースの名前を変更するのには、SQL Data Warehouse に接続されているセッションを強制終了する必要があります。次のクエリでは、接続をクリアにする(現在のセッションは保存) ための、KILL コマンドのリストを生成します。

```
SELECT 'KILL '''+session_id+''''
FROM	sys.dm_pdw_exec_requests r
WHERE r.session_id <> SESSION_ID()
AND EXISTS
(	SELECT 	*
	FROM 	sys.dm_pdw_lock_waits w
	WHERE 	r.session_id = w.session_id
)
GROUP BY session_id
;
```

## スキーマの切り替え
オブジェクトが属するスキーマを変更したい場合は、`ALTER SCHEMA` を使用して達成できます。

```
ALTER SCHEMA dbo TRANSFER OBJECT::product.item;
```


## 次のステップ
開発に関するその他のヒントについては、「[開発の概要][]」を参照してください。

<!--Image references-->

<!--Article references-->
[開発の概要]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[KILL]: https://msdn.microsoft.com/ja-jp/library/ms173730.aspx

<!--Other Web references-->
[Azure management portal]: http://portal.azure.com/

<!---HONumber=July15_HO1-->