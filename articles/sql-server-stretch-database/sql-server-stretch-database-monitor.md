<properties
	pageTitle="データ移行の監視とトラブルシューティング (Stretch Database) | Microsoft Azure"
	description="データ移行の状態を監視する方法について説明します。"
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="douglasl"/>

# データ移行の監視とトラブルシューティング (Stretch Database)

Stretch Database Monitor でデータ移行を監視するには、SQL Server Management Studio でデータベースに **[タスク]、[Stretch]、[監視]** を選択します。

## Stretch Database Monitor でデータ移行の状態を確認する
SQL Server Management Studio でデータベースに **[タスク]、[Stretch]、[監視]** を選択し、Stretch Database Monitor を開き、データ移行を監視します。

-   モニターの最上部には、Stretch が有効な SQL Server データベースとリモートの Azure データベースの両方に関する全般的な情報が表示されます。

-   モニターの最下部には、データベース内の Stretch が有効な各テーブルのデータ移行の状態が表示されます。

![Stretch Database Monitor][StretchMonitorImage1]

## <a name="Migration"></a>動的管理ビューでデータ移行の状態を確認する
動的管理ビューの **sys.dm\_db\_rda\_migration\_status** を開き、移行されたデータのバッチ数と行数を確認します。詳細については、「[sys.dm\_db\_rda\_migration\_status (Transact-SQL)](https://msdn.microsoft.com/library/dn935017.aspx)」を参照してください。

## <a name="Firewall"></a>データ移行のトラブルシューティング

**Stretch を有効にしたテーブルの行が Azure に移行されません。何が問題なのでしょうか。**

移行に影響する可能性があるいくつかの問題があります。次のことを確認してください。

-   SQL Server コンピューターのネットワーク接続を確認します。

-   Azure ファイアウォールが、SQL Server からリモート エンドポイントへの接続をブロックしていないことを確認します。

-   最新バッチの状態については、動的管理ビュー **sys.dm\_db\_rda\_migration\_status** を確認します。エラーが発生した場合は、バッチの error\_number、error\_state、および error\_severity の値を確認します。

    -   ビューに関する詳細については、「[sys.dm\_db\_rda\_migration\_status (Transact-SQL)](https://msdn.microsoft.com/library/dn935017.aspx)」をご覧ください。

    -   SQL Server エラー メッセージの内容に関する詳細については、「[sys.messages (Transact-SQL)](https://msdn.microsoft.com/library/ms187382.aspx)」をご覧ください。

**Azure ファイアウォールがローカル サーバーからの接続をブロックします。**

Azure サーバーの Azure ファイアウォール設定にルールを追加して、SQL Server がリモートの Azure サーバーと通信できるようにする必要があります。

## 関連項目

[Stretch Database の管理とトラブルシューティング](sql-server-stretch-database-manage.md)

<!--Image references-->
[StretchMonitorImage1]: ./media/sql-server-stretch-database-monitor/StretchDBMonitor.png

<!---HONumber=AcomDC_0615_2016-->