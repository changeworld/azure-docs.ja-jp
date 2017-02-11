---
title: "クラウドのビジネス継続性 - 削除済みデータベースの復元 - SQL Database | Microsoft Docs"
description: "Azure SQL Database を特定の時点 (最長 35 日間) にロール バックすることができる、ポイントインタイム リストアについて説明します。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: fd1d334d-a035-4a55-9446-d1cf750d9cf7
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/11/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 239d009a1fc7273a50d335a0d55d61f414d99b11
ms.openlocfilehash: e333c306d85d6eb571c3c1990188e67b18c8a6b1


---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>データベースの自動バックアップを使用した Azure SQL Database の復旧
SQL Database は、 [SQL Database 自動バックアップ](sql-database-automated-backups.md)を使用したデータベース復旧の&3; つのオプションを提供します。 サービス主導のバックアップから、 [保有期間](sql-database-service-tiers.md) 内に次の場所にデータベースを復元できます。

* 保有期間内の特定の時点に復旧された、同じ論理サーバー上の新しいデータベース。 
* 削除済みデータベースの削除時に復旧された、同じ論理サーバー上のデータベース。
* geo レプリケートされている BLOB ストレージ (RA-GRS) の最新の日次バックアップに復旧された、任意のリージョンの論理サーバー上の新しいデータベース。

> [!TIP]
> チュートリアルについては、「[データの保護と回復のためのバックアップと復元の概要](sql-database-get-started-backup-recovery.md)」をご覧ください。
>

また、[SQL Database 自動バックアップ](sql-database-automated-backups.md)を使用して、現在の SQL Database とトランザクション上の一貫性がある、任意のリージョンの論理サーバー上に[データベースのコピー](sql-database-copy.md)を作成することもできます。 データベースのコピー、および [BACPAC ファイルへのエクスポート](sql-database-export.md) を使用して、トランザクション上の一貫性があるデータベースのコピーを保有期間を超えた長期間のストレージ用にアーカイブしたり、データベースのコピーをオンプレミスまたは SQL Server の Azure VM インスタンスに転送できます。

> [!IMPORTANT]
> [長期のバックアップ リテンション期間](sql-database-long-term-retention.md)で毎週のバックアップを格納するように倫理サーバーを構成できます。

## <a name="recovery-time"></a>復旧時間
データベースの自動バックアップを使用してデータベースを復元する復旧時間は、さまざまな要因によって影響を受けます。 

* データベースのサイズ
* データベースのパフォーマンス レベル
* 関連するトランザクション ログ数
* 復元時点に復旧するために再生の必要があるアクティビティ量
* 別のリージョンへの復元の場合は、ネットワーク帯域幅 
* ターゲット リージョンで処理される、同時実行される復元要求の数 
  
  大規模なデータベースや、アクティビティ数が多いデータベースの場合、復元に数時間かかることがあります。 あるリージョンでの停止が長引いた場合、多数の geo リストア要求が他のリージョンによって処理されることがあります。 多数の要求がある場合、そのリージョンでのデータベースの復旧時間が長くなる可能性があります。 データベースの復元のほとんどは、12 時間以内に完了します。
  
  一括復元を実行する機能は組み込まれていません。 このタスクを達成する&1; つの方法として、たとえば、 [Azure SQL Database: Full Server Recovery](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) スクリプトがあります。

> [!IMPORTANT]
> 自動バックアップを使って復旧するには、サブスクリプションにおける SQL Server の共同作成者ロールのメンバーまたはサブスクリプション所有者である必要があります。 復旧には、Azure ポータル、PowerShell、または REST API を使用できます。 Transact-SQL は使用できません。 
> 

