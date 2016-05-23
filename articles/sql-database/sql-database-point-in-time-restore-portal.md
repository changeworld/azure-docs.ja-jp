<properties
	pageTitle="以前の時点への Azure SQL Database の復元 (Azure ポータル) | Microsoft Azure"
	description="以前の時点に Azure SQL Database を復元します。"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="05/10/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Azure ポータルで以前の時点に Azure SQL Database を復元する


> [AZURE.SELECTOR]
- [Azure ポータル](sql-database-point-in-time-restore-portal.md)
- [PowerShell](sql-database-point-in-time-restore-powershell.md)

この記事では、Azure ポータルを使用して以前の時点にデータベースを復元する方法について説明します。

セルフサービス機能である[**ポイントインタイム リストア**](sql-database-point-in-time-restore.md)を使用すると、すべてのデータベースに対して自動作成されたバックアップから、データベースのリテンション期間内の任意のポイントにデータベースを復元できます。データベースのリテンション期間の詳細については、[ビジネス継続性の概要](sql-database-business-continuity.md)に関するページをご覧ください。

## 以前の時点に復元するデータベースの選択

Azure ポータルでデータベースを復元するには、次の手順を実行します。

1.	[Azure ポータル](https://portal.azure.com) を開きます。
2.  画面の左側で、**[参照]**、**[SQL データベース]** の順に選択します。
3.  復元するデータベースに移動して、選択します。
4.  データベースのブレードの上部で **[復元]** を選択します。

    ![Azure SQL データベースの復元](./media/sql-database-point-in-time-restore-portal/restore.png)

5.  データベース名と復元ポイントを指定して [OK] をクリックします。

    ![Azure SQL データベースの復元](./media/sql-database-point-in-time-restore-portal/restore-details.png)


## 次のステップ

- [復旧された Azure SQL データベースの最終処理を行う](sql-database-recovered-finalize.md)
- [SQL Server Management Studio を使用して SQL Database に接続し、T-SQL サンプル クエリを実行する](sql-database-connect-query-ssms.md)



## その他のリソース

- [ビジネス継続性の概要](sql-database-business-continuity.md)
- [SQL Database のドキュメント](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->