---
title: MARS エージェントのバックアップを管理および監視する
description: Azure Backup サービスを使用して Microsoft Azure Recovery Services (MARS) エージェントのバックアップを管理および監視する方法について説明します。
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: b7e947e7fd473ec787d49ffe82532ffd5b6a98d1
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/26/2019
ms.locfileid: "75496976"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>Azure Backup サービスを使用して Microsoft Azure Recovery Services (MARS) エージェントのバックアップを管理する

この記事では、Microsoft Azure Recovery Services エージェントによってバックアップされたファイルやフォルダーを管理する方法について説明します。

## <a name="modify-a-backup-policy"></a>バックアップ ポリシーを変更する

バックアップ ポリシーを変更すると、新しい項目の追加、バックアップからの既存項目の削除、[除外の設定] を使用したバックアップ対象からのファイルの除外が可能です。

- **[項目の追加]** : このオプションは、新しい項目をバックアップに追加する場合にのみ使用します。 既存の項目を削除するには、 **[項目の削除]** または **[除外の設定]** オプションを使用します。  
- **[項目の削除]** : このオプションは、バックアップ対象から項目を削除するために使用します。
  - ボリューム内のすべての項目を削除する場合は、 **[項目の削除]** ではなく **[除外の設定]** を使用します。
  - ボリューム内のすべての選択を解除すると、項目の以前のバックアップが、最終バックアップ時の保持設定に従って保持されます (変更のスコープなし)。
  - これらの項目を再選択すると最初の完全バックアップが行われ、新しいポリシーの変更は以前のバックアップには適用されません。
  - ボリューム全体の選択を解除すると、保持ポリシーを変更するためのスコープがなくても過去のバックアップが保持されます。
- **[除外の設定]** : 特定の項目をバックアップ対象から除外するには、このオプションを使用します。

### <a name="add-new-items-to-existing-policy"></a>既存のポリシーに新しい項目を追加する

1. **[アクション]** で、 **[バックアップのスケジュール]** をクリックします。

    ![Schedule a Windows Server backup](./media/backup-configure-vault/schedule-first-backup.png)

2. **[ポリシー項目の選択]** タブで、 **[Modify backup schedule for your files and folders] (ファイルとフォルダーのバックアップ スケジュールを変更する)** を選択し、 **[次へ]** をクリックします。

    ![ポリシー項目の選択](./media/backup-azure-manage-mars/select-policy-items.png)

