---
title: Azure Backup によってバックアップされる Azure VM 上の SQL Server データベースを管理および監視する | Microsoft Docs
description: この記事では、Azure Backup を使用してバックアップされた Azure VM 上で実行されている SQL Server データベースを復元する方法について説明します
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2018
ms.author: raynew
ms.openlocfilehash: 1c2ce0ba42f0bc3efd1dcc951113b05ab6941b98
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430956"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>バックアップされる SQL Server データベースを管理および監視する 


この記事では、[Azure Backup](backup-overview.md) サービスによって Azure Backup Recovery Services コンテナーにバックアップされる Azure VM 上で実行されている SQL Server データベースを管理および監視するための一般的なタスクについて説明します。 タスクには、ジョブとアラートの監視、データベースの保護の停止と再開、バックアップ ジョブの実行、バックアップからの VM の登録解除などがあります。


> [!NOTE]
> Azure Backup を使用した Azure VM 上で実行されている SQL Server データベースのバックアップは現在、パブリック プレビュー段階にあります。


SQL Server データベースのバックアップをまだ構成していない場合は、[この記事](backup-azure-sql-database.md)の手順に従ってください

## <a name="monitor-backup-jobs"></a>バックアップ ジョブを監視する

###  <a name="monitor-ad-hoc-jobs-in-the-portal"></a>ポータルでのアドホック ジョブを監視する

Azure Backup では、データベースの検出と登録や、バックアップと復元の操作など、手動でトリガーされるすべてのジョブが **[バックアップ ジョブ]** ポータルに表示されます。

