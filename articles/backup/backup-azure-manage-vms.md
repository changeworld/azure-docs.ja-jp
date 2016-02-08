
<properties
	pageTitle="Azure 仮想マシンのバックアップを管理および監視する | Microsoft Azure"
	description="Azure 仮想マシンのバックアップを管理および監視する方法を説明する"
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/25/2016"
	ms.author="trinadhk; jimpark; markgal;"/>

# Azure 仮想マシンのバックアップを管理および監視する

## 保護された仮想マシンを管理する

以下では、保護された仮想マシンを管理する方法を説明します。

1. 仮想マシンのバックアップ設定を表示して管理するには、**[保護された項目]** タブをクリックします。

2. 保護された項目の名前をクリックすると、**[バックアップの詳細]** タブに前回のバックアップに関する情報が表示されます。

    ![仮想マシンのバックアップ](./media/backup-azure-manage-vms/backup-vmdetails.png)

3. 仮想マシンのバックアップ ポリシー設定を表示して管理するには、**[ポリシー]** タブをクリックします。

    ![仮想マシンのポリシー](./media/backup-azure-manage-vms/manage-policy-settings.png)

    **[バックアップ ポリシー]** タブに既存のポリシーが表示されます。必要に応じて変更できます。新しいポリシーを作成する必要がある場合は、**[ポリシー]** ページの **[作成]** をクリックします。ポリシーを削除する場合は、そのポリシーにどの仮想マシンも関連付けられていないことをご確認ください。

    ![仮想マシンのポリシー](./media/backup-azure-manage-vms/backup-vmpolicy.png)

4. 仮想マシンの動作や状態に関する詳細情報は、**[ジョブ]** ページから取得できます。一覧内のジョブをクリックして詳細情報を表示するか、特定の仮想マシンのジョブをフィルターします。

    ![ジョブ](./media/backup-azure-manage-vms/backup-job.png)

## オンデマンドでの仮想マシンのバックアップ
仮想マシンに保護が構成されていれば、その仮想マシンについてオンデマンド バックアップを作成できます。仮想マシンの最初のバックアップが保留中の場合、オンデマンド バックアップを実行すると、仮想マシンの完全なコピーが Azure のバックアップ コンテナーに作成されます。最初のバックアップが完了している場合、オンデマンド バックアップを実行すると、前のバックアップから変更された部分のみが Azure のバックアップ コンテナーに送られます。

仮想マシンのオンデマンド バックアップを作成するには:

1. **[保護された項目]** ページを開き、**[タイプ]** として **[Azure 仮想マシン]** を選択し (まだ選択していない場合)、**[選択]** ボタンをクリックします。

    ![VM の種類](./media/backup-azure-manage-vms/vm-type.png)

2. オンデマンド バックアップを作成する仮想マシンを選択して、ページの下部にある **[今すぐバックアップ]** ボタンをクリックします。

    ![今すぐバックアップ](./media/backup-azure-manage-vms/backup-now.png)

    これにより、選択した仮想マシンにバックアップ ジョブが作成されます。このジョブによって作成される回復ポイントの保有期間は、仮想マシンに関連付けられているポリシーで指定される期間と同じになります。

    ![バックアップ ジョブの作成](./media/backup-azure-manage-vms/creating-job.png)

    >[AZURE.NOTE] 仮想マシンに関連付けられているポリシーを表示するには、**[保護された項目]** ページで仮想マシンをドリルダウンし、[バックアップ ポリシー] タブを開きます。

3. ジョブが作成された後で、トースト バーの **[ジョブの表示]** ボタンをクリックすると、対応するジョブがジョブのページに表示されます。

    ![バックアップ ジョブが作成されました](./media/backup-azure-manage-vms/created-job.png)

4. ジョブが正常に終了した後、仮想マシンの復元に使用できる回復ポイントが作成されます。また、これによって **[保護された項目]** ページの [回復ポイント] 列の値が 1 ずつ加算されます。

## 仮想マシンの保護を停止する
次の方法を使って、仮想マシンの以後のバックアップを停止することができます。

- 仮想マシンに関連付けられているバックアップ データを Azure Backup コンテナー内に保持する
- 仮想マシンに関連付けられているバックアップ データを削除する

