
<properties
	pageTitle="Resource Manager によってデプロイされた仮想マシン バックアップの管理と監視 | Microsoft Azure"
	description="Resource Manager によってデプロイされた仮想マシンのバックアップを管理したり監視したりする方法について説明します。"
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
	ms.date="06/03/2016"
	ms.author="jimpark; markgal;"/>

# Azure 仮想マシンのバックアップを管理および監視する

> [AZURE.SELECTOR]
- [Azure VM バックアップの管理](backup-azure-manage-vms.md)
- [クラシック VM バックアップの管理](backup-azure-manage-vms-classic.md)

この記事では、仮想マシン (VM) のバックアップを管理する方法のほか、ポータルのダッシュボードに表示されるバックアップの情報について説明します。この記事のガイダンスは、Recovery Services コンテナーと組み合わせて VM を使用するケースを想定しています。この記事で仮想マシンの作成や仮想マシンの保護については取り上げていません。Azure Resource Manager によって Azure にデプロイされた VM を Recovery Services コンテナーで保護する方法についての基本的な情報については、「[最初に: ARM VM の Recovery Services コンテナーへのバックアップ](backup-azure-vms-first-look-arm.md)」を参照してください。

## コンテナーと保護された仮想マシンへのアクセス

Azure ポータルから Recovery Services コンテナーのダッシュボードにアクセスすると、コンテナーに関して次のような情報を確認できます。

- 最新のバックアップ スナップショット (つまり最新の復元ポイント) <br>
- バックアップ ポリシー <br>
- すべてのバックアップ スナップショットの合計サイズ <br>
- コンテナーで保護されている仮想マシンの数 <br>

仮想マシンのバックアップに伴う管理タスクは多くの場合、ダッシュボードでコンテナーを開くことから始まります。ただし Recovery Services コンテナーを使った保護は、複数の項目 (または複数の仮想マシン) が対象となる場合もあるため、特定の仮想マシンについて詳しい情報を表示するためには、そのコンテナー項目のダッシュボードを開く必要があります。以降の手順では、*コンテナーのダッシュボード*と*コンテナー項目のダッシュボード*の開き方について順に説明しています。2 つの手順では、コンテナーとコンテナー項目を Azure ダッシュボードに追加する方法について、[ダッシュボードにピン留めする] コマンドを使用した "ヒント" を紹介しています。"ダッシュボードにピン留めする" は、コンテナーまたはコンテナー項目へのショートカットの作成手段となります。このショートカットから使用頻度の高いコマンドを実行することもできます。

>[AZURE.TIP] 複数のダッシュボードまたはブレードが開いている場合、ウィンドウの一番下にある濃い青色のスライダーを使用して、Azure ダッシュボード内のビューを左右にスライドさせることができます。

![Full view with slider](./media/backup-azure-manage-vms/bottom-slider.png)

### Recovery Services コンテナーをダッシュボードから開く

