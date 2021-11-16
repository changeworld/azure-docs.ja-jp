---
title: Azure ファイル共有のバックアップを管理する
description: この記事では、Azure Backup によってバックアップされた Azure ファイル共有を管理および監視するための一般的なタスクについて説明します。
ms.topic: conceptual
ms.date: 11/03/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 465331a39f5fc05d81a4ff06cd58c3962b301831
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132332043"
---
# <a name="manage-azure-file-share-backups"></a>Azure ファイル共有のバックアップを管理する

この記事では、[Azure Backup](./backup-overview.md) によってバックアップされた Azure ファイル共有を管理および監視するための一般的なタスクについて説明します。 管理タスクを **バックアップ センター** で実行する方法について説明します。

## <a name="monitor-jobs"></a>ジョブの監視

バックアップ操作または復元操作をトリガーすると、追跡用のジョブがバックアップ サービスによって作成されます。 **[バックアップ ジョブ]** ページで、すべてのジョブの進行状況を監視できます。

**[バックアップ ジョブ]** ページを開くには:

1. **バックアップ センター** に移動し、 **[監視]** セクションから **[バックアップ ジョブ]** を選択します。

   :::image type="content" source="./media/manage-afs-backup/backup-center-jobs-list-inline.png" alt-text="[監視] セクションの [バックアップ ジョブ] を示すスクリーンショット。" lightbox="./media/manage-afs-backup/backup-center-jobs-list-expanded.png":::

   **[バックアップ ジョブ]** ペインにすべてのジョブの状態が表示されます。

1. データソースの種類として **[Azure Files (Azure Storage)]** を選択し、任意の行を選択して特定のジョブの詳細を表示します。

   :::image type="content" source="./media/manage-afs-backup/backup-center-jobs-inline.png" alt-text="ジョブの一覧を示すスクリーンショット。" lightbox="./media/manage-afs-backup/backup-center-jobs-expanded.png":::
   
    >[!NOTE]
    >Azure Files に対応するバックアップ ジョブで転送されたデータは、コンテナーに転送するデータがないため MB 単位で 0 です。

## <a name="monitor-using-azure-backup-reports"></a>Azure Backup レポートを使用した監視