![[バックアップ ジョブ] ポータル](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> スケジュールされたバックアップ ジョブは、**[バックアップ ジョブ]** ポータルには表示されません。 次のセクションで説明されているように、スケジュールされたバックアップ ジョブを監視するには、SQL Server Management Studio を使用します。
>

### <a name="monitor-backup-jobs-with-sql-server-management-studio"></a>SQL Server Management Studio でバックアップ ジョブを監視する 

Azure Backup では、すべてのバックアップ操作に SQL ネイティブ API を使用します。

ネイティブ API を使用して、msdb データベース内の [SQL バックアップセット テーブル](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017)からすべてのジョブ情報をフェッチします。

次の例は、**DB1** という名前のデータベースのすべてのバックアップ ジョブをフェッチするためのクエリです。 高度な監視を行うには、このクエリをカスタマイズします。

```
select CAST (
Case type
                when 'D' 
                                 then 'Full'
                when  'I'
                               then 'Differential' 
                ELSE 'Log'
                END         
                AS varchar ) AS 'BackupType',
database_name, 
server_name,
machine_name,
backup_start_date,
backup_finish_date,
DATEDIFF(SECOND, backup_start_date, backup_finish_date) AS TimeTakenByBackupInSeconds,
backup_size AS BackupSizeInBytes
  from msdb.dbo.backupset where user_name = 'NT SERVICE\AzureWLBackupPluginSvc' AND database_name =  <DB1>  

```

## <a name="view-backup-alerts"></a>バックアップ アラートを表示する

ログ バックアップは 15 分ごとに行われるため、バックアップ ジョブを適宜監視することは面倒に感じられる場合があります。 Azure Backup では、メール アラートで監視が容易になります。

- すべてのバックアップ エラーに対してアラートがトリガーされます。
- アラートは、エラー コードごとにデータベース レベルで統合されます。
- 電子メール アラートは、データベースの最初のバックアップ エラーに対してのみ送信されます。 

バックアップ アラートを監視するには、次の手順を実行します。

1. データベース アラートを監視するには、[Azure portal](https://portal.azure.com) で Azure サブスクリプションにサインインします。

2. コンテナー ダッシュボードで、**[アラートとイベント]** を選択します。

   ![[アラートとイベント] を選択する](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

4. **[アラートとイベント]** で、**[バックアップ アラート]** を選択します。

   ![[バックアップ アラート] を選択する](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>SQL Server データベースの保護を停止する

複数の方法で SQL Server データベースのバックアップを停止できます。

* 今後のバックアップ ジョブすべてを停止し、すべての復旧ポイントを削除する。
* 今後のバックアップ ジョブすべてを停止するが、復旧ポイントはそのままにする。

以下の点に注意してください。

回復ポイントをそのままにすると、そのポイントはバックアップ ポリシーに従ってクリーンアップされます。 すべての復旧ポイントがクリーンアップされるまで、保護されたインスタンスと消費されているストレージの料金が発生します。 価格に関して詳しくは、[こちら](https://azure.microsoft.com/pricing/details/backup/)をご覧ください。
- 復旧ポイントをそのままにすると、それらはアイテム保持ポリシーに従って有効期限が切れますが、ユーザーがバックアップ データを明示的に削除するまで、Azure Backup では常に最後の 1 つの復旧ポイントが保持されます。
- バックアップを停止しないでデータ ソースを削除すると、新しいバックアップは失敗するようになります。 繰り返しますが、古い復旧ポイントはポリシーに従って期限切れになりますが、最後の 1 つの復旧ポイントを、ユーザーがバックアップを停止してデータを削除するまで、常に保持されます。
- 自動保護が有効になっているデータベースのバックアップは、自動保護を無効にするまで、停止することはできません。

データベースの保護を停止するには、次の手順を実行します。

1. コンテナー ダッシュボードで、**[使用状況]** の **[バックアップ アイテム]** を選択します。

    ![[バックアップ項目] メニューを開く](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png)。

2. **[Backup Management Type]\(バックアップの管理の種類\)** で、**[SQL in Azure VM]\(Azure VM 内の SQL\)** を選択します。

    ![[SQL in Azure VM]\(Azure VM 内の SQL\) を選択する](./media/backup-azure-sql-database/sql-restore-backup-items.png)


3. 保護を停止するデータベースを選択します。

    ![保護を停止するデータベースを選択する](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)


5. データベース メニューで、**[バックアップの停止]** を選択します。

    ![[バックアップの停止] を選択する](./media/backup-azure-sql-database/stop-db-button.png)


6. **[バックアップの停止]** メニューで、データを保持するか削除するかを選択します。 必要に応じて、理由とコメントを入力します。

    ![[バックアップの停止] メニュー](./media/backup-azure-sql-database/stop-backup-button.png)

7. **[バックアップの停止]** をクリックします。

  

### <a name="resume-protection-for-a-sql-database"></a>SQL Database での保護の再開

SQL データベースの保護を停止するときに **[バックアップ データの保持]** オプションを選択した場合は、保護を再開することができます。 バックアップ データが保持されていなかった場合、保護は再開できません。

1. SQL データベースの保護を再開するには、バックアップ項目を開き、**[バックアップの再開]** を選択します。

    ![[バックアップの再開] を選択してデータベースの保護を再開する](./media/backup-azure-sql-database/resume-backup-button.png)

2. **[バックアップ ポリシー]** メニューで、ポリシーを選択して、**[保存]** を選択します。

## <a name="run-an-on-demand-backup"></a>オンデマンド バックアップを実行する

異なる種類のオンデマンド バックアップを実行できます。

* 完全バックアップ
* コピーのみの完全バックアップ
* 差分バックアップ
* ログ バックアップ

SQL Server のバックアップの種類に関する[詳細情報](backup-architecture.md#sql-server-backup-types)。

## <a name="unregister-a-sql-server-instance"></a>SQL Server インスタンスを登録解除する

保護を無効にした後で、コンテナーを削除する前に、SQL Server インスタンスの登録を解除します。

1. コンテナー ダッシュボードの **[管理]** で、**[バックアップ インフラストラクチャ]** を選択します。  

   ![[バックアップ インフラストラクチャ] を選択する](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. **[管理サーバー]** で、**[保護されたサーバー]** を選択します。

   ![[保護されたサーバー] を選択する](./media/backup-azure-sql-database/protected-servers.png)


3. **[保護されたサーバー]** で、登録解除するサーバーを選択します。 コンテナーを削除するには、すべてのサーバーの登録を解除する必要があります。

4. 保護されたサーバーを右クリックして、**[削除]** を選択します。

   ![[削除] を選択する](./media/backup-azure-sql-database/delete-protected-server.png)


## <a name="next-steps"></a>次の手順

SQL Server データベースのバックアップに関するトラブルシューティングの情報を[確認](backup-sql-server-azure-troubleshoot.md)してください。
