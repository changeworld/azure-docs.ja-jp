<properties
	pageTitle="Stretch Database を無効にしてリモート データを戻す | Microsoft Azure"
	description="テーブルの Stretch Database を無効にし、必要に応じてリモート データを戻す方法について説明します。"
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
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# Stretch Database を無効にしてリモート データを戻す

テーブルの Stretch Database を無効にするには、SQL Server Management Studio でテーブルに **[Stretch]** を選択します。次のいずれかを選択します。

-   **無効にする | Azure からデータを戻す**。Azure から SQL Server にテーブルのリモート データをコピーし、テーブルの Stretch Database を無効にします。この操作にはデータ転送コストが発生し、キャンセルできません。

-   **無効にする | Azure にデータを残す**。テーブルの Stretch Database を無効にします。Azure のテーブルのリモート データを破棄します。

テーブルの Stretch Database を無効にすると、データ移行が停止し、クエリ結果にリモート テーブルからの結果が含まれなくなります。

Transact-SQL を利用し、テーブルまたはデータベースの Stretch Database を無効にすることもできます。

データ移行を一時停止する場合、「[Pause and resume Stretch Database (Stretch Database を一時停止し、再開する)](sql-server-stretch-database-pause.md)」を参照してください。

**注** Stretch を無効にしても、リモート オブジェクトは削除されません。リモート テーブルまたはリモート データベースを削除する場合は、Microsoft Azure 管理ポータルを使用して削除する必要があります。

## テーブルの Stretch Database を無効にする

### SQL Server Management Studio を使用し、テーブルの Stretch Database を無効にします。

1.  SQL Server Management Studio のオブジェクト エクスプローラーで、Stretch Database を無効にするテーブルを選択します。

2.  右クリックして **[Stretch]** を選択し、次のいずれかを選択します。

    -   **無効にする | Azure からデータを戻す**。Azure から SQL Server にテーブルのリモート データをコピーし、テーブルの Stretch Database を無効にします。このコマンドはキャンセルできません。

        この操作にはデータ転送コストが発生します。詳細については、「[データ転送の料金詳細](https://azure.microsoft.com/pricing/details/data-transfers/)」を参照してください。

        Azure から SQL Server にすべてのリモート データがコピーされると、テーブルの Stretch が無効になります。

    -   **無効にする | Azure にデータを残す**。テーブルの Stretch Database を無効にします。Azure のテーブルのリモート データを破棄します。

    Stretch を無効にしても、リモート テーブルは削除されません。リモート テーブルを削除する場合は、Microsoft Azure 管理ポータルを使用して削除する必要があります。

### Transact-SQL を利用し、テーブルの Stretch Database を無効にする

-   テーブルの Stretch を無効にして Azure SQL Database から SQL Server にテーブルのリモート データをコピーするには、次のコマンドを実行します。このコマンドはキャンセルできません。

    ```tsql
    ALTER TABLE <table name>
       SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = OUTBOUND ) ) ;
    ```
    この操作にはデータ転送コストが発生します。詳細については、「[データ転送の料金詳細](https://azure.microsoft.com/pricing/details/data-transfers/)」を参照してください。

    Azure から SQL Server にすべてのリモート データがコピーされると、テーブルの Stretch が無効になります。

-   テーブルの Stretch を無効にしてリモート データを破棄するには、次のコマンドを実行します。

    ```tsql
    ALTER TABLE <table_name>
       SET ( REMOTE_DATA_ARCHIVE = OFF_WITHOUT_DATA_RECOVERY ( MIGRATION_STATE = PAUSED ) ) ;
    ```
Stretch を無効にしても、リモート テーブルは削除されません。リモート テーブルを削除する場合は、Microsoft Azure 管理ポータルを使用して削除する必要があります。

## データベースの Stretch Database を無効にする
データベースの Stretch Database を無効にする前に、データベースの個々の Stretch 対応テーブルで Stretch Database を無効にする必要があります。

Stretch を無効にしても、リモート データベースは削除されません。リモート データベースを削除する場合は、Microsoft Azure 管理ポータルを使用して削除する必要があります。

### SQL Server Management Studio を使用し、データベースの Stretch Database を無効にする

1.  SQL Server Management Studio のオブジェクト エクスプローラーで、Stretch Database を無効にするデータベースを選択します。

2.  右クリックして **[タスク]** を選択し、**[Stretch]** を選択し、**[無効化]** を選択します。

### Transact-SQL を利用し、データベースの Stretch Database を無効にする
次のコマンドを実行します。

```tsql
ALTER DATABASE <database name>
    SET REMOTE_DATA_ARCHIVE = OFF ;
```

## 関連項目
[ALTER DATABASE SET オプション (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx) [Stretch Database を一時停止し、再開する](sql-server-stretch-database-pause.md)

<!---HONumber=AcomDC_0309_2016-->