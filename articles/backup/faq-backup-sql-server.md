---
title: Azure Backup による Azure VM での SQL Server データベースのバックアップについてよく寄せられる質問
description: Azure Backup による Azure VM での SQL Server データベースのバックアップについてよくある質問にお答えします。
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 8/16/2018
ms.author: sogup
ms.openlocfilehash: a14406733ff60d53d4bf7792ff0c9a015c57d9b3
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430926"
---
# <a name="faq-on-sql-server-running-on-azure-vm-backup"></a>Azure VM で実行されている SQL Server のバックアップに関する FAQ

この記事では、[Azure Backup](backup-overview.md) サービスによる Azure VM で実行されている SQL Server データベースのバックアップについてよくある質問にお答えします。

> [!NOTE]
> 現在、この機能はパブリック プレビュー段階にあります。



## <a name="can-i-throttle-the-backup-speed"></a>バックアップの速度を調整することができますか?

はい。 バックアップ ポリシーが実行される速度を調整して、SQL Server インスタンスへの影響を最小限に抑えることができます。 設定を変更するには:
1. SQL Server インスタンスで、*C:\Program Files\Azure Workload Backup\bin フォルダー*に **ExtensionSettingsOverrides.json** ファイルを作成します。
2. **ExtensionSettingsOverrides.json** ファイルで、**DefaultBackupTasksThreshold** の設定を小さな値 (たとえば、5) に変更します。 <br>
  ` {"DefaultBackupTasksThreshold": 5}`

3. 変更を保存します。 ファイルを閉じます。
4. SQL Server インスタンスで、**タスク マネージャー**を開きます。 **AzureWLBackupCoordinatorSvc** サービスを再起動します。

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>セカンダリ レプリカから完全バックアップを実行できますか?
いいえ。 この機能はサポートされていません。

## <a name="do-successful-backup-jobs-create-alerts"></a>成功したバックアップ ジョブでアラートが作成されますか?

いいえ。 成功したバックアップ ジョブではアラートは生成されません。 アラートは、失敗したバックアップ ジョブに対してのみ送信されます。

## <a name="can-i-see-scheduled-backup-jobs-in-the-jobs-menu"></a>スケジュールされたバックアップ ジョブを [ジョブ] メニューで確認できますか?

いいえ。 **[バックアップ ジョブ]** メニューにはオンデマンド ジョブの詳細が表示されますが、スケジュールされたバックアップ ジョブは表示されません。 スケジュールされたバックアップ ジョブのいずれかが失敗した場合は、失敗したジョブのアラートで詳細を確認できます。 スケジュールされたバックアップ ジョブとアドホック バックアップ ジョブをすべて監視するには、[SQL Server Management Studio](manage-monitor-sql-database-backup.md) を使用します。

## <a name="are-future-databases-automatically-added-for-backup"></a>今後作成されるデータベースはバックアップに自動的に追加されますか?

いいえ。 SQL Server インスタンスの保護を構成する場合、サーバー レベルのオプションを選択すると、すべてのデータベースが追加されます。 保護を構成した後に SQL Server インスタンスにデータベースを追加する場合、新しいデータベースを保護するにはそれらを手動で追加する必要があります。 これらのデータベースは、構成された保護に自動的には含まれません。

##  <a name="how-do-i-restart-protection-after-changing-recovery-type"></a>復旧の種類を変更した後で保護を再び開始するにはどうすればよいですか?

完全バックアップをトリガーします。 ログ バックアップは想定どおりに開始されます。

## <a name="can-i-protect-availability-groups-on-premises"></a>オンプレミスの可用性グループを保護できますか?

いいえ。 Azure Backup では、Azure で実行している SQL Server を保護します。 Azure とオンプレミスのマシンの間にまたがっている可用性グループ (AG) は、プライマリ レプリカが Azure で実行されている場合にのみ保護できます。 また、Azure Backup では、Recovery Services コンテナーと同じ Azure リージョンで実行されているノードのみ保護します。

## <a name="can-i-protect-availability-groups-across-regions"></a>リージョンをまたいで可用性グループを保護できますか?

Azure Backup Recovery Services コンテナーは、Recovery Services コンテナーと同じリージョン内にあるすべてのノードを検出および保護できます。 複数の Azure リージョンにまたがる SQL Always On 可用性グループがある場合は、プライマリ ノードのあるリージョンからのバックアップを構成する必要があります。 Azure Backup によって、バックアップ設定に従って、可用性グループのすべてのデータベースを検出および保護することができるようになります。 バックアップの設定条件が満たされていない場合は、バックアップに失敗し、エラー通知が表示されます。

## <a name="can-i-exclude-databases-with-auto-protection-enabled"></a>自動保護を有効にしてデータベースを除外することができますか?

いいえ、[自動保護](backup-azure-sql-database.md#enable-auto-protection)はインスタンス全体に適用されます。 自動保護を使用して、インスタンス内のデータベースを選択的に保護することはできません。

## <a name="can-i-have-different-policies-in-an-auto-protected-instance"></a>自動保護されたインスタンスで異なるポリシーを使用できますか?

インスタンス内の一部のデータベースが既に保護されている場合、[自動保護](backup-azure-sql-database.md#enable-auto-protection)オプションを**オン**にした後も、それらのデータベースは引き続きそれぞれのポリシーで保護されます。 ただし、その時点で保護されていないすべてのデータベースと、それ以降に追加されるデータベースには、データベースを選択した後に **[バックアップの構成]** で定義する 1 つのポリシーだけが適用されます。 実際、保護されている他のデータベースとは異なり、自動保護されたインスタンスに含まれるデータベースについてはポリシーを変更することもできません。
それを行いたい場合、唯一の方法は、インスタンスの自動保護をいったん無効にして、そのデータベースのポリシーを変更することです。 その後は、このインスタンスの自動保護を再び有効にできます。

## <a name="if-i-delete-a-database-from-auto-protection-will-backups-stop"></a>自動保護からデータベースを削除した場合、バックアップは停止しますか?

いいえ、自動保護されたインスタンスからデータベースを削除した場合でも、そのデータベースのバックアップは試行されます。 これは、削除されたデータベースは、**[バックアップ項目]** で非正常として表示されるようになり、まだ保護対象として扱われることを意味します。

このデータベースの保護を停止する唯一の方法は、インスタンスでの[自動保護](backup-azure-sql-database.md#enable-auto-protection)をいったん無効にした後、そのデータベースの **[バックアップ項目]** で **[バックアップの停止]** オプションを選択します。 その後は、このインスタンスの自動保護を再び有効にできます。

##  <a name="why-cant-i-see-an-added-database-for-an-auto-protected-instance"></a>自動保護されたインスタンスに追加したデータベースを表示できないのはなぜですか?

[自動保護](backup-azure-sql-database.md#enable-auto-protection)されたインスタンスに新しく追加されたデータベースは、保護された項目ですぐに保護済みとして表示されない場合があります。 これは、検出が通常は 8 時間ごとに実行されるためです。 ただし、次の図のように、ユーザーは **[DB の再検出]** オプションを使用して手動検出を実行し、すぐに新しいデータベースを検出して保護することができます。

  ![新しく追加されたデータベースの表示](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="next-steps"></a>次の手順

Azure VM で実行されている SQL Server データベースをバックアップする[方法を学習](backup-azure-sql-database.md)してください。