Azure Backup では、[Azure Monitor ログ](../azure-monitor/logs/log-analytics-tutorial.md)と [Azure ブック](../azure-monitor/visualize/workbooks-overview.md)を使用するレポート ソリューションが提供されます。 これらのリソースを利用すると、バックアップに関するさまざまな分析情報を得られます。 これらのレポートを利用して、Azure Files のバックアップ項目、項目レベルのジョブ、アクティブ ポリシーの詳細を可視化できます。 バックアップ レポートで使用できるメール レポート機能を使用すると、自動化されたタスクを作成して、メールで定期的なレポートを受信できます。 Azure Backup レポートを構成および表示する方法をご[確認](./configure-reports.md#get-started)ください。

## <a name="create-a-new-policy"></a>新しいポリシーの作成

**バックアップ センター** の **[バックアップ ポリシー]** セクションから、Azure ファイル共有をバックアップするための新しいポリシーを作成できます。 ファイル共有のバックアップを構成した際に作成されたすべてのポリシーは、**Azure ファイル共有** という **ポリシーの種類** で表示されます。

新しいバックアップ ポリシーを作成するには、次の手順を実行します。

1. **バックアップ センター** の **[バックアップ ポリシー]** ペインで、 **[+ 追加]** を選択します。

   :::image type="content" source="./media/manage-afs-backup/backup-center-add-policy-inline.png" alt-text="新しいバックアップ ポリシーの作成を開始するオプションを示すスクリーンショット。" lightbox="./media/manage-afs-backup/backup-center-add-policy-expanded.png":::

1. データソースの種類として **[Azure Files (Azure Storage)]** を選択し、ポリシーを作成するコンテナーを選択して、 **[続行]** をクリックします。

   :::image type="content" source="./media/manage-afs-backup/azure-file-share-select-vault-for-policy.png" alt-text="ポリシーの種類として Azure ファイル共有を選択することを示すスクリーンショット。":::

1. **Azure ファイル共有** の **[バックアップ ポリシー]** ペインが開くので、ポリシー名を指定します。

1. **[バックアップ スケジュール]** で、適切なバックアップの頻度を 選択します ( **[毎日]** または **[毎時]** )。

   :::image type="content" source="./media/manage-afs-backup/backup-frequency-types.png" alt-text="バックアップの頻度の種類を示すスクリーンショット。":::

   - **毎日**: 1 日に 1 回のバックアップをトリガーします。 毎日の頻度の場合は、次の適切な値を選択します。

     - **時刻**: バックアップ ジョブをトリガーする必要がある時刻のタイムスタンプ。
     - **タイム ゾーン**: バックアップ ジョブに対応するタイムゾーン。

   - **毎時**: 1 日に複数回のバックアップをトリガーします。 毎時の頻度の場合は、次の適切な値を選択します。
   
     - **スケジュール**: 連続するバックアップの間の時間間隔 (時間単位)。
     - **開始時刻**: その日の最初のバックアップ ジョブをトリガーする必要がある時刻。
     - **期間**: バックアップ期間 (時間単位) を表します。つまり、選択したスケジュールに従ってバックアップ ジョブをトリガーする必要がある期間です。
     - **タイム ゾーン**: バックアップ ジョブに対応するタイムゾーン。
     
     たとえば、RPO (回復ポイントの目標) の要件が 4 時間であり、勤務時間が午前 9 時から午後 9 時であるとします。 これらの要件を満たすには、バックアップ スケジュールの構成は次のようになります。
    
     - スケジュール: 4 時間ごと
     - 開始時刻: 午前 9 時 
     - 期間: 12 時間 
     
     :::image type="content" source="./media/manage-afs-backup/hourly-backup-frequency-values-scenario.png" alt-text="毎時のバックアップ頻度値の例を示すスクリーンショット。":::

     選択した内容に基づいて、バックアップ ジョブの詳細 (バックアップ ジョブがトリガーされる時刻のタイムスタンプ) がバックアップ ポリシー ブレードに表示されます。

1. **[保持期間]** には、バックアップの適切な保持期間値 (毎日、毎週、毎月、毎年というタグが付けられています) を指定します。

1. ポリシーのすべての属性を定義したら、 **[作成]** をクリックします。
  
### <a name="view-policy"></a>ポリシーの表示

既存のバックアップ ポリシーを表示するには:

1. **バックアップ センター** に移動し、 **[管理]** セクションから **[バックアップ ポリシー]** を選択します。

   コンテナー全体で構成されているすべてのバックアップ ポリシーが表示されます。

   :::image type="content" source="./media/manage-afs-backup/backup-center-policies-list-inline.png" alt-text="すべてのバックアップ ポリシーを示すスクリーンショット。" lightbox="./media/manage-afs-backup/backup-center-policies-list-expanded.png":::

1. **[Azure Files (Azure Storage)]** に固有のポリシーを表示するには、データソースの種類として **[Azure ファイル共有]** を選択します。

## <a name="modify-policy"></a>ポリシーを変更する

バックアップ ポリシーを変更し、バックアップの頻度または保持期間を変更することができます。

ポリシーを変更するには:

1. **バックアップ センター** に移動し、 **[管理]** セクションから **[バックアップ ポリシー]** を選択します。

   コンテナー全体で構成されているすべてのバックアップ ポリシーが表示されます。

   :::image type="content" source="./media/manage-afs-backup/backup-center-policies-list-inline.png" alt-text="コンテナー内のすべてのバックアップ ポリシーを示すスクリーンショット。" lightbox="./media/manage-afs-backup/backup-center-policies-list-expanded.png":::

1. Azure ファイル共有に固有のポリシーを表示するには、データソースの種類として **[Azure Files (Azure Storage)]** を選択します。

   更新するポリシーをクリックします。

1. **[スケジュール]** ペインが開きます。 必要に応じて **バックアップ スケジュール** と **保有期間の範囲** を編集し、 **[保存]** をクリックします。

   ウィンドウに _[更新が進行中です]_ というメッセージが表示されます。 ポリシーの変更が正常に更新されると、 _[バックアップ ポリシーを正常に更新しました]_ というメッセージが表示されます。

   ![変更したポリシーを保存する](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>ファイル共有の保護の停止

Azure ファイル共有の保護を停止するには、次の 2 つの方法があります。

* 今後のすべてのバックアップ ジョブを停止し、"*すべての復旧ポイントを削除*" します。
* 今後のすべてのバックアップ ジョブを停止しますが、"*復旧ポイントはそのままにします*"。

Azure Backup によって作成された基になるスナップショットが保持されるため、ストレージに復旧ポイントをそのまま残す場合にはコストが伴う可能性があります。 復旧ポイントを残す利点は、後でファイル共有を復元できることです。 復旧ポイントを保持するためのコストについては、「 [価格の詳細](https://azure.microsoft.com/pricing/details/backup/)」を参照してください。 すべての復旧ポイントを削除することを決めた場合、ファイル共有を復元することはできません。

Azure ファイル共有の保護を停止するには:

1. **バックアップ センター** に移動し、メニューから **[バックアップ インスタンス]** を選択し、データソースの種類として **[Azure Files (Azure Storage)]** を選択します。

   :::image type="content" source="./media/manage-afs-backup/azure-file-share-backup-instances-inline.png" alt-text="データ型として Azure Files を選択することを示すスクリーンショット。" lightbox="./media/manage-afs-backup/azure-file-share-backup-instances-expanded.png":::

1. 保護を停止するバックアップ項目を選択します。

1. **[バックアップの停止]** オプションを選択します。

   ![[バックアップの停止] を選択する](./media/manage-afs-backup/stop-backup.png)

1. **[バックアップの停止]** ペインで、 **[バックアップ データの保持]** または **[バックアップ データの削除]** を選択します。 次に、 **[バックアップの停止]** を選択します。

    ![[バックアップデータの保持] または [バックアップデータの削除] を選択する](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>ファイル共有の保護の再開

ファイル共有の保護を停止するときに **[バックアップ データの保持]** オプションを選択した場合は、保護を再開することができます。 **[バックアップ データの削除]** オプションを選択した場合は、ファイル共有の保護を再開することはできません。

Azure ファイル共有の保護を再開するには:

1. **バックアップ センター** に移動し、メニューから **[バックアップ インスタンス]** を選択し、データソースの種類として **[Azure Files (Azure Storage)]** を選択します。

   :::image type="content" source="./media/manage-afs-backup/azure-file-share-backup-instances-inline.png" alt-text="データソースの種類として Azure Files を選択することを示すスクリーンショット。" lightbox="./media/manage-afs-backup/azure-file-share-backup-instances-expanded.png":::

1. 保護を再開するバックアップ項目を選択します。

1. **[バックアップの再開]** オプションを選択します。

   ![[バックアップの再開] を選択する](./media/manage-afs-backup/resume-backup.png)

1. **[バックアップ ポリシー]** ペインが開きます。 バックアップを再開するためのポリシーを選択します。

1. バックアップ ポリシーを選択したら、 **[保存]** を選択します。

   ポータルに _[更新が進行中です]_ というメッセージが表示されます。 バックアップが正常に再開されると、 _[保護された Azure ファイル共有のバックアップ ポリシーが正常に更新されました]_ というメッセージが表示されます。

   ![正常に更新されたバックアップ ポリシー](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>バックアップ データの削除

**バックアップの停止** ジョブ中、または保護を停止した後はいつでも、ファイル共有のバックアップを削除できます。 数日または数週間待ってから復旧ポイントを削除する方が有益である場合もあります。 バックアップ データを削除するときは、特定の復旧ポイントを削除対象として選択することができません。 バックアップ データを削除することに決めた場合は、そのファイル共有に関連付けられている復旧ポイントもすべて削除されます。

次の手順では、ファイル共有の保護が停止されていることを前提としています。

Azure ファイル共有のバックアップ データを削除するには:

1. バックアップ ジョブが停止されたら、 **[バックアップ項目]** ダッシュボードで **[バックアップの再開]** オプションと **[バックアップ データの削除]** オプションが使用できるようになります。 **[バックアップ データの削除]** オプションを選択します。

   ![バックアップ データの削除](./media/manage-afs-backup/delete-backup-data.png)

1. **[バックアップ データの削除]** ペインが開きます。 ファイル共有の名前を入力して削除することを確認します。 必要に応じて、 **[理由]** または **[コメント]** ボックスに詳細情報を入力します。 バックアップ データを削除することを確認したら、 **[削除]** をクリックします。

   ![データ削除の確認](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-a-storage-account"></a>ストレージ アカウントの登録を解除する

特定のストレージ アカウントのファイル共有を別の Recovery Services コンテナーを使用して保護するには、まず最初に、そのストレージ アカウント内の[すべてのファイル共有の保護を停止](#stop-protection-on-a-file-share)します。 次に、現在保護に使用されている Recovery Services コンテナーからアカウントの登録を解除します。

次の手順では、登録を解除するストレージ アカウント内のすべてのファイル共有に対する保護が停止されていることを前提としています。

ストレージ アカウントの登録を解除するには:

1. ストレージ アカウントが登録されている Recovery Services コンテナーを開きます。
1. **[概要]** ペインで、 **[管理]** セクションの **[バックアップ インフラストラクチャ]** オプションを選択します。

   ![[バックアップ インフラストラクチャ] を選択する](./media/manage-afs-backup/backup-infrastructure.png)

1. **[バックアップ インフラストラクチャ]** ペインが開きます。 **[Azure Storage アカウント]** セクションから **[ストレージ アカウント]** を選択します。

   ![[ストレージ アカウント] を選択する](./media/manage-afs-backup/storage-accounts.png)

1. **[ストレージ アカウント]** を選択すると、コンテナーに登録されているストレージ アカウントの一覧が表示されます。
1. 登録を解除するストレージ アカウントを右クリックし、 **[登録解除]** を選択します。

   ![[登録解除] を選択する](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>次のステップ

詳細については、[Azure ファイル共有のトラブルシューティング](./troubleshoot-azure-files.md)に関する記事を参照してください。
