---
title: バックアップから Azure SQL Database を復元する | Microsoft Docs
description: Azure SQL Database を特定の時点 (最長 35 日間) にロール バックすることができる、ポイントインタイム リストアについて説明します。
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 10/23/2018
ms.openlocfilehash: 8785ef50cd7bd27e866b1fa546317021eed5da01
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53599112"
---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>データベースの自動バックアップを使用した Azure SQL Database の復旧

既定で、SQL Database のバックアップは Geo レプリケートされている Blob ストレージ (RA-GRS) に格納されます。 [自動データベース バックアップ](sql-database-automated-backups.md)を使用したデータベースの復旧には、次のオプションを使用できます。

- 保有期間内の特定の時点に復旧された、同じ論理サーバー上に新しいデータベースを作成する。
- 削除済みデータベースの削除時に復旧された、同じ論理サーバー上にデータベースを作成する。
- 最新のバックアップの時点に復旧された、同じリージョンの論理サーバー上に新しいデータベースを作成する。
- 最後にレプリケートされたバックアップの時点に復旧された、任意の他のリージョンの論理サーバー上に新しいデータベースを作成する。

[長期保有期間のバックアップ](sql-database-long-term-retention.md)を構成している場合、任意のリージョンの任意の論理サーバー上に任意の LTR バックアップから新しいデータベースを作成することもできます。

> [!IMPORTANT]
> 復元中に既存のデータベースを上書きすることはできません。

Standard または Premium サービス レベルを使用している場合、復元されたデータベースでは、次の条件下で、追加のストレージ コストが生じます。

- データベースの最大サイズが 500 GB より大きい場合に、P11–P15 を S4-S12 または P1–P6 に復元する。
- データベースの最大サイズが 250 GB より大きい場合に、P1–P6 を S4-S12 に復元する。

余分なコストがかかるのは復元されるデータベースの最大サイズがコンピューティング サイズに含まれるストレージの量を超えるためであり、含まれる量を超えてプロビジョニングされる余分なストレージに追加料金がかかります。 追加ストレージの価格について詳しくは、「[SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)」をご覧ください。 実際に使われる領域の量が含まれるストレージの量より少ない場合、データベースの最大サイズを含まれる量に減らすことで、この追加コストを回避できます。

> [!NOTE]
> [データベースのコピー](sql-database-copy.md)を作成するときは、[自動データベース バックアップ](sql-database-automated-backups.md)が使われます。

## <a name="recovery-time"></a>復旧時間

データベースの自動バックアップを使用してデータベースを復元する復旧時間は、さまざまな要因によって影響を受けます。

- データベースのサイズ
- データベースのコンピューティング サイズ
- 関連するトランザクション ログ数
- 復元時点に復旧するために再生の必要があるアクティビティ量
- 別のリージョンへの復元の場合は、ネットワーク帯域幅
- ターゲット リージョンで処理される、同時実行される復元要求の数

大規模なデータベースや、アクティビティ数が多いデータベースの場合、復元に数時間かかることがあります。 あるリージョンでの停止が長引いた場合、多数の geo リストア要求が他のリージョンによって処理されることがあります。 多数の要求がある場合、そのリージョンでのデータベースの復旧時間が長くなる可能性があります。 ほとんどのデータベースの復元は、12 時間未満で完了します。

単一サブスクリプションの場合、送信および処理される同時実行の復元要求の数に制限があります (ポイントインタイム リストア、geo リストア、および長期間保有のバックアップからの復元ポイントも含まれます)。

| | **処理される同時要求の最大数** | **送信される同時要求の最大数** |
| :--- | --: | --: |
|単一のデータベース (サブスクリプションごと)|10|60|
|エラスティック プール (プールごと)|4|200|
||||

一括復元を実行する機能は組み込まれていません。 このタスクを達成する 1 つの方法として、たとえば、[Azure SQL Database:Full Server Recovery](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) スクリプトがあります。

> [!IMPORTANT]
> 自動バックアップを使って復旧するには、サブスクリプションにおける SQL Server の共同作成者ロールのメンバーまたはサブスクリプション所有者である必要があります。[RBAC の組み込みのロール](../role-based-access-control/built-in-roles.md)に関するページをご覧ください。 復旧には、Azure ポータル、PowerShell、または REST API を使用できます。 Transact-SQL は使用できません。

## <a name="point-in-time-restore"></a>ポイントインタイム リストア

