---
title: "Azure Portal を使用した Azure SQL Database の管理 | Microsoft Docs"
description: "Azure ポータルを使用して、クラウド内のリレーショナル データベースを管理する方法について説明します。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 3a56e9de-c21a-40ba-9a35-958172cb4e5b
ms.service: sql-database
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 09/19/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: bb40c67d6b355fd2fe0799c781a28657f24f825d


---
# <a name="managing-azure-sql-databases-using-the-azure-portal"></a>Azure ポータルを使用した Azure SQL データベースの管理
> [!div class="op_single_selector"]
> * [Azure ポータル](sql-database-manage-portal.md)
> * [SSMS](sql-database-manage-azure-ssms.md)
> * [PowerShell](sql-database-manage-powershell.md)
> 
> 

[Azure Portal](https://portal.azure.com/) では、Azure SQL データベースと Azure SQL サーバーを作成、監視、管理できます。 この記事では、一般的なタスクを紹介し、より詳しい情報へのリンクを示します。

## <a name="view-your-azure-sql-databases-servers-and-pools"></a>Azure SQL データベース、サーバー、プールの表示
使用可能な SQL Database サービスを表示するには、**[More services (その他のサービス)]** をクリックし、検索ボックスに「**SQL**」と入力します。

![SQL Database](./media/sql-database-manage-portal/sql-services.png)

## <a name="how-do-i-create-or-view-azure-sql-databases"></a>Azure SQL データベースを作成または表示する方法
**[SQL Database]** ブレードを開くには、**[SQL Database]** をクリックし、対象のデータベースをクリックします。または、**[+追加]** をクリックして SQL Database を作成します。 詳細については、「[Azure Portal を使用して数分で SQL Database を作成する](sql-database-get-started.md)」をご覧ください。

![SQL データベース](./media/sql-database-manage-portal/sql-databases.png)

## <a name="how-do-i-create-or-view-azure-sql-servers"></a>Azure SQL サーバーを作成または表示する方法
**[SQL Server]** ブレードを開くには、**[SQL Server]** をクリックし、対象のサーバーをクリックします。または、**[+追加]** をクリックして SQL サーバーを作成します。 詳細については、「[Azure Portal を使用して数分で SQL Database を作成する](sql-database-get-started.md)」をご覧ください。

![SQL Server](./media/sql-database-manage-portal/sql-servers.png)

## <a name="how-do-i-create-or-view-sql-elastic-pools"></a>SQL エラスティック プールを作成または表示する方法
**[SQL 可変プール]** ブレードを開くには、**[SQL 可変プール]** をクリックし、対象のプールをクリックします。または、**[+追加]** をクリックしてプールを作成します。 詳細については、[Azure Portal を使用した Elastic Database プールの作成](sql-database-elastic-pool-create-portal.md)に関するページをご覧ください。

![SQL elastic pools](./media/sql-database-manage-portal/elastic-pools.png)

## <a name="how-do-i-update-or-view-sql-database-settings"></a>SQL データベースの設定を更新または表示する方法
データベースの設定を更新または表示するには、[SQL データベース] ブレードで目的の設定をクリックします。

![SQL データベースの設定](./media/sql-database-manage-portal/settings.png)

## <a name="how-do-i-find-a-sql-databases-fully-qualified-server-name"></a>SQL データベースの完全修飾サーバー名を確認する方法
データベース サーバー名を表示するには、**[SQL Database]** ブレードで **[概要]** をクリックして、サーバー名を書き留めます。

![SQL データベースの設定](./media/sql-database-manage-portal/server-name.png)

## <a name="how-do-i-manage-firewall-rules-to-control-access-to-my-sql-server-and-database"></a>ファイアウォール規則を管理して SQL サーバーと SQL データベースへのアクセスを制御する方法
ファイアウォール規則を表示、作成、または更新するには、**[SQL データベース]** ブレードで **[Set server firewall (サーバー ファイアウォールの設定)]** をクリックします。 詳細については、「 [Azure Portal を使用して Azure SQL Database のサーバー レベルのファイアウォール規則を作成する](sql-database-configure-firewall-settings.md)」を参照してください。

![firewall rules](./media/sql-database-manage-portal/sql-database-firewall.png)

## <a name="how-do-i-change-my-sql-database-service-tier-or-performance-level"></a>SQL データベースのサービス レベルまたはパフォーマンス レベルを変更する方法
SQL データベースのサービス レベルまたはパフォーマンス レベルを更新するには、**[SQL Database]** ブレードで **[価格レベル (DTU のスケール)]** をクリックします。 詳細については、「 [SQL Database のサービス レベルとパフォーマンス レベル (価格レベル) を変更する](sql-database-scale-up.md)」を参照してください。

![pricing tiers](./media/sql-database-manage-portal/pricing-tier.png)

## <a name="how-do-i-configure-auditing-and-threat-detection-for-a-sql-database"></a>SQL データベースの監査と脅威の検出を構成する方法
SQL データベースの監査と脅威の検出を構成するには、**[SQL Database]** ブレードで **[Auditing and Threat detection (監査と脅威の検出)]** をクリックします。 詳細については、「[SQL Database 監査の使用](sql-database-auditing-get-started.md)」と「[SQL Database 脅威の検出の概要](sql-database-threat-detection-get-started.md)」をご覧ください。

## <a name="how-do-i-configure-dynamic-data-masking-for-a-sql-database"></a>SQL データベースの動的データ マスクを構成する方法
SQL データベースの動的データ マスクを構成するには、**[SQL Database]** ブレードで **[動的データ マスク]** をクリックします。 詳細については、 [SQL Database 動的データ マスクの使用](sql-database-dynamic-data-masking-get-started.md)に関するページを参照してください。

## <a name="how-do-i-configure-transparent-data-encryption-tde-for-a-sql-database"></a>SQL データベースの Transparent Data Encryption (TDE) を構成する方法
SQL データベースの Transparent Data Encryption を構成するには、**[SQL Database]** ブレードで **[透過的なデータ暗号化]** をクリックします。 詳細については、「 [ポータルを使用してデータベースで TDE を有効にする](https://msdn.microsoft.com/library/dn948096#Anchor_1)」を参照してください。

## <a name="how-do-i-view-or-change-the-max-size-of-a-sql-database"></a>SQL データベースの最大サイズを表示または変更する方法
SQL データベースのサイズを表示または変更するには、**[SQL Database]** ブレードで **[データベース サイズ]** をクリックします。 サービス レベルまたはパフォーマンス レベルを変更して、データベースの最大サイズを更新します。 詳細については、「 [SQL Database のサービス レベルとパフォーマンス レベル (価格レベル) を変更する](sql-database-scale-up.md)」を参照してください。

## <a name="how-do-i-monitor-and-improve-the-performance-of-a-sql-database"></a>SQL データベースのパフォーマンスを監視および改善する方法
SQL データベースのパフォーマンス特性を監視および改善するには、**[SQL Database]** ブレードの **[Performance overview (パフォーマンス概要)]** をクリックします。 詳細については、「 [SQL Database Performance Insight](sql-database-performance.md)」を参照してください。

## <a name="how-do-i-configure-geo-replication"></a>geo レプリケーションを構成する方法
SQL データベースの geo レプリケーションを設定するには、**[SQL Database]** ブレードで **[geo レプリケーション]** をクリックします。 詳細については、「 [Azure Portal を使用して Azure SQL Database の geo レプリケーションを構成する](sql-database-geo-replication-portal.md)」を参照してください。

## <a name="how-do-i-failover-to-a-geo-replicated-sql-database"></a>geo レプリケートされた SQL データベースにフェールオーバーする方法
geo レプリケートされたセカンダリにフェールオーバーするには、**[SQL Database]** ブレードで **[geo レプリケーション]** をクリックし、**[フェールオーバー]** をクリックします。 詳細については、「 [Azure Portal を使用した Azure SQL Database の計画されたフェールオーバーまたは計画されていないフェールオーバーの開始](sql-database-geo-replication-failover-portal.md)」を参照してください。

## <a name="how-do-i-copy-a-sql-database"></a>SQL データベースをコピーする方法
SQL データベースをコピーするには、**[SQL Database]** ブレードで **[コピー]** をクリックします。 詳細については、「 [Azure Portal を使用した Azure SQL Database のコピー](sql-database-copy-portal.md)」を参照してください。

![SQL データベースの設定](./media/sql-database-manage-portal/sql-database-copy.png)

## <a name="how-do-i-archive-an-azure-sql-database-to-a-bacpac-file"></a>Azure SQL データベースを BACPAC ファイルにアーカイブする方法
SQL データベースの BACPAC を作成するには、**[SQL Database]** ブレードで **[エクスポート]** をクリックします。 詳細については、「 [Azure Portal を使用して BACPAC ファイルに Azure SQL Database をアーカイブする](sql-database-export.md)」を参照してください。

![SQL database export](./media/sql-database-manage-portal/sql-database-export.png)

## <a name="how-do-i-restore-a-sql-database-to-a-previous-point-in-time"></a>SQL データベースを過去のある時点に復元する方法
SQL データベースを復元するには、**[SQL Database]** ブレードで **[復元]** をクリックします。 詳細については、「 [Azure Portal で以前の時点に Azure SQL Database を復元する](sql-database-point-in-time-restore-portal.md)」を参照してください。

![SQL データベースの設定](./media/sql-database-manage-portal/sql-database-restore.png)

## <a name="how-do-i-create-an-azure-sql-database-from-a-bacpac-file"></a>BACPAC ファイルから Azure SQL データベースを作成する方法
BACPAC ファイルから SQL Database を作成するには、**[SQL Server]** ブレードで **[データベースのインポート]** をクリックします。 詳細については、「 [BACPAC ファイルをインポートして新しい Azure SQL Database を作成する](sql-database-import.md)」を参照してください。

![[データベースのインポート]](./media/sql-database-manage-portal/server-commands.png)

## <a name="how-do-i-restore-a-deleted-sql-database"></a>削除した SQL データベースを復元する方法
削除した SQL Database を復元するには、**[SQL Server]** (削除したデータベースが含まれていた SQL Server) ブレードで **[削除済みデータベース]** をクリックします。 詳細については、「 [Azure Portal を使用した、削除済み Azure SQL データベースの復元](sql-database-restore-deleted-database-portal.md)」を参照してください。

## <a name="how-do-i-delete-a-sql-database"></a>SQL データベースを削除する方法
SQL Database を削除するには、**[SQL Database]** ブレードで **[削除]** をクリックします。 

![SQL データベースの設定](./media/sql-database-manage-portal/sql-database-delete.png)

## <a name="additional-resources"></a>その他のリソース
* [SQL Database](sql-database-technical-overview.md)
* [Azure ポータルを使用したエラスティック データベース プールの監視と管理](sql-database-elastic-pool-manage-portal.md)




<!--HONumber=Nov16_HO3-->