1. [Azure ポータル](https://portal.azure.com/)にサインインします。

2. ハブ メニューで **[参照]** をクリックし、リソースの一覧で「**Recovery Services**」と入力します。入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。**[Recovery Services コンテナー]** をクリックします。

    ![Create Recovery Services Vault step 1](./media/backup-azure-manage-vms/browse-to-rs-vaults.png) <br/>

    Recovery Services コンテナーの一覧が表示されます。

    ![List of Recovery Services vaults](./media/backup-azure-manage-vms/list-o-vaults.png) <br/>

    >[AZURE.TIP] Azure ダッシュボードにコンテナーをピン留めすると、Azure ポータルを開いてすぐにコンテナーにアクセスすることができます。ダッシュボードにコンテナーをピン留めするには、コンテナーの一覧で目的のコンテナーを右クリックし、**[ダッシュボードにピン留めする]** を選択します。

3. コンテナーの一覧から、いずれかのコンテナーを選択して、そのダッシュボードを開きます。コンテナーを選択すると、そのコンテナーのダッシュボードと **[設定]** ブレードが表示されます。次の画像では、**Contoso-vault** ダッシュボードが強調表示されています。

    ![Open vault dashboard and Settings blade](./media/backup-azure-manage-vms/full-view-rs-vault.png)

### コンテナー項目のダッシュボードを開く

前の手順では、コンテナーのダッシュボードを開きました。コンテナー項目のダッシュボードを開くには、次の手順に従います。

1. コンテナーのダッシュボードにある **[バックアップ項目]** タイルで、**[Azure Virtual Machines]** をクリックします。

    ![Open backup items tile](./media/backup-azure-manage-vms/contoso-vault.png)

    **[バックアップ項目]** ブレードに、項目ごとの最新のバックアップ ジョブが一覧表示されます。この例では、このコンテナーによって保護されている仮想マシンが 1 つ存在します (demovm-markgal)。

    ![Backup items tile](./media/backup-azure-manage-vms/backup-items-blade.png)

    >[AZURE.TIP] コンテナー項目は、アクセスしやすいように、Azure ダッシュボードにピン留めすることができます。コンテナー項目をピン留めするには、コンテナー項目の一覧で目的の項目を右クリックし、**[ダッシュボードにピン留めする]** を選択します。

2. **[バックアップ項目]** ブレードで目的の項目をクリックして、コンテナー項目のダッシュボードを開きます。

    ![Backup items tile](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

    コンテナー項目のダッシュボードとその **[設定]** ブレードが表示されます。

    ![Backup items dashboard with Settings blade](./media/backup-azure-manage-vms/item-dashboard-settings.png)

    コンテナー項目のダッシュボードから、多くの重要な管理作業を実行できます。その例を次に示します。

    - ポリシーの変更または新しいバックアップ ポリシーの作成<br>
	- 復元ポイントとその整合状態の確認 <br>
	- 仮想マシンのオンデマンド バックアップ <br>
	- 仮想マシンの保護の停止 <br>
	- 仮想マシンの保護の再開 <br>
	- バックアップ データ (または復旧ポイント) の削除 <br>
	- [バックアップ (または復旧ポイント) の復元](./backup-azure-arm-restore-vms.md#restore-a-recovery-point) <br>

次の手順は、コンテナー項目のダッシュボードを表示したところから始めます。

## ポリシーの変更または新しいバックアップ ポリシーの作成

1. [コンテナー項目のダッシュボード](backup-azure-manage-vms.md#open-a-vault-item-dashboard)で、**[すべての設定]** をクリックして **[設定]** ブレードを開きます。

    ![Backup policy blade](./media/backup-azure-manage-vms/all-settings-button.png)

2. **[設定]** ブレードの **[バックアップ ポリシー]** をクリックして対応するブレードを開きます。

    表示されたブレードに、バックアップの頻度とリテンション期間が詳しく表示されます。

    ![Backup policy blade](./media/backup-azure-manage-vms/backup-policy-blade.png)

3. **[バックアップ ポリシーの選択]** メニューから次の手順を実行します。
    - ポリシーを変更するには、別のポリシーを選択し、**[保存]** をクリックします。すぐに、新しいポリシーがコンテナーに適用されます。<br>
    - 新しいポリシーを作成するには、**[新規作成]** を選択します。

    ![仮想マシンのバックアップ](./media/backup-azure-manage-vms/backup-policy-create-new.png)

    バックアップ ポリシーを作成する手順については、「[バックアップ ポリシーの定義](backup-azure-manage-vms.md#defining-a-backup-policy)」を参照してください。


## オンデマンドでの仮想マシンのバックアップ
仮想マシンに保護が構成されていれば、その仮想マシンについてオンデマンド バックアップを作成できます。仮想マシンの初期バックアップがまだ実行されていない場合、オンデマンド バックアップを実行すると、仮想マシンの完全なコピーが Recovery Services コンテナーに作成されます。初期バックアップが完了している場合、オンデマンド バックアップによって Recovery Services コンテナーに送信されるのは、前回のスナップショット以降の変更のみとなります。つまり増分のみが送信対象となります。

>[AZURE.NOTE] オンデマンド バックアップのリテンション期間は、ポリシーの中で日単位のバックアップ ポイントに対して指定されたリテンション期間の値となります。日単位のバックアップ ポイントが選択されていない場合は、週単位のバックアップ ポイントが使用されます。

仮想マシンのオンデマンド バックアップをトリガーするには、次の手順に従います。

1. [コンテナー項目のダッシュボード](backup-azure-manage-vms.md#open-a-vault-item-dashboard)で **[今すぐバックアップ]** をクリックします。

    ![Backup now button](./media/backup-azure-manage-vms/backup-now-button.png)

    オンデマンド バックアップ ジョブを開始してよいかどうかを確認するメッセージが表示されます。バックアップ ジョブを開始する場合は **[はい]** をクリックします。

    ![Backup now button](./media/backup-azure-manage-vms/backup-now-check.png)

    バックアップ ジョブによって、新しい復旧ポイントが作成されます。作成される復旧ポイントのリテンション期間は、仮想マシンに関連付けられているポリシーで指定される期間と等しくなります。ジョブの進行状況を追跡するには、コンテナーのダッシュボードで **[バックアップ ジョブ]** タイルをクリックします。


## 仮想マシンの保護を停止する
仮想マシンの保護を中止するように選択した場合、復旧ポイントを保持するかどうかを確認するメッセージが表示されます。仮想マシンの保護を中止する方法は 2 つあります。将来のバックアップ ジョブをすべて停止したうえですべての復旧ポイントを削除する方法と、将来のバックアップ ジョブをすべて停止したうえで復旧ポイントを保持する方法です。復旧ポイントを記憶域に保持するためにはコストが発生します。一方、復旧ポイントを保持する利点は、仮想マシンを必要に応じて後から復元できることです。このような仮想マシンの詳しい価格については、[ここ](https://azure.microsoft.com/pricing/details/backup/)をクリックしてください。すべての復旧ポイントを削除するように選択した場合、仮想マシンを復元することはできません。

仮想マシンに対する保護を停止するには、次の手順に従います。

1. [コンテナー項目のダッシュボード](backup-azure-manage-vms.md#open-a-vault-item-dashboard)で **[バックアップを停止する]** をクリックします。

    ![Stop backup button](./media/backup-azure-manage-vms/stop-backup-button.png)

    [バックアップの停止] ブレードが開きます。

    ![Stop backup blade](./media/backup-azure-manage-vms/stop-backup-blade.png)

2. **[バックアップの停止]** ブレードで、バックアップ データを保持するか削除するかを選択します。選択内容についての詳しい説明が、情報ボックスに表示されます。

    ![保護の停止](./media/backup-azure-manage-vms/retain-or-delete-option.png)

3. バックアップ データを保持するように選択した場合は、手順 4. に進みます。バックアップ データを削除するように選択した場合は、バックアップ ジョブの停止と復旧ポイントの削除についての意思確認をします。対象となる項目の名前を入力してください。

    ![Stop verification](./media/backup-azure-manage-vms/item-verification-box.png)

    項目名がわからない場合は、感嘆符にマウス カーソルを合わせると名前が表示されます。また、**[バックアップの停止]** ブレードのタイトル下にも項目の名前が表示されます。

4. 必要に応じて、**[理由]** または **[コメント]** を入力します。

5. 現在の項目のバックアップ ジョブを停止するには、![Stop backup button](./media/backup-azure-manage-vms/stop-backup-button-blue.png) をクリックします。

    バックアップ ジョブが停止したことを示す通知メッセージが表示されます。

    ![保護の停止の確認](./media/backup-azure-manage-vms/stop-message.png)


## 仮想マシンの保護の再開
仮想マシンの保護を停止するときに **[バックアップ データの保持]** オプションを選択した場合、保護を再開することができます。**[バックアップ データを削除]** オプションを選択した場合は、仮想マシンの保護を再開することはできません。

仮想マシンの保護を再開するには

1. [コンテナー項目のダッシュボード](backup-azure-manage-vms.md#open-a-vault-item-dashboard)で **[バックアップの再開]** をクリックします。

    ![Resume protection](./media/backup-azure-manage-vms/resume-backup-button.png)

    [バックアップ ポリシー] ブレードが開きます。

    >[AZURE.NOTE] 仮想マシンを再保護する場合は、最初に仮想マシンを保護するために使用いたポリシー以外のポリシーを選択できます。

2. 「[ポリシーの変更または新しいバックアップ ポリシーの作成](backup-azure-manage-vms.md#change-policies-or-create-a-new-backup-policy)」の手順に従って、仮想マシンのポリシーを割り当てます。

    仮想マシンにバックアップ ポリシーが適用されると、次のメッセージが表示されます。

    ![Successfully protected VM](./media/backup-azure-manage-vms/success-message.png)

## バックアップ データの削除
仮想マシンに関連付けられているバックアップ データは、**バックアップの停止**ジョブの実行中やバックアップを停止した後にいつでも削除することができます。仮想マシンのバックアップ ジョブを停止して数日または数週間待ったうえで復旧ポイントを削除するかどうかを決められる方が都合のよい場合は、そのようにすることもできます。復旧ポイントの復元とは異なり、バックアップ データを削除するときは、特定の復旧ポイントを削除対象として選択することができません。バックアップ データを削除するように選択した場合、その項目に関連付けられているすべての復旧ポイントが削除されます。

以降の手順は、仮想マシンのバックアップ ジョブが停止済みであるか、無効にされていることを前提としています。**[バックアップの再開]** と **[Delete backup (バックアップの削除)]** のオプションは、バックアップ ジョブが無効にされた後でのみ、コンテナー項目のダッシュボードに表示されます。

![Resume and delete buttons](./media/backup-azure-manage-vms/resume-delete-buttons.png)

*バックアップを無効*にした状態の仮想マシンでバックアップ データを削除するには、次の手順に従います。

1. [コンテナー項目のダッシュボード](backup-azure-manage-vms.md#open-a-vault-item-dashboard)で **[Delete backup (バックアップの削除)]** をクリックします。

    ![VM の種類](./media/backup-azure-manage-vms/delete-backup-buttom.png)

    **[バックアップ データを削除]** ブレードが開きます。

    ![VM の種類](./media/backup-azure-manage-vms/delete-backup-blade.png)

2. 復旧ポイントを削除してよいことを確認するために、バックアップ項目の名前を入力する必要があります。

    ![Stop verification](./media/backup-azure-manage-vms/item-verification-box.png)

    項目名がわからない場合は、感嘆符にマウス カーソルを合わせると名前が表示されます。また、**[バックアップ データを削除]** ブレードのタイトル下にも項目の名前が表示されます。

3. 必要に応じて、**[理由]** または **[コメント]** を入力します。

4. 現在の項目のバックアップ データを削除するには、![Stop backup button](./media/backup-azure-manage-vms/delete-button.png) をクリックします。

    バックアップ データが削除されたことを示す通知メッセージが表示されます。

## 監査操作
Recovery Services コンテナーで実行された管理操作は、操作ログとイベント ログで確認できます。操作ログは、バックアップ操作の事後分析や監査を行うのに役立ちます。*サブスクリプション内*のすべての操作の記録は、監査ログの機能を使用して確認できます。イベント ログ、操作ログ、監査ログの詳細については、「[イベントと監査ログの表示](../azure-portal/insights-debugging-with-events.md)」の記事を参照してください。**[監査ログ]** の設定は、Recovery Services コンテナー (またはコンテナー項目) に固有のイベント ログや操作ログを確認するときに使用します。

監査ログには次の操作が記録されます。

- 登録
- 登録解除
- 保護の構成
- バックアップ (オンデマンド バックアップとスケジュールされたバックアップの両方)
- 復元
- 保護の停止
- バックアップ データの削除
- ポリシーの追加
- ポリシーの削除
- ポリシーの更新
- ジョブの取り消し

Recovery Services コンテナーのイベント ログを表示するには、次の手順に従います。

1. [コンテナーのダッシュボード](backup-azure-manage-vms.md#open-a-recovery-services-vault-in-the-dashboard)で **[監査ログ]** に移動してクリックし、**[イベント]** ブレードを開きます。

    ![Audit Logs](./media/backup-azure-manage-vms/audit-logs.png)

    [イベント] ブレードに、現在のコンテナーに該当する操作イベントのみが表示されます。このブレードには、過去 1 週間に発生した重大イベント、エラー イベント、警告イベント、情報イベントが一覧表示されます。この期間は、**[フィルター]** の設定で指定された既定値です。**[イベント]** ブレードには、イベントがいつ発生したかを追跡する棒グラフも表示されます。棒グラフを非表示にするには、**[イベント]** メニューの **[グラフの表示]** をクリックしてグラフをオフに切り替えます。

    ![Audit Logs Filter](./media/backup-azure-manage-vms/audit-logs-filter.png)

2. 特定の操作についての詳しい情報が必要な場合は、操作の一覧で目的の操作をクリックし、対応するブレードを開きます。このブレードには、操作に関する詳細な情報のほか、特定の期間に発生したイベントの一覧が表示されます。

    ![操作の詳細](./media/backup-azure-manage-vms/audit-logs-details-window.png)

3. 特定のイベントに関する詳しい情報を表示するには、イベントの一覧から目的の操作をクリックして、その [詳細] ブレードを開きます。

    ![Event Details](./media/backup-azure-manage-vms/audit-logs-details-window-deep.png)

    イベント レベルの情報は、得られた内容がそのままの詳細度で表示されます。この手順の残りの部分では、入手できる情報を編集または変更する方法について説明します。

4. 利用可能なフィルターの一覧を編集するには、**[イベント]** メニューの **[フィルター]** をクリックして対応するブレードを開きます。

    ![open filter blade](./media/backup-azure-manage-vms/audi-logs-filter-button.png)

5. **[フィルター]** ブレードで、**[レベル]**、**[期間]**、**[呼び出し元]** の各フィルターを調整します。その他のフィルターは、現在の Recovery Services コンテナーの情報を提供するように設定されているため利用できません。

    ![Audit Logs-query details](./media/backup-azure-manage-vms/filter-blade.png)

    イベントの**レベル**には、重大、エラー、警告、情報のいずれかを指定できます。イベントのレベルは複数選んで組み合わせることができますが、少なくとも 1 つは選択する必要があります。目的のレベルのオン/オフを指定してください。イベントの収集対象期間は、**[期間]** フィルターで指定できます。カスタムの期間を使用する場合は、開始と終了のタイミングを設定できます。

6. フィルターを使って操作ログを照会する準備が整ったら、**[更新]** をクリックします。その結果が **[イベント]** ブレードに表示されます。

    ![操作の詳細](./media/backup-azure-manage-vms/edited-list-of-events.png)


## アラート通知
ポータルで、ジョブのカスタム アラート通知を受け取ることができます。ジョブの通知を受け取るには、操作ログ イベントに対して PowerShell ベースのアラート ルールを定義します。*PowerShell Version 1.3.0 以降*を使用してください。

バックアップに失敗した場合に警告するカスタム通知を定義するには、次のようなコマンドを使用します。

```
PS C:\> $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail contoso@microsoft.com
PS C:\> Add-AzureRmLogAlertRule -Name backupFailedAlert -Location "East US" -ResourceGroup RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US -OperationName Microsoft.Backup/RecoveryServicesVault/Backup -Status Failed -TargetResourceId /subscriptions/86eeac34-eth9a-4de3-84db-7a27d121967e/resourceGroups/RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US/providers/microsoft.backupbvtd2/RecoveryServicesVault/trinadhVault -Actions $actionEmail
```

**ResourceId**: この情報は監査ログで確認できます。ResourceId は操作ログの Resource 列に記録されています。

**OperationName**: 形式は "Microsoft.RecoveryServices/recoveryServicesVault/<EventName>" となります。ここで、EventName は、Register、Unregister、ConfigureProtection、Backup、Restore、StopProtection、DeleteBackupData、CreateProtectionPolicy、DeleteProtectionPolicy、UpdateProtectionPolicy のいずれかとなります。

**Status**: サポートされる値は、Started、Succeeded、Failed です。

**ResourceGroup**: 対象リソースが属しているリソース グループです。生成するログに Resource Group 列を追加することができます。リソース グループは、入手できるイベント情報の種類の 1 つです。

**Name**: アラート ルールの名前を指定します。

**CustomEmail**: アラート通知を送信するカスタム電子メール アドレスを指定します。

**SendToServiceOwners**: このオプションを指定すると、サブスクリプションの管理者と共同管理者全員にアラート通知を送信します。これは、**New-AzureRmAlertRuleEmail** コマンドレットで使用できます。

### アラートに関する制限事項
イベント ベースのアラートには、次の制限事項が適用されます。

1. アラートは、Recovery Services コンテナー内のすべての仮想マシン上でトリガーされます。Recovery Services コンテナーから特定の仮想マシンを選んでアラートをカスタマイズすることはできません。
2. この機能はプレビュー段階にあります。[詳細情報](../azure-portal/insights-powershell-samples.md#create-alert-rules)
3. アラートは、"alerts-noreply@mail.windowsazure.com" から送信されます。現時点で、電子メールの送信者を変更することはできません。

[AZURE.INCLUDE [backup-create-backup-policy-for-vm](../../includes/backup-create-backup-policy-for-vm.md)]

## 次のステップ

復旧ポイントからの仮想マシンの再作成については、[仮想マシンの復元](backup-azure-restore-vms.md)に関するページを参照してください。仮想マシンの保護については、「[最初に: ARM VM の Recovery Services コンテナーへのバックアップ](backup-azure-vms-first-look-arm.md)」を参照してください。

<!---HONumber=AcomDC_0608_2016-->