<properties
	pageTitle="Azure SQL Database でデータベースのパフォーマンスのトラブルシューティングを行います。"
	description="データベース パフォーマンスのトラブルシューティングを行うための簡単な手順。"
	services="sql-database"
	documentationCenter=""
	authors="v-shysun"
	manager="msmets"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="v-shysun"/>

# Azure SQL Database を使用してデータベースのパフォーマンスのトラブルシューティングを行う
単一データベースの[サービス レベル](sql-database-service-tiers.md)を変更するか、エラスティック データベース プールの eDTU を増やすことで、いつでもパフォーマンスを高めることができますが、最初にクエリのパフォーマンスを改善または最適化する機会を特定する必要があります。データベースのパフォーマンスが悪い一般的な理由は、インデックスの欠落や最適化が不十分なクエリです。

## データベースのパフォーマンスを評価し、チューニングする手順
1.	[Azure ポータル](https://portal.azure.com)で、**[SQL データベース]** をクリックし、データベースを選択してから、監視グラフを使用して上限に近づいているリソースを見つけます。DTU 消費は、既定で表示されます。**[編集]** をクリックして、表示される時間の範囲と値を変更します。
2.	[[Query Performance Insight]](sql-database-query-performance.md) を使用して DTU を使用するクエリを評価し、その後 [[Index Advisor]](sql-database-index-advisor.md) を使用してインデックスを推奨し、作成します。
3.	動的管理ビュー (DMV)、拡張イベント (Xevent)、および SSMS のクエリ ストアを使用すると、リアルタイムでパフォーマンス パラメーターを取得できます。詳細な監視とチューニングに関するヒントについては、[パフォーマンス ガイダンス トピック](sql-database-performance-guidance.md)を参照してください。

## その他のリソースを含むデータベースのパフォーマンスを改善する手順
1.	単一のデータベースの場合は、[サービス レベルの変更](sql-database-scale-up.md)をオンデマンドで行うことで、データベースのパフォーマンスを改善できます。
2.	複数のデータベースの場合は、リソースを自動的にスケーリングするための[エラスティック データベース プール](sql-database-elastic-pool-guidance.md)の使用を検討してください。

パフォーマンスの問題が続く場合は、サポートに連絡してサポート ケースを登録してください。

<!---HONumber=AcomDC_1217_2015-->