Azure portal、[PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase)、または [REST API](https://docs.microsoft.com/rest/api/sql/databases) を使用し、同じサーバー上の新しいデータベースとして、単一のデータベース、プールされたデータベース、または Managed Instance データベースを以前の時点に復元できます。 データベースは、任意のサービス層または計算サイズに復元できます。 データベースを復元するサーバーに十分なリソースがあることを確認します。 完了すると、復元されたデータベースは完全にアクセス可能な通常のオンライン データベースになります。 復元されたデータベースは、サービス レベルとコンピューティング サイズに基づいて通常料金が発生します。 データベースの復元が完了するまで、料金は発生しません。

通常、以前の時点へのデータベースの復元は、復旧の目的で行います。 その場合、復元されたデータベースを元のデータベースの代わりとして扱うか、データを取得して元のデータベースの更新に使用できます。

- **データベースの置換**

  復元されたデータベースを元のデータベースの代わりに使用する場合は、コンピューティング サイズやサービス レベルが適切であることを確認し、必要に応じてデータベースをスケーリングしてください。 T-SQL の [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) コマンドを使用して、元のデータベース名を変更し、復元したデータベースに元の名前を付けます。

- **データの復旧**

  ユーザー エラーまたはアプリケーション エラーからの復旧のために、復元されたデータベースからデータを取得する場合は、復元されたデータベースから元のデータベースにデータを抽出するのに必要なデータ復旧のスクリプトを書き込み、実行する必要があります。 復元操作が完了するまでに時間がかかる可能性がありますが、復元プロセスを通して、復元しているデータベースがデータベース一覧に表示されます。 復元中にデータベースを削除すると、復元操作は取り消され、復元が完了していないデータベースの料金はかかりません。

Azure portal を使用して単一のデータベース、プールされたデータベース、または Managed Instance データベースを特定の時点に復旧するには、データベースのページを開き、ツールバーの **[復元]** をクリックします。

![ポイントインタイム リストア](./media/sql-database-recovery-using-backups/point-in-time-recovery.png)

> [!IMPORTANT]
> データベースをプログラムでバックアップから復元するには、「[自動バックアップを使用したプログラム実行の復旧](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)」を参照してください。

## <a name="deleted-database-restore"></a>削除されたデータベースの復元

Azure Portal、[PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase)、または [REST (createMode=Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) を使用して、削除されたデータベースを同じ論理サーバー上の削除されたデータベースの削除時刻に復元できます。 [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase) を使った復元の間に、削除されたデータベースを以前の時点に復元することができます。

> [!Note]
> 削除されたデータベースの復元は、Managed Instance で利用できません。
> [!TIP]
> 削除されたデータベースの復元方法を示すサンプル PowerShell スクリプトについては、[PowerShell を使用した SQL データベースの復元](scripts/sql-database-restore-database-powershell.md)に関するページを参照してください。
> [!IMPORTANT]
> Azure SQL Database サーバー インスタンスを削除すると、そのデータベースもすべて削除されます。これを回復することはできません。 現時点では、削除されたサーバーを復元するためのサポートはありません。

### <a name="deleted-database-restore-using-the-azure-portal"></a>Azure portal を使用した削除済みデータベースの復元

Azure portal を使用して、削除されたみデータベースを、その[DTU ベースのモデル リテンション期間](sql-database-service-tiers-dtu.md)中または[仮想コア ベースのモデル リテンション期間](sql-database-service-tiers-vcore.md)中に復旧するには、サーバーのページを開き、[操作] 領域で **[削除済みデータベース]** をクリックします。

![削除されたデータベースの復元 1](./media/sql-database-recovery-using-backups/deleted-database-restore-1.png)

![削除されたデータベースの復元 2](./media/sql-database-recovery-using-backups/deleted-database-restore-2.png)

> [!IMPORTANT]
> 削除されたデータベースをプログラムで復元するには、「[自動バックアップを使用したプログラム実行の復旧](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)」を参照してください。

## <a name="geo-restore"></a>geo リストア

geo レプリケートされた最新のバックアップから任意の Azure リージョン内の任意のサーバーで SQL データベースを復元することができます。 geo リストアではソースとして geo 冗長バックアップが使用され、障害によってデータベースまたはデータセンターにアクセスできない場合でも、geo リストアを使用してデータベースを復旧できます。

> [!Note]
> geo 復元は、Managed Instance で利用できません。

geo リストアは、データベースがホストされているリージョンでのインシデントが原因でデータベースが利用できない場合の既定の復旧オプションです。 リージョン内の大規模なインシデントにより、データベース アプリケーションが使用できなくなった場合、geo レプリケートされたバックアップから他の任意のリージョン内のサーバーにデータベースを復元できます。 バックアップが取得される時刻と、別のリージョンの Azure BLOB にその差分バックアップが geo レプリケートされる時刻には時間差があります。 この時間差は最大 1 時間なので、障害が発生した場合、最大 1 時間分のデータが損失する可能性があります。 次の図は、別のリージョン内の利用可能な最新のバックアップからのデータベースの復元を示しています。

![地理リストア](./media/sql-database-geo-restore/geo-restore-2.png)

> [!TIP]
> geo リストアの実行方法を示すサンプル PowerShell スクリプトについては、[PowerShell を使用した SQL データベースの復元](scripts/sql-database-restore-database-powershell.md)に関するページを参照してください。

geo セカンダリでのポイントインタイム リストアは、現在はサポートされていません。 ポイントインタイム リストアは、プライマリ データベースでのみ実行できます。 geo リストアを使用して障害から復旧する方法の詳細については、[障害からの復旧](sql-database-disaster-recovery.md)に関するページを参照してください。

> [!IMPORTANT]
> バックアップからの復旧は、目標復旧時点 (RPO) と推定復旧時間 (ERT) が最も長い、SQL Database で使用できるディザスター リカバリー ソリューションで最も基本的な方法です。 小さなサイズのデータベース (例: Basic サービス レベルのデータベース、またはエラスティック プールの小さなサイズのテナント データベース) を使用しているソリューションでは、多くの場合、ERT が最長 12 時間 (通常ははるかに短い時間で完了) である geo リストアが妥当な DR ソリューションです。 大規模なデータベースを使用しているソリューションを短時間で復旧する必要がある場合は、[アクティブ geo レプリケーション](sql-database-active-geo-replication.md)または[自動フェールオーバー グループ](sql-database-auto-failover-group.md)の使用を検討する必要があります。 アクティブ geo レプリケーションでは、継続的にレプリケートされるセカンダリへのフェールオーバーを開始することだけが必要なので、RPO と ERT が大きく短縮されます。 自動フェールオーバー グループでは、データベースのグループを自動的にフェールオーバーできます。 ビジネスを継続するための選択の詳細については、[ビジネス継続性の概要](sql-database-business-continuity.md)に関するページを参照してください。

### <a name="geo-restore-using-the-azure-portal"></a>Azure portal を使用した geo リストア

Azure Portal を使用して、データベースをその [DTU ベースのモデル リテンション期間](sql-database-service-tiers-dtu.md)中または[仮想コアベースのモデル リテンション期間](sql-database-service-tiers-vcore.md)中に geo リストアするには、[SQL データベース] ページを開き、**[追加]** をクリックします。 **[ソースの選択]** テキスト ボックスで、**[バックアップ]** を選択します。 任意のリージョン内のサーバー上で復旧を実行するバックアップを指定します。

## <a name="programmatically-performing-recovery-using-automated-backups"></a>自動バックアップを使用したプログラム実行の復旧

前に説明したように、データベースの復旧は、Azure Portal のほかに、Azure PowerShell または REST API を使用してプログラムで実行できます。 次の表では、使用できるコマンド セットについて説明します。

### <a name="powershell"></a>PowerShell

- 単一のデータベースまたはプールされたデータベースを復元するには、「[Restore-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase)」を参照してください

  | コマンドレット | 説明 |
  | --- | --- |
  | [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase) |1 つまたは複数のデータベースを取得します。 |
  | [Get-AzureRMSqlDeletedDatabaseBackup](/powershell/module/azurerm.sql/get-azurermsqldeleteddatabasebackup) | 復元する削除済みデータベースを取得します。 |
  | [Get-AzureRmSqlDatabaseGeoBackup](/powershell/module/azurerm.sql/get-azurermsqldatabasegeobackup) |データベースの geo 冗長バックアップを取得します。 |
  | [Restore-AzureRmSqlDatabase](/powershell/module/azurerm.sql/restore-azurermsqldatabase) |SQL Database を復元します。 |

  > [!TIP]
  > データベースのポイントインタイム リストアの実行方法を示すサンプル PowerShell スクリプトについては、[PowerShell を使用した SQL データベースの復元](scripts/sql-database-restore-database-powershell.md)に関するページを参照してください。

- Managed Instance データベースを復元するには、「[Point-in-time restore of a database on Azure SQL Managed Instance using AzureRm.Sql PowerShell library](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/28/point-in-time-restore-of-a-database-on-azure-sql-managed-instance-using-azurerm-sql-powershell-library/)」(AzureRm.Sql PowerShell ライブラリを使用して Azure SQL Managed Instance 上の特定の時点のデータベースを復元する) を参照してください

### <a name="rest-api"></a>REST API

REST API を使用して単一のデータベースまたはプールされたデータベースを復元するには:

| API | 説明 |
| --- | --- |
| [REST (createMode=Recovery)](https://docs.microsoft.com/rest/api/sql/databases) |データベースを復元します |
| [Get Create or Update Database Status](https://docs.microsoft.com/rest/api/sql/operations) |復元操作中にステータスを返します |

### <a name="azure-cli"></a>Azure CLI

Azure CLI を使用して単一のデータベースまたはプールされたデータベースを復元するには、「[az sql db restore](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-restore)」を参照してください。

## <a name="summary"></a>まとめ

自動バックアップは、ユーザー エラーやアプリケーション エラー、偶発的なデータベースの削除、および長期間にわたる障害からデータベースを保護します。 この組み込みの機能は、すべてのサービス レベルとコンピューティング サイズで使用できます。

## <a name="next-steps"></a>次の手順

- ビジネス継続性の概要およびシナリオについては、 [ビジネス継続性の概要](sql-database-business-continuity.md)に関する記事を参照してください。
- Azure SQL Database 自動バックアップの詳細については、「 [SQL Database 自動バックアップ](sql-database-automated-backups.md)」を参照してください。
- 長期のリテンション期間については、[長期のリテンション期間](sql-database-long-term-retention.md)に関する記事をご覧ください。
- より迅速な復旧オプションについては、[アクティブ geo レプリケーション](sql-database-active-geo-replication.md)または[自動フェールオーバー グループ](sql-database-auto-failover-group.md)に関する記事を参照してください。