3. **[Modify or stop schedule backup] (バックアップ スケジュールの変更または停止)** タブで、 **[バックアップ項目または時刻を変更する]** を選択し、 **[次へ]** をクリックします。

    ![バックアップの変更またはスケジュール](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. **[バックアップする項目の選択]** タブで **[項目の追加]** をクリックし、バックアップする項目を追加します。

    ![バックアップの変更またはスケジュールでの [項目の追加]](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. **[項目の選択]** ウィンドウで、追加するファイルまたはフォルダーを選択し、 **[OK]** をクリックします。

    ![項目の選択](./media/backup-azure-manage-mars/select-item.png)

6. 後続の手順を完了し、 **[完了]** をクリックして操作を完了します。

### <a name="add-exclusion-rules-to-existing-policy"></a>既存のポリシーに除外ルールを追加する

除外ルールを追加して、バックアップ対象にしないファイルやフォルダーをスキップできます。 これは、新しいポリシーの定義時または既存ポリシーの変更時に実行できます。

1. [アクション] ペインで、 **[バックアップのスケジュール]** をクリックします。 **[バックアップする項目の選択]** に移動し、 **[除外の設定]** をクリックします。

    ![項目の選択](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. **[除外の設定]** で **[除外の追加]** をクリックします。

    ![項目の選択](./media/backup-azure-manage-mars/add-exclusion.png)

3. **[除外する項目の選択]** から、ファイルやフォルダーを参照し、除外する項目を選択して **[OK]** をクリックします。

    ![項目の選択](./media/backup-azure-manage-mars/select-items-exclude.png)

4. 既定では、選択したフォルダー内のすべての**サブフォルダー**が除外されます。 これは、 **[はい]** または **[いいえ]** を選択することで変更できます。 下に示すように、除外するファイルの種類を編集および指定することができます。

    ![項目の選択](./media/backup-azure-manage-mars/subfolders-type.png)

5. 後続の手順を完了し、 **[完了]** をクリックして操作を完了します。

### <a name="remove-items-from-existing-policy"></a>既存のポリシーから項目を削除する

1. [アクション] ペインで、 **[バックアップのスケジュール]** をクリックします。 **[バックアップする項目の選択]** に移動します。 一覧で、バックアップ スケジュールから削除するファイルやフォルダーを選択し、 **[項目の削除]** をクリックします。

    ![項目の選択](./media/backup-azure-manage-mars/select-items-remove.png)

> [!NOTE]
> ポリシーからボリュームを完全に削除するときには、注意して操作を進めてください。  もう一度追加する必要がある場合、それは新しいボリュームとして扱われます。 次のスケジュールされたバックアップでは、増分バックアップではなく初回バックアップ (完全バックアップ) が実行されます。 後で項目を一時的に削除して追加する必要がある場合は、完全バックアップではなく増分バックアップとなるように、 **[項目の削除]** ではなく **[除外の設定]** を使用することをお勧めします。

2. 後続の手順を完了し、 **[完了]** をクリックして操作を完了します。

## <a name="stop-protecting-files-and-folder-backup"></a>ファイルおよびフォルダーのバックアップの保護を停止する

ファイルおよびフォルダーのバックアップの保護を停止する方法は 2 つあります。

- **保護を停止してバックアップ データを保持します**。
  - このオプションでは、今後、バックアップ ジョブによる保護がすべて停止されます。
  - Azure Backup サービスは、保持ポリシーに基づいて、バックアップされた復旧ポイントを保持します。
  - 有効期限になっていない復旧ポイントのバックアップ データを復元することができます。
  - 保護を再開する場合は、*バックアップ スケジュールを再有効化する*オプションを使用できます。 その後は、新しい保持ポリシーに基づいてデータが保持されます。
- **保護を停止してバックアップ データを削除します**。
  - このオプションでは、今後、バックアップ ジョブによるデータの保護がすべて停止され、すべての復旧ポイントが削除されます。
  - ユーザーは、バックアップ データの削除に関するアラート メールを受信します。このメールには、"*Your data for this Backup item has been deleted. (このバックアップ項目のデータは削除されました。)This data will be temporarily available for 14 days, after which it will be permanently deleted (このデータは 14 日間一時的に使用できるようになり、その後、完全に削除されます)* " というメッセージと、"*Reprotect the Backup item within 14 days to recover your data. (データを復旧するには、14 日以内にバックアップ項目を再保護してください。)* という推奨される操作が記載されています。
  - 保護を再開するには、削除操作から 14 日以内に再保護します。

### <a name="stop-protection-and-retain-backup-data"></a>保護を停止してバックアップ データを保持する

1. MARS 管理コンソールを開き、 **[アクション]** ウィンドウに移動して、 **[バックアップのスケジュール]** を選択します。

    ![スケジュールされたバックアップを変更または停止する。](./media/backup-azure-manage-mars/mars-actions.png)
1. **[ポリシー項目の選択]** ページで、 **[ファイルとフォルダーのバックアップ スケジュールを変更します]** を選択し、 **[次へ]** をクリックします。

    ![スケジュールされたバックアップを変更または停止する。](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. **[スケジュールされたバックアップの変更または停止]** ページで、 **[Stop using this backup schedule, but keep the stored backups until a schedule is activated again] (このバックアップ スケジュールの使用を中止するが、スケジュールが再度アクティブ化されるまで保存されているバックアップを保持する)** を選択します。 次に、 **[次へ]** を選択します。

    ![スケジュールされたバックアップを変更または停止する。](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. **[スケジュールされたバックアップの一時停止]** で情報を確認し、 **[完了]** をクリックします。

    ![スケジュールされたバックアップを変更または停止する。](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. **[Modify backup process]\(バックアップ プロセスの変更\)** で、スケジュールのバックアップの一時停止が成功状態であることを確認し、 **[閉じる]** をクリックして終了します。

### <a name="stop-protection-and-delete-backup-data"></a>保護を停止してバックアップ データを削除する

1. MARS 管理コンソールを開き、 **[アクション]** ウィンドウに移動して、 **[バックアップのスケジュール]** を選択します。
2. **[スケジュールされたバックアップの変更または停止]** ページで、 **[このバックアップ スケジュールの使用を中止して、保存されているバックアップをすべて削除する]** を選択します。 次に、 **[次へ]** を選択します。

    ![スケジュールされたバックアップを変更または停止する。](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. **[スケジュールされたバックアップの停止]** ページで、 **[完了]** を選択します。

    ![スケジュールされたバックアップを停止する。](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. セキュリティ PIN (暗証番号) の入力を求められます。これは手動で生成する必要があります。 これを行うには、最初に Azure portal にサインインします。
5. **[Recovery Services コンテナー]**  >  **[設定]**  >  **[プロパティ]** の順に移動します。
6. **[セキュリティ PIN]** の下にある **[生成]** を選択します。 この PIN をコピーします。 この PIN は 5 分間だけ有効です。
7. 管理コンソールに PIN を貼り付け、 **[OK]** を選択します。

    ![セキュリティ PIN を生成する。](./media/backup-azure-delete-vault/security-pin.png)

8. **[バックアップの進行状況の変更]** ページに、次のメッセージが表示されます。"*削除されたバックアップ データは 14 日間保持されます。14 日が経過すると、バックアップ データが完全に削除されます。* " と表示されます。  

    ![バックアップ インフラストラクチャを削除する。](./media/backup-azure-delete-vault/deleted-backup-data.png)

オンプレミスのバックアップ アイテムを削除したら、ポータルから次の手順を実行します。

## <a name="re-enable-protection"></a>保護を再有効化する

データを保持しつつ保護を停止し、保護を再開することにした場合は、バックアップ ポリシーの変更を利用してバックアップ スケジュールを再有効化することができます。

1. **[アクション]** で、 **[バックアップのスケジュール]** を選択します。
1. **[バックアップ スケジュールを再有効化します。バックアップ項目または時刻を変更することもできます]** を選択し、 **[次へ]** をクリックします。<br>

    ![バックアップ インフラストラクチャを削除する。](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. **[バックアップする項目の選択]** で、 **[次へ]** をクリックします。

    ![バックアップ インフラストラクチャを削除する。](./media/backup-azure-manage-mars/re-enable-next.png)
1. **[バックアップ スケジュールの選択]** でバックアップ スケジュールを指定し、 **[次へ]** をクリックします。
1. **[保有期間ポリシーの選択]** ページで、保有期間を指定し、 **[次へ]** をクリックします。
1. 最後に **[確認]** 画面で、ポリシーの詳細を確認し、 **[完了]** をクリックします。

## <a name="re-generate-passphrase"></a>パスフレーズを再生成する

パスフレーズは、MARS エージェントを使用して Azure との間でオンプレミスまたはローカル マシンをバックアップまたは復元するときに、データを暗号化および復号化するために使用されます。 パスフレーズを紛失した場合または忘れた場合は、次の手順に従ってパスフレーズを再生成できます (マシンが Recovery Services Vault にまだ登録され、バックアップが構成されている場合)。

- MARS エージェント コンソールで、 **[操作] ペイン** >  **[プロパティの変更]** に移動します。 次に、 **[暗号化] タブ**に移動します。<br>
- **[パスフレーズの変更]** チェックボックスをオンにします。<br>
- 新しいパスフレーズを入力するか、 **[パスフレーズの生成]** をクリックします。
- **[参照]** をクリックして、新しいパスフレーズを保存します。

    ![パスフレーズを生成します。](./media/backup-azure-manage-mars/passphrase.png)
- **[OK]** をクリックして変更を適用します。  Azure portal で Recovery Services コンテナーの [[Security Feature]\(セキュリティ機能\)](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#enable-security-features) が有効な場合、[セキュリティ PIN] の入力を求められます。 PIN を受信するには、こちらの[記事](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#authentication-to-perform-critical-operations)に記載されている手順に従います。<br>
- ポータルからセキュリティ PIN を貼り付け、 **[OK]** をクリックして変更を適用します。<br>

    ![パスフレーズを生成します。](./media/backup-azure-manage-mars/passphrase2.png)
- パスフレーズは、(ソース マシンではない) 別の場所、できれば Azure Key Vault に安全に保存するようにします。 複数のマシンが MARS エージェントでバックアップされている場合は、すべてのパスフレーズを追跡します。


## <a name="next-steps"></a>次のステップ

- サポートされるシナリオと制限事項については、[MARS エージェントのサポート マトリックス](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)に関するページを参照してください。
- [オンデマンド バックアップ ポリシーの保持動作](backup-configure-vault.md#on-demand-backup-policy-retention-behavior)の詳細を確認します。
