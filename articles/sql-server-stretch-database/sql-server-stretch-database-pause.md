<properties
	pageTitle="データ移行の一時停止および再開 (Stretch Database) | Microsoft Azure"
	description="Azure へのデータ移行を一時停止または再開する方法について説明します。"
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

# データ移行の一時停止および再開 (Stretch Database)

Azure へのデータ移行を一時停止または再開するには、SQL Server Management Studio でテーブルの**[Stretch]** を選択してから、**[一時停止]** を選択してデータ移行を一時停止するか、**[再開]** を選択してデータ移行を再開します。Transact-SQL を使用してデータ移行を一時停止または再開することもできます。

ローカル サーバー上の問題をトラブルシューティングするか、使用可能なネットワーク帯域幅を最大化する場合は、各テーブルでデータ移行を一時停止します。

## データ移行を一時停止する

### SQL Server Management Studio を使用してデータ移行を一時停止する

1.  SQL Server Management Studio のオブジェクト エクスプローラーで、データ移行を一時停止する Stretch が有効なテーブルを選択します。

2.  右クリックして **[Stretch]** を選択し、**[一時停止]** を選択します。

### Transact-SQL を使用してデータ移行を一時停止する
次のコマンドを実行します。

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>  
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = PAUSED ) ) ;  
GO
```

## データ移行を再開する

### SQL Server Management Studio を使用してデータ移行を再開する

1.  SQL Server Management Studio のオブジェクト エクスプローラーで、データ移行を再開する Stretch が有効なテーブルを選択します。

2.  右クリックして **[Stretch]** を選択し、**[再開]** を選択します。

### Transact-SQL を使用してデータ移行を再開する
次のコマンドを実行します。

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>   
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = OUTBOUND ) ) ;  
 GO
```

## 移行がアクティブか一時停止しているかを確認する

### SQL Server Management Studio を使用して、移行がアクティブか一時停止しているかを確認する
SQL Server Management Studio で **Stretch Database Monitor** を開き、**[移行状態]** 列の値を確認します。詳細については、「[データ移行の監視とトラブルシューティング](sql-server-stretch-database-monitor.md)」をご覧ください。

### Transact-SQL を使用して、移行がアクティブか一時停止しているかを確認する
カタログ ビュー **sys.remote\_data\_archive\_tables** に対してクエリを実行し、**is\_migration\_paused** 列の値を確認します。詳細については、「[sys.remote\_data\_archive\_tables](https://msdn.microsoft.com/library/dn935003.aspx)」をご覧ください。

## 関連項目

[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx) [データ移行の監視とトラブルシューティング](sql-server-stretch-database-monitor.md)

<!---HONumber=AcomDC_0622_2016-->