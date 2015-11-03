<properties
	pageTitle="XTP インメモリ ストレージの監視 |Microsoft Azure"
	description="XTP インメモリ ストレージの使用量と容量を推定し、監視します。また、容量不足エラー 41805 を解決します。"
	services="sql-database"
	documentationCenter=""
	authors="jodebrui"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="10/28/2015"
	ms.author="jodebrui"/>


# XTP インメモリ ストレージを監視する

サービス階層とストレージの詳細については、「[SQL Database のサービス階層](sql-database-service-tiers.md)」を参照してください

[インメモリ](sql-database-in-memory.md)を使用している場合、メモリ最適化テーブルおよびテーブル変数内のデータは、XTP インメモリ ストレージに格納されています。Premium サービス階層には、それぞれにインメモリ ストレージの最大サイズがあります。この上限を超過すると、挿入操作や更新操作が (エラー 41805 で) 失敗することがあります。その場合は、データを削除してメモリを解放するか、データベースのパフォーマンス階層をアップグレードする必要があります。

## データがインメモリ ストレージの上限に収まるかどうかを判断する

メモリ最適化テーブルのメモリ必要量の推定は、Azure SQL Database で SQL Server の要件を推定する場合と同じように行います。少し時間をとって、[MSDN](https://msdn.microsoft.com/library/dn282389.aspx) でメモリ最適化テーブルのメモリ必要量の推定について確認してください。

テーブル行とテーブル変数行、およびインデックスは、最大ユーザー データ サイズにカウントされるので注意してください。また、テーブル全体とそのインデックスの新しいバージョンを作成するには、ALTER TABLE に十分な領域が必要になります。

##### Premium サービス階層の最大 XTP インメモリ ストレージ サイズ:



Azure [ポータル](http://portal.azure.com/)でインメモリ ストレージの使用量を監視できます。

- [データベース] ブレードの [リソース使用率] ボックスで [編集] をクリックします。
- [XTP インメモリ ストレージの使用率] メトリックを選択します。

または、次のクエリを使用して、インメモリ ストレージの使用率を表示します。

    select xtp_storage_percent from sys.dm_db_resource_stats

## メモリ不足状況の修正 - エラー 41805

メモリが不足すると、INSERT、UPDATE、および CREATE 操作が失敗し、エラー メッセージ 41805 が表示されます。

エラー メッセージ 41805 は、メモリ最適化テーブルとテーブル変数が最大サイズを超えたことを示しています。

このエラーを解決するには、次のいずれかを実行します。


- 従来のディスク ベース テーブルにデータをオフロードするなどして、メモリ最適化テーブルからデータを削除します。
- メモリ最適化テーブルにデータを残す必要がある場合は、十分なインメモリ ストレージがあるサービス階層にアップグレードします。

## 次のステップ
「[動的管理ビューを使用した Azure SQL Database の監視](sql-database-monitoring-with-dmvs.md)」について確認する

<!---HONumber=Nov15_HO1-->