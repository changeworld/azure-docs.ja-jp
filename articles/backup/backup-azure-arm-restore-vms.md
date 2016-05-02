
<properties
	pageTitle="Azure ポータルを使用してバックアップから仮想マシンを復元する | Microsoft Azure"
	description="Azure ポータルを使用して復旧ポイントから Azure 仮想マシンを復元する"
	services="backup"
	documentationCenter=""
	authors="markgalioto"
	manager="jwhit"
	editor=""
	keywords="バックアップの復元, 復元する方法, 回復ポイント"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/19/2016"
	ms.author="trinadhk; jimpark;"/>


# Azure ポータルを使用した仮想マシンの復元

> [AZURE.SELECTOR]
- [クラシック ポータルでの VM の復元](backup-azure-restore-vms.md)
- [Azure ポータルでの VM の復元](backup-azure-arm-restore-vms.md)


Backup サービスを使用してデータを保護するには、定義された間隔でデータのスナップショットを取得します。これらのスナップショットは復旧ポイントと呼ばれ、Recovery Services コンテナーに格納されます。VM を修復または再構築する必要がある場合は、保存されている復旧ポイントのいずれかから VM を復元することができます。復旧ポイントを復元すると、VM は、復旧ポイントが作成された時点の状態に戻ります。この記事では、VM を復元する方法について説明します。

> [AZURE.NOTE] Azure には、リソースの作成と操作に関して 2 種類のデプロイ モデルがあります。[Resource Manager デプロイ モデルとクラシック デプロイ モデル](../resource-manager-deployment-model.md)です。この記事では、Resource Manager モデルを使用してデプロイされた VM を復元するための情報および手順を示しています。



## 復旧ポイントを復元する

