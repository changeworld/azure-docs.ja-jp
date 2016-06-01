<properties
	pageTitle="Stretch Database の管理とトラブルシューティング | Microsoft Azure"
	description="Stretch Database の管理とトラブルシューティングの方法について説明します。"
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
	ms.date="05/17/2016"
	ms.author="douglasl"/>

# Stretch Database の管理とトラブルシューティング

Stretch Database を管理し、問題を解決するには、このトピックで説明するツールと方法を利用します。

## <a name="LocalInfo"></a>Stretch Database が有効になっているローカルのデータベースとテーブルに関する情報を取得する
Stretch が有効になっている SQL Server のデータベースとテーブルに関する情報を確認するには、カタログ ビューの **sys.databases** と **sys.tables** を開きます。詳細については、「[sys.databases (Transact-SQL)](https://msdn.microsoft.com/library/ms178534.aspx)」と「[sys.tables (Transact-SQL)](https://msdn.microsoft.com/library/ms187406.aspx)」を参照してください。

## <a name="RemoteInfo"></a>Stretch Database で使用されるリモートのデータベースとテーブルに関する情報を取得する
移行されたデータが保存されているリモートのデータベースとテーブルに関する情報を確認するには、カタログ ビューの **sys.remote\_data\_archive\_databases** と **sys.remote\_data\_archive\_tables** を開きます。詳細については、「[sys.remote\_data\_archive\_databases (Transact-SQL)](https://msdn.microsoft.com/library/dn934995.aspx)」と「[sys.remote\_data\_archive\_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx)」を参照してください。

## テーブルに適用されたフィルター述語を確認する
カタログ ビュー **sys.remote\_data\_archive\_tables** を開き、**filter\_predicate** 列の値を確認します。値が null の場合、テーブル全体が移行の対象になります。詳細については、「[sys.remote\_data\_archive\_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx)」を参照してください。

## <a name="Migration"></a>データ移行の状態を確認する
SQL Server Management Studio でデータベースに **[タスク]、[Stretch]、[監視]** を選択し、Stretch Database Monitor でデータ移行を監視します。詳細については、「[Monitor and troubleshoot data migration (Stretch Database) (データ移行の監視とトラブルシューティング (Stretch Database))](sql-server-stretch-database-monitor.md)」を参照してください。

あるいは、動的管理ビューの **sys.dm\_db\_rda\_migration\_status** を開き、移行されたデータのバッチ数と行数を確認します。

## Azure のパフォーマンス レベルを上げ、インデックス作成など、リソースが集中的に利用される操作に対応する
Stretch Database に構成された大規模テーブルでインデックスを作成、再作成、再編成するとき、その操作が続く間、対応するリモート データベースのパフォーマンス レベルを上げることを検討してください。

## リモート テーブルのスキーマは変更しないでください。
Stretch Database に構成された SQL Server テーブルに関連付けられているリモート Azure テーブルのスキーマは変更しないでください。特に、列の名前とデータ型は変更しないでください。Stretch Database 機能では、さまざまな場面で、SQL Server テーブルのスキーマとの関連でリモート テーブルのスキーマを推測します。リモート スキーマを変更すると、Stretch Database は変更されたテーブルの動作を停止します。

## <a name="Firewall"></a>データ移行のトラブルシューティング
トラブルシューティングのヒントについては、「[Monitor and troubleshoot data migration (Stretch Database) (データ移行の監視とトラブルシューティング (Stretch Database))](sql-server-stretch-database-monitor.md)」を参照してください。

## クエリ パフォーマンスのトラブルシューティング
**Stretch 対応テーブルが含まれるクエリが遅いです。** Stretch 対応テーブルが含まれるクエリのパフォーマンスは、テーブルの Stretch を有効にする前に比べて遅くなります。クエリのパフォーマンスが大幅に低下する場合、次の問題が考えられます。

-   ご利用の Azure サーバーと SQL Server の地理的リージョンが異なっていませんか。 Azure サーバーと SQL Server の地理的リージョンを同じに設定し、ネットワークの待ち時間を減らしてください。

-   ネットワークの状態が低下している可能性があります。ネットワーク管理者に問い合わせ、最近、問題や機能停止が発生していないか確認してください。

## 関連項目

[Stretch Database の監視](sql-server-stretch-database-monitor.md)

[Stretch 対応データベースをバックアップし、復元します。](sql-server-stretch-database-backup.md)

<!---HONumber=AcomDC_0518_2016-->