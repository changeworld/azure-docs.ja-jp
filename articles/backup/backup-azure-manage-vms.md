
<properties
	pageTitle="Azure Backup -仮想マシンの管理"
	description="Azure 仮想マシンを管理する方法について説明します。"
	services="backup"
	documentationCenter=""
	authors="jimpark"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/28/2015"
	ms.author="jimpark"/>

# 仮想マシンの管理


## 保護された仮想マシンを管理する

1. 仮想マシンのバックアップ設定を表示して管理するには、**[保護された項目]** タブをクリックします。

  - 保護された項目の名前をクリックすると、**[バックアップの詳細]** タブに前回のバックアップに関する情報が表示されます。

        ![Virtual machine backup](./media/backup-azure-manage-vms/backup-vmdetails.png)

2. 仮想マシンのバックアップ ポリシー設定を表示して管理するには、**[ポリシー]** タブをクリックします。

  - **[バックアップ ポリシー]** タブに既存のポリシーが表示されます。必要に応じて変更できます。新しいポリシーを作成する必要がある場合は、**[ポリシー]** ページの **[作成]** をクリックします。ポリシーを削除する場合は、そのポリシーにどの仮想マシンも関連付けられていないことをご確認ください。

        ![Virtual machine policy](./media/backup-azure-manage-vms/backup-vmpolicy.png)

3. 仮想マシンの動作や状態に関する詳細情報は、**[ジョブ]** ページから取得できます。一覧内のジョブをクリックして詳細情報を表示するか、特定の仮想マシンのジョブをフィルターします。

    ![ジョブ](./media/backup-azure-manage-vms/backup-job.png)

## オンデマンドでの仮想マシンのバックアップ
保護のためのバックアップを構成した後で、仮想マシンのオンデマンド バックアップを作成できます。仮想マシンの最初のバックアップが保留中の場合、オンデマンド バックアップを実行すると、仮想マシンの完全なコピーが Azure Backup 資格情報コンテナーに作成されます。最初のバックアップが完了している場合、オンデマンド バックアップを実行すると、前のバックアップから変更された部分のみが Azure Backup 資格情報コンテナーに送られます。

仮想マシンのオンデマンド バックアップを作成するには:

1. **[保護された項目]** ページを開き、**[種類]** として **[Azure 仮想マシン]** を選択し (まだ選択していない場合)、**[選択]** ボタンをクリックします。

    ![VM Type](./media/backup-azure-manage-vms/vm-type.png)

2. オンデマンド バックアップを作成する仮想マシンを選択して、ページの下部にある **[今すぐバックアップ]** ボタンをクリックします。

    ![Back up now](./media/backup-azure-manage-vms/backup-now.png)

    これにより、選択した仮想マシンにバックアップ ジョブが作成されます。このジョブによって作成される回復ポイントの保有期間は、仮想マシンに関連付けられているポリシーで指定される期間と同じになります。

    ![Creating backup job](./media/backup-azure-manage-vms/creating-job.png)

    >[AZURE.NOTE]仮想マシンに関連付けられているポリシーを表示するには、**[保護された項目]** ページで仮想マシンをドリルダウンし、[バックアップ ポリシー] タブを開きます。

3. ジョブが作成された後で、トースト バーの **[ジョブの表示]** ボタンをクリックすると、対応するジョブがジョブのページに表示されます。

    ![Backup job created](./media/backup-azure-manage-vms/created-job.png)

4. ジョブが正常に終了した後、仮想マシンの復元に使用できる回復ポイントが作成されます。また、これによって **[保護された項目]** ページの [回復ポイント] 列の値が 1 ずつ加算されます。

## 仮想マシンの保護を停止する
次の方法を使って、仮想マシンの以後のバックアップを停止することができます。

- 仮想マシンに関連付けられているバックアップ データを Azure Backup 資格情報コンテナー内に保持する
- 仮想マシンに関連付けられているバックアップ データを削除する

