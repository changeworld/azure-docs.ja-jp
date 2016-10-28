<properties
	pageTitle="geo 冗長バックアップからの Azure SQL Database の復元 (Azure ポータル) | Microsoft Azure"
	description="geo 冗長バックアップから Azure SQL Database を geo リストアします (Azure ポータル)。"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="07/17/2016"
	ms.author="sstein"
	ms.workload="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Azure ポータルを使用した geo 冗長バックアップからの Azure SQL Database の geo リストア


> [AZURE.SELECTOR]
- [概要](sql-database-recovery-using-backups.md)
- [geo リストア: PowerShell](sql-database-geo-restore-powershell.md)

この記事では、Azure ポータルで geo リストアを使用して新しいサーバーにデータベースを復元する方法について説明します。

## 復元するデータベースの選択

Azure ポータルでデータベースを復元するには、次の手順を実行します。

1.	[Azure ポータル](https://portal.azure.com) を開きます。
2.  画面の左側で、**[新規]**、**[データ + ストレージ]**、**[SQL Database]** の順に選択します。
3.  ソースとして **[バックアップ]** を選択し、復元する geo 冗長バックアップを選択します。

    ![Azure SQL データベースの復元](./media/sql-database-geo-restore-portal/geo-restore.png)

4.  データベース名と、データベースの復元先サーバーを指定し、[作成] をクリックします。


## 次のステップ

- ビジネス継続性の概要およびシナリオについては、[ビジネス継続性の概要](sql-database-business-continuity.md)に関する記事を参照してください。
- Azure SQL Database 自動バックアップの詳細については、「[SQL Database automated backups (SQL Database 自動バックアップ)](sql-database-automated-backups.md)」を参照してください。
- 自動バックアップを使用して復旧する方法については、[サービス主導のバックアップからのデータベース復元](sql-database-recovery-using-backups.md)に関する記事を参照してください。
- より迅速な復旧オプションについては、[アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)に関する記事を参照してください。
- 自動バックアップを使用したアーカイブについては、[データベースのコピー](sql-database-copy.md)に関する記事を参照してください。

<!---HONumber=AcomDC_0727_2016-->