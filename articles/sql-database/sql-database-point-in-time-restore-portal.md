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
	ms.date="06/17/2016"
	ms.author="sstein"
	ms.workload="sqldb-bcdr"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Azure ポータルで以前の時点に Azure SQL Database を復元する


> [AZURE.SELECTOR]
- [概要](sql-database-point-in-time-restore.md)
- [Azure ポータル](sql-database-point-in-time-restore-portal.md)
- [PowerShell](sql-database-point-in-time-restore-powershell.md)

この記事では、Azure ポータルを使用して、[SQL Database 自動バックアップ](sql-database-automated-backups.md)から以前の時点にデータベースを復元する方法について説明します。

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

- PowerShell を使用して特定の時点に復旧する詳細な手順については、[PowerShell を使用したポイントインタイム リストア](sql-database-point-in-time-restore-powershell.md)に関するページをご覧ください。
- REST API を使用して特定の時点に復旧する詳細な手順については、[REST API を使用したポイントインタイム リストア](https://msdn.microsoft.com/library/azure/mt163685.aspx)に関するページをご覧ください。
- ポイントイン タイム リストアの概要については、[ポイントインタイム リストア](sql-database-point-in-time-restore.md)に関するページをご覧ください。
- ユーザー エラーまたはアプリケーション エラーから復旧する方法の詳細については、[ユーザー エラーの復旧](sql-database-user-error-recovery.md)に関するページをご覧ください。

## その他のリソース

- [ビジネス継続性のシナリオ](sql-database-business-continuity-scenarios.md)

<!---HONumber=AcomDC_0622_2016-->