最初の方法を選択した場合、バックアップ データを使用して仮想マシンを復元できます。このような仮想マシンの詳しい料金については、[ここ](http://azure.microsoft.com/pricing/details/backup/)をクリックしてください。

仮想マシンに対する保護を停止するには:

1. **[保護された項目]** ページを開き、フィルターの種類として **[Azure 仮想マシン]** を選択し (まだ選択していない場合)、**[選択]** ボタンをクリックします。

    ![VM Type](./media/backup-azure-manage-vms/vm-type.png)

2. 仮想マシンを選択して、ページの下部にある **[保護を停止]** をクリックします。

    ![Stop protection](./media/backup-azure-manage-vms/stop-protection.png)

3. Azure Backup の既定では、仮想マシンに関連付けられているバックアップ データは削除されません。

    ![Confirm stop protection](./media/backup-azure-manage-vms/confirm-stop-protection.png)

    バックアップ データを削除するには、このチェック ボックスをオンにします。

    ![Checkbox](./media/backup-azure-manage-vms/checkbox.png)

    バックアップを停止する理由を選択してください。これは省略可能ですが、理由を示すことで、Azure Backup の開発チームがフィードバックを調査し、顧客シナリオに優先順位を付けるために役立ちます。

4. **[送信]** ボタンをクリックすると、**保護を停止**ジョブが送信されます。**[ジョブの表示]** をクリックすると、対応するジョブが **[ジョブ]** ページに表示されます。

    ![Stop protection](./media/backup-azure-manage-vms/stop-protect-success.png)

    **保護を停止**ウィザードで **[関連付けられたバックアップ データを削除する]** オプションをオフにした場合、ジョブの完了後に保護の状態が **[保護停止]** に変わります。データは、明示的に削除されるまで Azure Backup に残ります。**[保護された項目]** ページで仮想マシンを選択して **[削除]** をクリックすると、データをいつでも削除できます。

    ![Stopped protection](./media/backup-azure-manage-vms/protection-stopped-status.png)

    **[関連付けられたバックアップ データを削除する]** オプションをオンにした場合、仮想マシンが **[保護された項目]** ページに表示されなくなります。

## 仮想マシンの再保護
**[保護を停止]** で **[関連付けられたバックアップ データを削除する]** をオフにしていた場合、登録済みの仮想マシンのバックアップと同様の手順に従って、仮想マシンを再び保護することができます。再保護された仮想マシンは、保護の停止前に保持されていたバックアップ データと、再保護された後に作成された回復ポイントを持つことになります。

再保護の後、**[保護を停止]** より前の回復ポイントがある場合は、仮想マシンの保護の状態が **[保護済み]** に変わります。

  ![Reprotected VM](./media/backup-azure-manage-vms/reprotected-status.png)

>[AZURE.NOTE]仮想マシンを再保護する場合は、最初に仮想マシンを保護するために使用いたポリシー以外のポリシーを選択できます。

## 仮想マシンを登録解除する

バックアップ資格情報コンテナーから仮想マシンを削除する場合

1. ページの下部にある **[登録解除]** をクリックします。

    ![保護を無効にする](./media/backup-azure-manage-vms/unregister-button.png)

    確認を求める画面の下部に、トースト通知が表示されます。**[はい]** をクリックして続行します。

    ![保護を無効にする](./media/backup-azure-manage-vms/confirm-unregister.png)

## バックアップ データの削除
仮想マシンに関連付けられているバックアップ データを削除できるのは、次のタイミングです。

- "保護を停止" ジョブ中
- 仮想マシンで "保護を停止" ジョブが完了した後

**[バックアップの停止]** ジョブが正常に完了した後で、"保護停止" 状態になっている仮想マシンのバックアップ データを削除するには:

1. **[保護された項目]** ページを開き、種類として **[Azure 仮想マシン]** を選択し、**[選択]** ボタンをクリックします。

    ![VM Type](./media/backup-azure-manage-vms/vm-type.png)

2. 仮想マシンを選択します。仮想マシンが **[保護停止]** 状態になります。

    ![Protection stopped](./media/backup-azure-manage-vms/protection-stopped-b.png)

3. ページの下部にある **[削除]** をクリックします。

    ![Delete backup](./media/backup-azure-manage-vms/delete-backup.png)

4. **バックアップ データを削除**ウィザードで、バックアップ データを削除する理由を選択して (強く推奨)、**[送信]** をクリックします。

    ![Delete backup data](./media/backup-azure-manage-vms/delete-backup-data.png)

5. これで、選択した仮想マシンのバックアップ データを削除するジョブが作成されます。**[ジョブの表示]** をクリックすると、対応するジョブが [ジョブ] ページに表示されます。

    ![Data deletion successful](./media/backup-azure-manage-vms/delete-data-success.png)

    ジョブが完了すると、仮想マシンに対応するエントリが **[保護された項目]** ページから削除されます。


###ダッシュボード

**ダッシュボード** ページでは、Azure 仮想マシン、ストレージ、過去 24 時間以内に関連付けられたジョブに関する情報を確認できます。バックアップの状態と関連付けられているバックアップ エラーを表示できます。

  ![Dashboard](./media/backup-azure-manage-vms/dashboard-protectedvms.png)
 

<!---HONumber=58_postMigration-->