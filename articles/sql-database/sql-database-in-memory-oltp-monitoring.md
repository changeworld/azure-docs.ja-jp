<properties
	pageTitle="XTP インメモリ ストレージの監視 | Microsoft Azure"
	description="XTP インメモリ ストレージの使用量と容量を推定し、監視します。また、容量不足エラー 41823 を解決します。"
	services="sql-database"
	documentationCenter=""
	authors="jodebrui"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/18/2016"
	ms.author="jodebrui"/>


# インメモリ OLTP ストレージの監視

[インメモリ OLTP](sql-database-in-memory.md) を使用している場合、メモリ最適化テーブルおよびテーブル変数内のデータは、インメモリ OLTP ストレージに格納されています。Premium サービス レベルには、それぞれインメモリ OLTP ストレージの最大サイズがあります。詳細については、[SQL Database のサービス レベル](sql-database-service-tiers.md#service-tiers-for-single-databases)に関する記事をご覧ください。この上限を超過すると、挿入操作や更新操作が (エラー 41823 で) 失敗することがあります。その場合は、データを削除してメモリを解放するか、データベースのパフォーマンス階層をアップグレードする必要があります。

## データがインメモリ ストレージの上限に収まるかどうかを判断する

ここでは、ストレージの上限を判断します。各種 Premium サービス階層のストレージ上限については、[SQL Database のサービス階層に関する記事](sql-database-service-tiers.md#service-tiers-for-single-databases)を参照してください。

メモリ最適化テーブルのメモリ必要量の推定は、Azure SQL Database で SQL Server の要件を推定する場合と同じように行います。少し時間をとって、[MSDN](https://msdn.microsoft.com/library/dn282389.aspx) でメモリ最適化テーブルのメモリ必要量の推定について確認してください。

テーブル行とテーブル変数行、およびインデックスは、最大ユーザー データ サイズにカウントされるので注意してください。また、テーブル全体とそのインデックスの新しいバージョンを作成するには、ALTER TABLE に十分な領域が必要になります。

## 監視とアラート

Azure [ポータル](https://portal.azure.com/)で、インメモリ ストレージの使用量を[パフォーマンス階層のストレージ上限](sql-database-service-tiers.md#service-tiers-for-single-databases)に対するパーセンテージとして監視できます。

- [データベース] ブレードの [リソース使用率] ボックスで [編集] をクリックします。
- メトリック `In-Memory OLTP Storage percentage` を選択します。
- アラートを追加するには、[リソース使用率] チェック ボックスをオンにして [メトリック] ブレードを開き、[アラートの追加] をクリックします。

または、次のクエリを使用して、インメモリ ストレージの使用率を表示します。

    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats


## メモリ不足状況の修正 - エラー 41823

メモリが不足すると、INSERT、UPDATE、および CREATE 操作が失敗し、エラー メッセージ 41823 が表示されます。

エラー メッセージ 41823 は、メモリ最適化テーブルとテーブル変数が最大サイズを超えたことを示しています。

このエラーを解決するには、次のいずれかを実行します。


- 従来のディスク ベース テーブルにデータをオフロードするなどして、メモリ最適化テーブルからデータを削除します。
- メモリ最適化テーブルにデータを残す必要がある場合は、十分なインメモリ ストレージがあるサービス階層にアップグレードします。

## 次のステップ
その他のリソースとして「[動的管理ビューを使用した Azure SQL Database の監視](sql-database-monitoring-with-dmvs.md)」を参照する

<!---HONumber=AcomDC_0720_2016-->