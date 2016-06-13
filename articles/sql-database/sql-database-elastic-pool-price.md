<properties
	pageTitle="SQL Database エラスティック プールの価格とパフォーマンス"
	description="エラスティック データベース プール固有の価格情報。"
	services="sql-database"
	documentationCenter=""
	authors="srinia"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="05/27/2016"
	ms.author="srinia"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# エラスティック データベース プールの課金と価格情報

エラスティック データベース プールは、次の特性ごとに課金されます。

- エラスティック プールは、プールにデータベースがない場合でも、その作成時に課金されます。
- エラスティック プールは 1 時間ごとに課金されます。これは、シングル データベースのパフォーマンス レベルと同じ使用状況測定の頻度です。
- エラスティック プールが新しい eDTU 量にサイズ変更されると、サイズ変更操作が完了するまでは新しい eDTU 量に応じた課金はされません。これは、スタンドアロン データベースのパフォーマンス レベルを変更する場合と同様のパターンに従っています。


- エラスティック プールの価格は、プールの eDTU 数に基づきます。エラスティック プールの価格は、内部のエラスティック データベースの数および使用率とは関係ありません。
- 価格は、(プールの eDTU 数) x (eDTU あたりの単価) で計算されます。

エラスティック プールの eDTU 単価は、同じサービス階層のスタンドアロン データベースの DTU 単価よりも高くなります。詳細については、「[SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)」をご覧ください。


eDtu およびサービス レベルについては、[SQL Database のオプションとパフォーマンス](sql-database-service-tiers.md)に関するページをご覧ください。

## 次のステップ

- [エラスティック データベース プールを作成します](sql-database-elastic-pool-create-portal.md)
- [エラスティック データベース プールの監視、管理、およびサイズ設定](sql-database-elastic-pool-manage-portal.md)
- [SQL Database のオプションとパフォーマンス: 各サービス階層で使用できる内容について理解します](sql-database-service-tiers.md)
- [エラスティック データベース プールに適したデータベースを識別する Powershell スクリプト](sql-database-elastic-pool-database-assessment-powershell.md)

<!---HONumber=AcomDC_0601_2016-->