仮想マシンに関連付けられているバックアップ データを保持する方法を選択した場合、バックアップ データを使用して仮想マシンを復元できます。このような仮想マシンの詳しい価格については、[ここ](https://azure.microsoft.com/pricing/details/backup/)をクリックしてください。

仮想マシンに対する保護を停止するには:

1. **[保護された項目]** ページを開き、フィルターのタイプとして **[Azure 仮想マシン]** を選択し (まだ選択していない場合)、**[選択]** ボタンをクリックします。

    ![VM の種類](./media/backup-azure-manage-vms/vm-type.png)

2. 仮想マシンを選択して、ページの下部にある **[保護を停止]** をクリックします。

    ![保護の停止](./media/backup-azure-manage-vms/stop-protection.png)

3. 既定では、Azure Backup では、仮想マシンに関連付けられているバックアップ データは削除されません。

    ![保護の停止の確認](./media/backup-azure-manage-vms/confirm-stop-protection.png)

    バックアップ データを削除するには、このチェック ボックスをオンにします。

    ![チェックボックス](./media/backup-azure-manage-vms/checkbox.png)

    バックアップを停止する理由を選択してください。これは省略可能ですが、理由を示すことで、Azure Backup の開発チームがフィードバックを調査し、顧客シナリオに優先順位を付けるために役立ちます。

4. **[送信]** ボタンをクリックすると、**保護を停止**ジョブが送信されます。**[ジョブの表示]** をクリックすると、対応するジョブが **[ジョブ]** ページに表示されます。

    ![保護の停止](./media/backup-azure-manage-vms/stop-protect-success.png)

    **保護を停止**ウィザードで **[関連付けられたバックアップ データを削除する]** オプションをオフにした場合、ジョブの完了後に保護の状態が **[保護停止]** に変わります。データは、明示的に削除されるまで Azure Backup に残ります。**[保護された項目]** ページで仮想マシンを選択して **[削除]** をクリックすると、データをいつでも削除できます。

    ![停止された保護](./media/backup-azure-manage-vms/protection-stopped-status.png)

    **[関連付けられたバックアップ データを削除します]** オプションをオンにした場合、仮想マシンが **[保護された項目]** ページに表示されなくなります。

## 仮想マシンの再保護
**[保護を停止]** で **[関連付けられたバックアップ データを削除する]** をオフにしていた場合、登録済みの仮想マシンのバックアップと同様の手順に従って、仮想マシンを再び保護することができます。再保護された仮想マシンは、保護の停止前に保持されていたバックアップ データと、再保護された後に作成された回復ポイントを持つことになります。

再保護の後、**[保護を停止]** より前の回復ポイントがある場合は、仮想マシンの保護の状態が **[保護済み]** に変わります。

  ![再保護された VM](./media/backup-azure-manage-vms/reprotected-status.png)

>[AZURE.NOTE] 仮想マシンを再保護する場合は、最初に仮想マシンを保護するために使用いたポリシー以外のポリシーを選択できます。

## 仮想マシンを登録解除する

バックアップ コンテナーから仮想マシンを削除する場合

1. ページの下部にある **[登録解除]** をクリックします。

    ![保護を無効にする](./media/backup-azure-manage-vms/unregister-button.png)

    確認を求める画面の下部に、トースト通知が表示されます。**[はい]** をクリックして続行します。

    ![保護を無効にする](./media/backup-azure-manage-vms/confirm-unregister.png)

## バックアップ データの削除
仮想マシンに関連付けられているバックアップ データを削除できるのは、次のタイミングです。

- "保護を停止" ジョブ中
- 仮想マシンで "保護を停止" ジョブが完了した後

**[バックアップの停止]** ジョブが正常に完了した後で、*[保護停止]* 状態になっている仮想マシンのバックアップ データを削除するには、次の操作を行います。

1. **[保護された項目]** ページを開き、*[タイプ]* として **[Azure 仮想マシン]** を選択し、**[選択]** ボタンをクリックします。

    ![VM の種類](./media/backup-azure-manage-vms/vm-type.png)

2. 仮想マシンを選択します。仮想マシンが **[保護停止]** 状態になります。

    ![保護の停止](./media/backup-azure-manage-vms/protection-stopped-b.png)

3. ページの下部にある **[削除]** をクリックします。

    ![バックアップの削除](./media/backup-azure-manage-vms/delete-backup.png)

4. **バックアップ データを削除**ウィザードで、バックアップ データを削除する理由を選択して (強く推奨)、**[送信]** をクリックします。

    ![バックアップ データの削除](./media/backup-azure-manage-vms/delete-backup-data.png)

5. これで、選択した仮想マシンのバックアップ データを削除するジョブが作成されます。**[ジョブの表示]** をクリックすると、対応するジョブが [ジョブ] ページに表示されます。

    ![データ削除の成功](./media/backup-azure-manage-vms/delete-data-success.png)

    ジョブが完了すると、仮想マシンに対応するエントリが **[保護された項目]** ページから削除されます。

## ダッシュボード
**ダッシュ ボード** ページでは、Azure Virtual Machines、ストレージ、過去 24 時間以内に関連付けられたジョブに関する情報を確認できます。バックアップの状態と関連付けられているバックアップ エラーを表示できます。

![ダッシュボード](./media/backup-azure-manage-vms/dashboard-protectedvms.png)

>[AZURE.NOTE] ダッシュボード内の値は、24 時間に 1 度更新されます。

## 監査操作
Azure Backup では、顧客がトリガーしたバックアップ操作の "操作ログ" を確認できます。この機能を使えば、バックアップ コンテナーで行われた管理操作を正確に把握することができます。操作ログは、バックアップ操作の事後分析や監査を行うのに役立ちます。

操作ログには、次の操作が記録されます。

- 登録
- 登録解除
- 保護の構成
- バックアップ ([今すぐバックアップ] で行うオンデマンド バックアップとスケジュールされたバックアップの両方)
- 復元
- 保護の停止
- バックアップ データの削除
- ポリシーの追加
- ポリシーの削除
- ポリシーの更新
- ジョブの取り消し

バックアップ コンテナーに対応する操作ログを表示するには、次の操作を行います。

1. Azure ポータルの **[管理サービス]** に移動し、**[操作ログ]** タブをクリックします。

    ![操作ログ](./media/backup-azure-manage-vms/ops-logs.png)

2. フィルターで *[タイプ]* として **[Backup]** を選択し、*[サービス名]* にバックアップ コンテナー名を指定して、**[送信]** をクリックします。

    ![操作ログ フィルター](./media/backup-azure-manage-vms/ops-logs-filter.png)

3. 操作ログで、任意の操作を選択し、**[詳細]** をクリックすると、選択した操作に対応する詳細が表示されます。

    ![操作ログ取得の詳細](./media/backup-azure-manage-vms/ops-logs-details.png)

    **詳細ウィザード**には、トリガーされた操作、ジョブ ID、この操作がトリガーされたリソース、および操作開始時間に関する情報が表示されます。

    ![操作の詳細](./media/backup-azure-manage-vms/ops-logs-details-window.png)

## アラート通知
ポータルで、ジョブのカスタム アラート通知を取得できます。この通知は、操作ログのイベントに PowerShell ベースのアラート ルールを定義することによって取得できます。

イベント ベースのアラートは、Azure リソース モードで動作します。Azure リソース モードに切り替えるには、管理者特権のコマンド モードで次のコマンドレットを実行します。

```
PS C:\> Switch-AzureMode AzureResourceManager
```

バックアップが失敗した場合にアラートを発行するカスタム通知を定義する場合、コマンドのサンプルは次のようになります。

```
PS C:\> Add-AlertRule -Operator GreaterThanOrEqual -Threshold 1 -ResourceId '/subscriptions/86eeac34-eth9a-4de3-84db-7a27d121967e/resourceGroups/RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US/providers/microsoft.backupbvtd2/BackupVault/trinadhVault' -EventName Backup  -EventSource Administrative -Level Error -OperationName 'Microsoft.Backup/backupVault/Backup' -ResourceProvider Microsoft.Backup -Status Failed  -SubStatus Failed -RuleType Event -Location eastus -ResourceGroup RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US -Name Backup-Failed -Description 'Backup failed for one of the VMs in vault trinadhkVault' -CustomEmails 'contoso@microsoft.com' -SendToServiceOwners
```

**ResourceId**: 前のセクションで説明した操作ログのポップアップから取得できます。具体的には、操作の詳細を示したポップアップ ウィンドウに表示される ResourceUri が、このコマンドレットに指定する ResourceId となります。

**EventName**: IaaS VM バックアップに関するアラートの場合、サポートされる値は、Register、Unregister、ConfigureProtection、Backup、Restore、StopProtection、DeleteBackupData、CreateProtectionPolicy、DeleteProtectionPolicy、UpdateProtectionPolicy です。

**Level**: サポートされる値は、Informational、Error です。操作が失敗した場合のアラートには Error を使用し、ジョブが成功した場合のアラートには Informational を使用します。

**OperationName**: これは、"Microsoft.Backup/backupvault/<EventName>" の形式で指定します。EventName には上で説明した値を指定します。

**Status**: サポートされる値は、Started、Succeeded、および Failed です。Status に Succeeded を指定する場合、Level には Informational を指定することをお勧めします。

**SubStatus**: バックアップ操作の Status と同じです。

**RuleType**: イベント ベースのバックアップ アラートであるため、*Event* のままにします。

**ResourceGroup**: 操作がトリガーされるリソースの ResourceGroup です。これは、ResourceId の値から取得できます。ResourceId 値の */resourceGroups/* と */providers/* フィールドの間にある値が ResourceGroup の値です。

**Name**: アラート ルールの名前を指定します。

**Description**: アラート ルールの説明を指定します。

**CustomEmails**: アラート通知を送信するカスタム電子メール アドレスを指定します。

**SendToServiceOwners**: このオプションを指定すると、サブスクリプションの管理者と共同管理者全員にアラート通知を送信します。

アラートの電子メール サンプルは、次のようになります。

ヘッダーのサンプル:

![アラート ヘッダー](./media/backup-azure-manage-vms/alert-header.png)

アラートの電子メールの本文のサンプル:

![アラートの本文](./media/backup-azure-manage-vms/alert-body.png)

### アラートに関する制限事項
イベント ベースのアラートには、次の制限事項が適用されます。

1. アラートは、バックアップ コンテナー内のすべての仮想マシン上でトリガーされます。バックアップ コンテナー内の特定の仮想マシンのセットのアラートを取得するようにカスタマイズすることはできません。
2. アラートは、次のアラート期間にアラートの条件に一致するイベントがトリガーされなかった場合、自動解決されます。アラートのトリガー期間を設定するには、Add-AlertRule コマンドレットで、*WindowSize* パラメーターを使用します。

## 次のステップ

- [Azure VM の復元](backup-azure-restore-vms.md)

<!---HONumber=AcomDC_0128_2016-->