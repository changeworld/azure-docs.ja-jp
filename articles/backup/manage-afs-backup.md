---
title: Azure ファイル共有のバックアップを管理する
description: この記事では、Azure Backup サービスによってバックアップされた Azure ファイル共有を管理および監視するための一般的なタスクについて説明します。
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: a5477d021b6e3600693e183d8707e11592b7cc38
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294893"
---
# <a name="manage-azure-file-share-backups"></a>Azure ファイル共有のバックアップを管理する

この記事では、[Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) サービスによってバックアップされた Azure ファイル共有を管理および監視するための一般的なタスクについて説明します。 Recovery Services コンテナーで次の管理タスクを実行する方法について説明します。

* [ジョブの監視](#monitor-jobs)
* [新しいポリシーの作成](#create-a-new-policy)
* [ポリシーの変更](#modify-policy)
* [ファイル共有の保護の停止](#stop-protection-on-a-file-share)
* [ファイル共有の保護の再開](#resume-protection-on-a-file-share)
* [バックアップ データの削除](#delete-backup-data)
* [ストレージ アカウントの登録解除](#unregister-storage-account)

## <a name="monitor-jobs"></a>ジョブの監視

バックアップ操作または復元操作をトリガーすると、追跡用のジョブがバックアップ サービスによって作成されます。 **[バックアップ ジョブ]** ページで、すべてのジョブの進行状況を監視できます。

**[バックアップ ジョブ]** ページを開くには:

1. ファイル共有のバックアップを構成するために使用した Recovery Services コンテナーを開きます。 **[概要]** ブレードの **[監視]** セクションで、 **[バックアップ ジョブ]** をクリックします。

   ![監視セクションのバックアップ ジョブ](./media/manage-afs-backup/backup-jobs.png)

2. [OK] をクリックすると、 **[バックアップ ジョブ]** ブレードが表示され、すべてのジョブの状態が一覧表示されます。 監視するファイル共有に対応するワークロード名をクリックできます。

   ![ワークロード名](./media/manage-afs-backup/workload-name.png)

## <a name="create-a-new-policy"></a>新しいポリシーの作成

Recovery Services コンテナーの **[バックアップ ポリシー]** セクションから、Azure ファイル共有をバックアップするための新しいポリシーを作成できます。 ファイル共有のバックアップを構成した際に作成されたすべてのポリシーは、Azure ファイル共有というポリシーの種類で表示されます。

既存のバックアップ ポリシーを表示するには:

1. ファイル共有のバックアップを構成するために使用した Recovery Services コンテナーを開き、[Recovery Services コンテナー] メニューで、[管理] セクションの **[バックアップ ポリシー]** クリックします。 コンテナーで構成されているすべてのバックアップ ポリシーが一覧表示されます。

   ![すべてのバックアップ ポリシー](./media/manage-afs-backup/all-backup-policies.png)

2. Azure ファイル共有に固有のポリシーを表示するには、右上のドロップダウンから **[Azure ファイル共有]** を選択します。

   ![Azure ファイル共有を選択する](./media/manage-afs-backup/azure-file-share.png)

新しいバックアップ ポリシーを作成するには:

1. [バックアップ ポリシー] ブレードで **[+追加]** をクリックします。

   ![新しいバックアップ ポリシー](./media/manage-afs-backup/new-backup-policy.png)

2. **[追加]** ブレードで、 **[ポリシーの種類]** として **[Azure ファイル共有]** を選択します。 Azure ファイル共有の [バックアップ ポリシー] ブレードが開きます。 復旧ポイントのポリシー名、バックアップ頻度、保持期間を指定します。 ポリシーの定義が完了したら、 **[OK]** をクリックします。

   ![バックアップ ポリシーの定義](./media/manage-afs-backup/define-backup-policy.png)

## <a name="modify-policy"></a>ポリシーの変更

バックアップ ポリシーを変更し、バックアップの頻度または保持期間を変更することができます。

ポリシーを変更するには:

1. ファイル共有のバックアップを構成するために使用した Recovery Services コンテナーを開き、[Recovery Services コンテナー] メニューで、[管理] セクションの **[バックアップ ポリシー]** クリックします。 コンテナーで構成されているすべてのバックアップ ポリシーが一覧表示されます。

   ![コンテナー内のすべてのバックアップ ポリシー](./media/manage-afs-backup/all-backup-policies-modify.png)

2. Azure ファイル共有に固有のポリシーを表示するには、右上のドロップダウンから **[Azure ファイル共有]** を選択します。 変更するバックアップ ポリシーをクリックします。

   ![変更する Azure ファイル共有](./media/manage-afs-backup/azure-file-share-modify.png)

3. **[スケジュール]** ブレードが開きます。 バックアップ スケジュール、または保有期間の範囲を必要に応じて編集し、 **[保存]** をクリックします。 ブレードに "更新中" というメッセージが表示され、ポリシーの変更が正常に更新されると、"Successfully updated the backup policy" (バックアップ ポリシーを正常に更新しました) というメッセージが表示されます。

   ![変更したポリシーを保存する](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>ファイル共有の保護の停止

Azure ファイル共有の保護を停止するには、次の 2 つの方法があります。

* 今後のバックアップ ジョブすべてを停止し、*すべての復旧ポイントを削除する*
* 今後のバックアップ ジョブすべてを停止するが、*復旧ポイントはそのままにする*

Azure Backup によって作成された基になるスナップショットが保持されるため、ストレージに復旧ポイントをそのまま残す場合にはコストが伴う可能性があります。 一方、復旧ポイントを残す利点は、必要に応じて後でファイル共有を復元できることです。 復旧ポイントを保持するためのコストについては、「 [価格の詳細](https://azure.microsoft.com/pricing/details/backup/)」を参照してください。 すべての復旧ポイントを削除するように選択した場合、ファイル共有を復元することはできません。

Azure ファイル共有の保護を停止するには:

1. ファイル共有の復旧ポイントが含まれている Recovery Services コンテナーを開き、[保護された項目] セクションの **[バックアップ項目]** をクリックします。 バックアップ項目の種類の一覧が表示されます。

   ![[バックアップ項目]](./media/manage-afs-backup/backup-items.png)

2. **[バックアップの管理の種類]** の一覧で、 **[Azure Storage (Azure Files)]** を選択します。 **(Azure Storage (Azure Files)) のバックアップ項目**の一覧が表示されます。

   ![Azure Storage (Azure Files) のバックアップを選択する](./media/manage-afs-backup/azure-storage-azure-files.png)

3. **[バックアップ項目 (Azure Storage (Azure Files))]** の一覧で、保護を停止するバックアップ項目を選択します。

4. **[バックアップ項目]** ブレードのメニューから、 **[バックアップの停止]** オプションを選択します。

   ![[バックアップの停止] を選択する](./media/manage-afs-backup/stop-backup.png)

5. **[バックアップの停止]** ブレードで **[バックアップ データの保持]** を選択するか、 **[バックアップ データの削除]** をクリックして、 **[バックアップの停止]** をクリックします。

    ![バックアップ データの保持または削除を選択する](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>ファイル共有の保護の再開

ファイル共有の保護を停止するときに **[バックアップ データの保持]** オプションを選択した場合は、保護を再開することができます。 **[バックアップ データの削除]** オプションを選択した場合は、ファイル共有の保護を再開できません。

Azure ファイル共有の保護を再開するには:

1. ファイル共有の復旧ポイントが含まれている Recovery Services コンテナーを開き、[保護された項目] セクションの **[バックアップ項目]** をクリックします。 バックアップ項目の種類の一覧が表示されます。

   ![再開用に項目をバックアップする](./media/manage-afs-backup/backup-items-resume.png)

2. **[バックアップの管理の種類]** の一覧で、 **[Azure Storage (Azure Files)]** を選択します。 **(Azure Storage (Azure Files)) のバックアップ項目**の一覧が表示されます。

   ![Azure Storage (Azure Files) の一覧](./media/manage-afs-backup/azure-storage-azure-files.png)

3. **[バックアップ項目 (Azure Storage (Azure Files))]** の一覧で、保護を再開するバックアップ項目を選択します。

4. **[バックアップ項目]** ブレードのメニューから、 **[バックアップの再開]** オプションを選択します。

   ![[バックアップの再開] を選択する](./media/manage-afs-backup/resume-backup.png)

5. **[バックアップ ポリシー]** ブレードが開き、任意のポリシーを選択してバックアップを再開できます。

6. 目的の **[バックアップ ポリシー]** を選択した後、 **[保存]** をクリックします。 ポータルに "更新中" というメッセージが表示され、バックアップが正常に再開されると、"Successfully updated backup Policy for Protected Azure File Share" (保護された Azure ファイル共有のバックアップ ポリシーが正常に更新されました) というメッセージが表示されます。

   ![正常に更新されたバックアップ ポリシー](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>バックアップ データの削除

**バックアップの停止**ジョブ中、または保護を停止した後はいつでも、ファイル共有のバックアップを削除できます。 数日または数週間待ってから復旧ポイントを削除する方が便利な場合もあります。 バックアップ データを削除するときは、特定の復旧ポイントを削除対象として選択することができません。 バックアップ データを削除するように選択すると、そのファイル共有に関連付けられている復旧ポイントもすべて削除されます。

次の手順では、ファイル共有の保護が停止されていることを前提としています。

Azure ファイル共有のバックアップ データを削除するには:

1. バックアップ ジョブが停止されたら、 **[バックアップ項目]** ダッシュボードで **[バックアップの再開]** オプションと **[バックアップ データの削除]** オプションが使用できるようになります。 **[バックアップ項目]** ブレードのメニューで、 **[バックアップ データの削除]** オプションをクリックします。

   ![バックアップ データの削除](./media/manage-afs-backup/delete-backup-data.png)

2. **[バックアップ データの削除]** ブレードが開きます。 ファイル共有の名前を入力して削除することを確認します。 必要に応じて、削除の**理由**または**コメント**を入力します。 バックアップ データを削除することを確認したら、 **[削除]** をクリックします。

   ![データ削除の確認](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-storage-account"></a>ストレージ アカウントの登録解除

特定のストレージ アカウントのファイル共有を別の Recovery Services コンテナーを使用して保護する場合は、まず最初に、そのストレージ アカウント内の[すべてのファイル共有の保護を停止](#stop-protection-on-a-file-share)します。 次に、現在保護に使用されている Recovery Services コンテナーからアカウントの登録を解除します。

次の手順では、登録を解除するストレージ アカウント内のすべてのファイル共有に対する保護が停止されていることを前提としています。

ストレージ アカウントの登録を解除するには:

1. ストレージ アカウントが登録されている Recovery Services コンテナーを開きます。
2. **[概要]** ブレードの **[管理]** セクションで、 **[バックアップ インフラストラクチャ]** オプションをクリックします。

   ![[バックアップ インフラストラクチャ] をクリックする](./media/manage-afs-backup/backup-infrastructure.png)

3. **[バックアップ インフラストラクチャ]** ブレードが開きます。 このブレードの **[Azure Storage アカウント]** セクションのから **[ストレージ アカウント]** をクリックします。

   ![[ストレージ アカウント]をクリックする](./media/manage-afs-backup/storage-accounts.png)

4. **[ストレージ アカウント]** をクリックすると、コンテナーに登録されているストレージ アカウントの一覧が表示されます。
5. 登録を解除するストレージ アカウントを右クリックし、 **[登録解除]** を選択します。

   ![[登録解除] を選択する](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>次のステップ

詳細については、[Azure ファイル共有のバックアップと復元に関する障害のトラブルシューティング](https://docs.microsoft.com/azure/backup/troubleshoot-azure-files)に関する記事を参照してください