1. [Azure ポータル](http://ms.portal.azure.com/)にサインインします。

2. Azure メニューで **[参照]** をクリックし、サービスの一覧で「**Recovery Services**」と入力します。入力した文字列に合わせて、サービスの一覧の内容が変更されます。**[Recovery Services コンテナー]** が表示されたら、それを選択します。

    ![Open Recovery Services vault](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    サブスクリプションに含まれるコンテナーの一覧が表示されます。

    ![List of Recovery Services vaults](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)

3. 一覧から、復元する VM に関連付けられているコンテナーを選択します。コンテナーをクリックすると、ダッシュボードが開きます。

    ![List of Recovery Services vaults](./media/backup-azure-arm-restore-vms/select-vault-open-vault-blade.png)

4. コンテナーのダッシュボードが表示されています。**[バックアップ項目]** タイルの **[Azure Virtual Machines]** をクリックして、コンテナーに関連付けられている VM を表示します。

    ![vault dashboard](./media/backup-azure-arm-restore-vms/vault-dashboard.png)

    **[バックアップ項目]** ブレードが開き、Azure の仮想マシンの一覧が表示されます。

    ![list of VMs in vault](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault.png)

5. 一覧から VM を選択して、ダッシュボードを開きます。

    ![list of VMs in vault](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault-selected.png)

    VM のダッシュボードが開き、[復旧ポイント] タイルを含む [監視] 領域が表示されます。

    ![list of VMs in vault](./media/backup-azure-arm-restore-vms/vm-blade.png)

6. VM のダッシュボードのメニューで、**[復元]** をクリックします。

    ![list of VMs in vault](./media/backup-azure-arm-restore-vms/vm-blade-menu-restore.png)

    [復元] ブレードが開きます。

    ![restore blade](./media/backup-azure-arm-restore-vms/restore-blade.png)

7. **[復元]** ブレードで **[復旧ポイント]** をクリックして、**[復旧ポイントの選択]** ブレードを開きます。

    ![restore blade](./media/backup-azure-arm-restore-vms/recovery-point-selector.png)

    既定では、ダイアログ ボックスには過去 30 日間のすべての復旧ポイントが表示されます。**[期間]**、**[年]**、および **[復元ポイントの整合性]** の各一覧を必要に応じて編集します。各種の復元ポイントの詳細については、[データの一貫性](./backup-azure-vms-introduction.md#data-consistency)の説明を参照してください。
    - **[期間]** は、特定の月または過去 30 日間です。
    - **[年]** は、特定の年です。
    - **[復元ポイントの整合性]** は、次の一覧から選択します。
        - クラッシュ整合性の復元ポイント
        - アプリケーション整合性の復元ポイント
        - ファイル システム整合性の復元ポイント
        - すべての復元ポイント  

8. 復旧ポイントを選択し、**[OK]** をクリックします。

    ![choose recovery point](./media/backup-azure-arm-restore-vms/select-recovery-point.png)

    **[復元]** ブレードに、設定した復旧ポイントが表示されます。

    ![recovery point is set](./media/backup-azure-arm-restore-vms/recovery-point-set.png)

9. **[復元]** ブレードで、**[復旧構成]** をクリックしてブレードを開きます。

    ![recovery configuration wizard is set](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)

## VM の復旧構成の選択

復旧ポイントを選択したら、ストレージ アカウントと、復旧 VM を構成する方法を選択します。復旧 VM の構成には、Azure ポータルまたは PowerShell を使用できます。ストレージ アカウントを選択するときは、Recovery Services コンテナーと同じ場所を共有するアカウントを選択する必要があります。ゾーン冗長であるストレージ アカウントはサポートされていません。Recovery Services コンテナーと同じ場所を共有するストレージ アカウントがない場合は、復元操作を開始する前にアカウントを作成する必要があります。

1. 現在 **[復元]** ブレードを表示していない場合は、表示します。いずれかの**復旧ポイント**が選択されている状態で、**[復旧構成]** をクリックして **[復旧構成]** ブレードを開きます。

    ![recovery configuration wizard is set](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)

2. **[復旧構成]** ブレードで **[ストレージ アカウント]** をクリックして、Recovery Services コンテナーと同じ場所にあるストレージ アカウントの一覧を開きます。

3. 一覧からストレージ アカウントを選択し、[OK] をクリックします。

    ![list of storage accounts](./media/backup-azure-arm-restore-vms/list-of-storage-accounts.png)

    **[復旧構成]** ブレードで、選択したストレージ アカウントが **[ストレージ アカウント]** ダイアログ ボックスに表示されます。

    ![choose storage accounts](./media/backup-azure-arm-restore-vms/selected-storage-account.png)

4. ポータルで VM を復元するときには、基本的な ARM VM またはクラシック VM を構成できます。次の一覧に示すオプションのような複雑な構成を復元する場合は、PowerShell を使用して、ディスクから VM を構成する必要があります。複雑な構成を復元しない場合は、手順 5. に進みます。

    ![list of complex VM configurations](./media/backup-azure-arm-restore-vms/complex-vm-configurations.png)

    復元の構成が複雑であると考えられる場合は、**[復旧構成]** ブレードで **[OK]** をクリックします。**[復旧構成]** ブレードが閉じ、復旧構成の横にチェックマークが表示されます。**[復元]** をクリックして、ディスクの復元ジョブを開始します。手順 5. には進まずに、「[特別なネットワーク構成を持つ VM の復元](#restoring-vms-with-special-network-configurations)」に進みます。

    ![Restore configured](./media/backup-azure-arm-restore-vms/restore-configured.png)

5. **[復旧構成]** ブレードで **[仮想マシンの構成]** をクリックして、**[VM の作成]** ブレードを開きます。

    ![Open Create VM blade](./media/backup-azure-arm-restore-vms/recovery-configuration-create-vm.png)

6. **[VM の作成]** ブレードで、次の各フィールドの値を入力または選択します。

    - **[仮想マシン名]** - VM の名前を指定します。この名前は、リソース グループ (ARM VM の場合) またはクラウド サービス (クラシック VM の場合) に対して一意である必要があります。同じ名前を持つ既存の VM を置き換える場合は、最初に既存の VM とデータ ディスクを削除した後で、Azure Backup からデータを復元します。
    - **[リソース グループ]** - 既存のリソース グループを使用するか、新しいリソース グループを作成します。クラシック VM を復元する場合は、このフィールドを使用して、新しいクラウド サービスの名前を指定します。新しいリソース グループまたはクラウド サービスを作成する場合、名前はグローバルに一意である必要があります。通常、クラウド サービス名は、公開された URL に関連付けられています (例: [cloudservice].cloudapp.net)。既に使用されているクラウド リソース グループまたはクラウド サービスの名前を指定した場合、Azure はリソース グループまたはクラウド サービスに VM と同じ名前を割り当てます。一覧には、どのアフィニティ グループにも関連付けられていないリソース グループまたはクラウド サービス、および仮想マシンが表示されます。詳細については、「[アフィニティ グループから、リージョン Virtual Network (VNet) に移行する方法](../virtual-network/virtual-networks-migrate-to-regional-vnet.md)」を参照してください。
    - **[Virtual Network]** - VM を作成する場合は、Virtual Network (VNET) を選択します。このフィールドには、サブスクリプションに関連付けられているすべての VNET が表示されます。

    > [AZURE.NOTE] ARM ベースの VM を復元する場合には、VNET の選択は必須です。クラシック VM の場合には、VNET は省略可能です。

    - **[サブネット]** - VNET にサブネットがある場合は、最初のサブネットが既定で選択されています。追加のサブネットがある場合は、必要なサブネットを選択します。

7. **[VM の作成]** ブレードで **[OK]** をクリックして、構成を完了します。

    **[VM の作成]** ブレードが閉じ、**[復旧構成]** ブレードに新しい VM の名前が表示されます。

    ![Recovery configuration completed](./media/backup-azure-arm-restore-vms/recovery-configuration-complete.png)

8. **[復旧構成]** ブレードで **[OK]** をクリックして、復元の構成を完了します。

9. **[復元]** ブレードで **[復元]** をクリックして、復元操作を開始します。

    ![Recovery configuration completed](./media/backup-azure-arm-restore-vms/trigger-restore-operation.png)

## 復元操作を追跡する

復元操作を開始すると、Backup サービスによって、復元操作を追跡するためのジョブが作成されます。また、通知が作成されて一時的に表示されます。

![Restore triggered](./media/backup-azure-arm-restore-vms/restore-triggered.png)

通知が表示されない場合は、いつでも通知アイコンをクリックすることで通知を表示できます。

![Restore triggered](./media/backup-azure-arm-restore-vms/restore-notification.png)

操作を処理中に表示するか、または処理の完了後に表示するには、バックアップ ジョブの一覧を開きます。

1. Azure メニューで **[参照]** をクリックし、サービスの一覧で「**Recovery Services**」と入力します。入力した文字列に合わせて、サービスの一覧の内容が変更されます。**[Recovery Services コンテナー]** が表示されたら、それを選択します。

    ![Open Recovery Services vault](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    サブスクリプションに含まれるコンテナーの一覧が表示されます。

    ![List of Recovery Services vaults](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)

2. 一覧から、復元した VM に関連付けられているコンテナーを選択します。コンテナーをクリックすると、ダッシュボードが開きます。

    ![List of Recovery Services vaults](./media/backup-azure-arm-restore-vms/select-vault-open-vault-jobs.png)

3. コンテナーのダッシュボードの **[バックアップ ジョブ]** タイルで **[Azure Virtual Machines]** をクリックして、コンテナーに関連付けられているジョブを表示します。

    ![vault dashboard](./media/backup-azure-arm-restore-vms/vault-dashboard-jobs.png)

    **[バックアップ ジョブ]** ブレードが開き、ジョブの一覧が表示されます。

    ![list of VMs in vault](./media/backup-azure-arm-restore-vms/restore-job-in-progress.png)




## 特別なネットワーク構成を持つ VM の復元
次のような特別なネットワーク構成を持つ VM をバックアップして復元することができます。ただし、これらの構成では、復元プロセスを実行するときにいくつかの特別な配慮が必要となります。

- ロード バランサー (内部および外部) の対象になっている VM
- 予約済み IP が複数ある VM
- NIC が複数ある VM

>[AZURE.IMPORTANT] VM に対して特別なネットワーク構成を作成するときには、PowerShell を使用してディスクから復元する必要があります。


ディスクへの復元後に仮想マシンを完全に再作成するには、次の手順を実行します。

1. [Azure Backup PowerShell](../backup-azure-vms-automation.md#restore-an-azure-vm) を使用して、Recovery Services コンテナーからディスクを復元します。

2. PowerShell コマンドレットを使用して、ロード バランサー、複数の NIC、複数の予約済み IP に必要な VM 構成を作成し、その構成を使用して、目的の構成の VM を作成します。
	- [内部ロード バランサー](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/)を使用してクラウド サービスに VM を作成する
	- [インターネットに接続するロード バランサー](https://azure.microsoft.com/ja-JP/documentation/articles/load-balancer-internet-getstarted/)に接続する VM を作成する
	- [NIC が複数](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/)ある VM を作成する
	- [予約済み IP が複数](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/)ある VM を作成する

## 次のステップ
これで、VM を復元することができます。VM で発生する一般的なエラーについては、トラブルシューティングの記事を参照してください。また、VM を使用したタスク管理に関する記事もご覧ください。

- [エラーのトラブルシューティング](backup-azure-vms-troubleshoot.md#restore)
- [仮想マシンの管理](backup-azure-manage-vms.md)

<!---HONumber=AcomDC_0420_2016-->