## <a name="point-in-time-restore"></a>ポイントインタイム リストア
ポイントインタイム リストアでは、 [SQL Database 自動バックアップ](sql-database-automated-backups.md)を使用し、既存のデータベースを、以前の時点の内容で同じ論理サーバー上に新しいデータベースとして復元できます。 既存のデータベースを上書きすることはできません。 以前の時点への復元には、[Azure Portal](sql-database-point-in-time-restore.md)、[PowerShell](sql-database-point-in-time-restore.md)、または [REST API](https://msdn.microsoft.com/library/azure/mt163685.aspx) を使用できます。


データベースは、任意のパフォーマンス レベルまたはエラスティック プールに復元できます。 論理サーバーまたはエラスティック プールに、十分な DTU クォータがあることをご確認ください。 復元によって新しいデータベースが作成される点や、復元されるデータベースのサービス レベルとパフォーマンス レベルが、現在実行中のデータベースの状態とは異なる点を考慮してください。 復元が完了すると、復元されたデータベースは、通常の完全にアクセス可能なオンライン データベースになり、サービス レベルとパフォーマンス レベルに基づいて通常料金が発生します。 データベースの復元が完了するまで、料金は発生しません。

通常、以前の時点へのデータベースの復元は、復旧の目的で行います。 その場合、復元されたデータベースを元のデータベースの代わりとして扱うか、データを取得して元のデータベースの更新に使用できます。 

* ***データベースの置換***: 復元されたデータベースを元のデータベースの代わりに使用する場合は、パフォーマンス レベルやサービス レベルが適切であることを確認し、必要に応じてデータベースをスケールしてください。 T-SQL の ALTER DATABASE コマンドを使用して、元のデータベース名を変更し、復元したデータベースに元の名前を付けます。 
* ***データの復旧***: ユーザー エラーまたはアプリケーション エラーからの復旧のために、復元されたデータベースからデータを取得する場合は、復元されたデータベースから元のデータベースにデータを抽出するのに必要なデータ復旧のスクリプトを書き込み、実行する必要があります。 復元操作が完了するまでに時間がかかる可能性がありますが、データベース一覧全体で復元しているデータベースが表示されるようになります。 復元中にデータベースを削除すると、操作は取り消され、復元が完了していないデータベースの料金はかかりません。 

ユーザー エラーおよびアプリケーション エラーからの復旧にポイントインタイム リストアを使用する詳細については、「 [Point-in-Time Restore](sql-database-recovery-using-backups.md#point-in-time-restore)

## <a name="deleted-database-restore"></a>削除されたデータベースの復元
削除されたデータベースの復元では、 [SQL Database 自動バックアップ](sql-database-automated-backups.md)を使用して、削除済みデータベースを、削除された時点の内容で同じ論理サーバー上に復元できます。 

> [!IMPORTANT]
> Azure SQL Database サーバー インスタンスを削除すると、そのデータベースもすべて削除されます。これを回復することはできません。 現時点では、削除されたサーバーを復元するためのサポートはありません。
> 
> 

復元されたデータベースには、同じ名前または新しいデータベース名を使用できます。 [Azure Portal](sql-database-restore-deleted-database-portal.md)、[PowerShell](sql-database-restore-deleted-database-powershell.md)、または [REST (createMode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx) を使用することができます。 


## <a name="geo-restore"></a>地理リストア
geo リストアでは、geo レプリケートされた最新の [毎日の自動バックアップ](sql-database-automated-backups.md)から任意の Azure リージョン内の任意のサーバーで SQL データベースを復元することができます。 geo リストアではソースとして geo 冗長バックアップが使用され、障害によってデータベースまたはデータセンターにアクセスできない場合でも、geo リストアを使用してデータベースを復旧できます。 [Azure Portal](sql-database-geo-restore-portal.md)、[PowerShell](sql-database-geo-restore-powershell.md)、または [REST (createMode=Recovery)](https://msdn.microsoft.com/library/azure/mt163685.aspx) を使用することができます。 


geo リストアは、データベースがホストされているリージョンでのインシデントが原因でデータベースが利用できない場合の既定の復旧オプションです。 リージョン内の大規模なインシデントにより、データベース アプリケーションが使用できなくなった場合、geo リストアを使用して、最新のバックアップから他の任意のリージョン内のサーバーにデータベースを復元できます。 すべてのバックアップは geo レプリケートされ、バックアップが作成されてから異なるリージョンの Azure BLOB に geo レプリケートされるまでの間には、遅延がある場合があります。 この遅延は最大&1; 時間なので、障害発生時には、最大&1; 時間分のデータが損失する可能性があります。つまり、RPO は最大&1; 時間です。 次に示すのは、最後の日次バックアップからのデータベースの復元です。

![地理リストア](./media/sql-database-geo-restore/geo-restore-2.png)

geo リストアを使用して障害から復旧する方法の詳細については、 [障害からの復旧](sql-database-disaster-recovery.md)

> [!IMPORTANT]
> geo リストアはすべてのサービス階層で使用できますが、RPO と推定復旧時間 (ERT) が最も長い SQL Database で使用できる障害復旧ソリューションが最も基本です。 最大サイズが 2 GB の Basic データベースでは、geo リストアは ERT が 12 時間である妥当な障害復旧ソリューションを提供します。 さらに大きい Standard または Premium データベースでは、短い復旧間隔が必要な場合、またはデータ損失の可能性を下げる場合は、アクティブ geo レプリケーションの使用を検討する必要があります。 アクティブ geo レプリケーションでは、継続的にレプリケートされるセカンダリへのフェールオーバーを開始することだけが必要なので、RPO と ERT が大きく短縮されます。 詳細については、 [アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)に関する記事を参照してください。
> 
> 

## <a name="programmatically-performing-recovery-using-automated-backups"></a>自動バックアップを使用したプログラム実行の復旧
前に説明したように、データベースの復旧は、Azure Portal のほかに、Azure PowerShell と REST API を使用してプログラムで実行できます。 次の表では、使用できるコマンド セットについて説明します。

### <a name="powershell"></a>PowerShell
| コマンドレット | 説明 |
| --- | --- |
| [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/en-us/library/azure/mt603648.aspx) |1 つまたは複数のデータベースを取得します。 |
| [Get-AzureRMSqlDeletedDatabaseBackup](https://msdn.microsoft.com/en-us/library/azure/mt693387.aspx) |復元する削除済みデータベースを取得します。 |
| [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx) |データベースの geo 冗長バックアップを取得します。 |
| [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx) |SQL Database を復元します。 |
|  | |

### <a name="rest-api"></a>REST API
| API | 説明 |
| --- | --- |
| [REST (createMode=Recovery)](https://msdn.microsoft.com/library/azure/mt163685.aspx) |データベースを復元します |
| [Get Create or Update Database Status](https://msdn.microsoft.com/library/azure/mt643934.aspx) |復元操作中にステータスを返します |
|  | |

## <a name="summary"></a>概要
自動バックアップは、ユーザー エラーやアプリケーション エラー、偶発的なデータベースの削除、および長期間にわたる障害からデータベースを保護します。 このゼロ コスト、管理作業ゼロのソリューションは、すべての SQL Database で使用できます。 

## <a name="next-steps"></a>次のステップ
* ビジネス継続性の概要およびシナリオについては、 [ビジネス継続性の概要](sql-database-business-continuity.md)
* Azure SQL Database 自動バックアップの詳細については、「 [SQL Database 自動バックアップ](sql-database-automated-backups.md)
* Azure Recovery Services コンテナーの自動バックアップの長期のリテンション期間を構成するには、「[長期のバックアップ リテンション期間の構成](sql-database-configure-long-term-retention.md)」をご覧ください。
* より迅速な復旧オプションについては、 [アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)  
* 自動バックアップを使用したアーカイブについては、 [データベースのコピー](sql-database-copy.md)



<!--HONumber=Dec16_HO2-->


