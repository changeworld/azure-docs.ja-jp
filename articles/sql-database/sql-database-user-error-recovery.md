<properties 
   pageTitle="SQL Database のユーザー エラーの復旧 | Microsoft Azure" 
   description="Azure SQL Database の特定時点に復元 (PITR) 機能を使用して、ユーザー エラー、偶発的なデータの破損、または削除済みデータベースを復旧する方法について説明します。" 
   services="sql-database" 
   documentationCenter="" 
   authors="carlrabeler" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="06/16/2016"
   ms.author="carlrab"/>

# Azure SQL Database をエラーから回復する

Azure SQL Database は、ユーザー エラーまたは予期しないデータ変更から復旧するための、2 つの中核的な機能を提供します。

- [ポイントインタイム リストア](sql-database-recovery-using-backups.md#point-in-time-restore)
- [削除済みデータベースの復元](sql-database-recovery-using-backups.md#deleted-database-restore)

Azure SQL Database は、ポイントインタイム リストアの実行時に、常に新しいデータベースに復元しますが、削除済みデータベースから復元する場合は、同じデータベース名に復元することができます。これらの復元機能は、Basic、Standard、Premium のすべてのデータベースに提供されます。

##ポイントインタイム リストア

ユーザー エラーや意図しないデータ変更が発生した場合、ポイントインタイム リストア (PITR) 機能を使用して、データベースの保持期間内の任意の時点にデータベースを復元できます。

Basic データベースの保持期間は 7 日間、Standard データベースの保持期間は 35 日間、Premium データベースの保持期間は 35 日です。データベース バックアップのリテンション期間の詳細については、[自動バックアップ](sql-database-automated-backups.md)に関するページをご覧ください。

ポイントインタイム リストアを実行する場合は、次をご覧ください。

- [Azure ポータルでのポイントインタイム リストア](sql-database-point-in-time-restore-portal.md)
- [PowerShell でのポイントインタイム リストア](sql-database-point-in-time-restore-powershell.md)
- [REST API でのポイントインタイム リストア (createmode=PointInTimeRestore)](https://msdn.microsoft.com/library/azure/mt163685.aspx)


## 削除されたデータベースの復元

データベースが削除された場合、Azure SQL Database では、削除されたデータベースを削除された時点の状態に復元できます。Azure SQL Database では、データベースの保持期間にわたり、削除されたデータベースのバックアップを格納します。

削除されたデータベースの保持期間は、データベースのサービス階層に基づく日数とデータベースが存在していた日数のどちらか短い方になります。データベースのリテンション期間の詳細については、[自動バックアップ](sql-database-automated-backups.md)に関するページをご覧ください。

削除されたデータベースを復元するには:

- [Azure ポータルで削除済みデータベースを復元する](sql-database-restore-deleted-database-portal.md)
- [PowerShell で削除済みデータベースを復元する](sql-database-restore-deleted-database-powershell.md)
- [REST API で削除済みデータベースを復元する (createmode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx)


## 次のステップ

- ビジネス継続性の概要については、[ビジネス継続性の概要](sql-database-business-continuity.md)に関する記事を参照してください。
- Azure SQL Database 自動バックアップの詳細については、「[SQL Database automated backups (SQL Database 自動バックアップ)](sql-database-automated-backups.md)」を参照してください。
- ビジネス継続性の設計および復旧シナリオについては、[継続性のシナリオ](sql-database-business-continuity-scenarios.md)に関する記事を参照してください。
- 自動バックアップを使用して復旧する方法については、[サービス主導のバックアップからのデータベース復元](sql-database-recovery-using-backups.md)に関する記事を参照してください。
- アクティブ geo レプリケーションの使用方法については、[アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)に関する記事を参照してください。

<!---HONumber=AcomDC_0706_2016-->