---
title: 'Azure Backup: Azure portal を使用して仮想マシンを復元する'
description: Azure Portal を使用して復旧ポイントから Azure 仮想マシンを復元します
services: backup
author: geethalakshmig
manager: vijayts
keywords: バックアップの復元, 復元する方法, 回復ポイント
ms.service: backup
ms.topic: conceptual
ms.date: 09/04/2017
ms.author: geg
ms.openlocfilehash: ac0c727e41ad9b361ea9f558a97f16446c12ef0e
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/27/2018
ms.locfileid: "53793378"
---
# <a name="use-the-azure-portal-to-restore-virtual-machines"></a>Azure Portal を使用して仮想マシンを復元する
定義された間隔でデータのスナップショットを取得してデータを保護します。 こうしたスナップショットは復旧ポイントと呼ばれ、Recovery Services コンテナーに格納されます。 仮想マシン (VM) を修復または再構築する必要がある場合は、保存されている復旧ポイントのいずれかから VM を復元できます。 復旧ポイントを復元すると、次のことが可能です。

* 新しい仮想マシンを作成する:バックアップされた VM の特定の時点に復旧ポイントから。
* ディスクを復元する:プロセスに含まれるテンプレートを使用して、復元された VM をカスタマイズするか、個別にファイルを回復する。

この記事では、VM を新しい VM に復元するか、バックアップされたディスクすべてを復元する方法について説明します。 ファイルを個別に回復する方法については、[Azure VM バックアップからのファイルの回復](backup-azure-restore-files-from-vm.md)に関するページをご覧ください。

![VM バックアップからの 3 つの復元方法](./media/backup-azure-arm-restore-vms/azure-vm-backup-restore.png)


VM バックアップから VM またはすべてのディスクを復元するには、次の 2 つの手順を実行します。

* 復元を行うための復元ポイントを選択します。
* 復元の種類を選択します
    - オプション 1:新しい仮想マシンを作成する
    - オプション 2:ディスクを復元する。

## <a name="select-a-restore-point-for-restore"></a>復元を行うための復元ポイントを選択する
1. [Azure Portal](http://portal.azure.com/) にサインインします。

2. Azure メニューで、**[すべてのサービス]** を選びます。 サービスの一覧で、「**Recovery Services**」と入力するか、**[ストレージ]** の **[Recovery Services コンテナー]** を選択します。

    ![Recovery Services コンテナー](./media/backup-azure-arm-restore-vms/open-recovery-services-vault1.png)

3. サブスクリプションに含まれるコンテナーの一覧が表示されます。

    ![List of Recovery Services vaults](./media/backup-azure-arm-restore-vms/list-of-rs-vaults1.png)

4. Recovery Services コンテナーの一覧から、復元する VM に関連付けられているコンテナーを選択します。 コンテナーを選択すると、ダッシュボードが開きます。

    ![選択されている Recovery Services コンテナー](./media/backup-azure-arm-restore-vms/select-vault-open-vault-blade1.png)

5. コンテナーのダッシュボードにある **[バックアップ項目]** タイルで、**[Azure 仮想マシン]** を選択します。

    ![コンテナー ダッシュボード](./media/backup-azure-arm-restore-vms/vault-dashboard1.png)

6. **[バックアップ項目]** ブレードが開き、Azure VM の一覧が表示されます。

    ![コンテナーの VM の一覧](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault1.png)

7. 一覧から VM を選択して、ダッシュボードを開きます。 VM のダッシュボードが開き、**[復旧ポイント]** を含む監視領域が表示されます。 **[今すぐバックアップ]**、**[ファイルの回復]**、**[バックアップの停止]** など、VM レベルの操作はすべて、このブレードから実行できます。
復元は、このブレードからさまざまな方法で実行できます。 なお、このブレードには直近 30 日間の復元ポイントしか表示されません。

    - 過去 30 日間の復元ポイントを使用して復元するには、[VM] > **[VM の復元]** を右クリックします。
    - 30 日以上前の復元ポイントを使用して復元するには、**[復元ポイント]** の下のリンクをクリックします。
    - VM を一覧表示し、カスタムの日付でフィルター処理するには、メニューの **[VM の復元]** をクリックします。 フィルターを使用すると、表示される復元ポイントの時間範囲を変更できます。 さまざまな種類のデータ整合性でフィルター処理することもできます。
8. 復元ポイントの設定を確認します。
    - データ整合性は、復元ポイントの[整合性の種類](backup-azure-vms-introduction.md#consistency-types)を示します。
    - **[復元の種類]** は、ポイントが格納されている場所 (ストレージ アカウント内、コンテナー内、またはその両方) を示します。 詳しくは、[インスタント復旧ポイント](https://azure.microsoft.com/blog/large-disk-support/)に関する記事をご覧ください。

  ![復元ポイント](./media/backup-azure-arm-restore-vms/vm-blade1.png)
9. 復元ポイントを選択します。

10. **[復元の構成]** を選択します。 **[復元の構成]** ブレードが開きます。

## <a name="choose-a-vm-restore-configuration"></a>VM の復元の構成を選択する
復元ポイントを選択したら、VM の復元の構成を選択します。 復元された VM を構成するには、Azure Portal または PowerShell を使用します。

### <a name="restore-options"></a>復元オプション

**オプション** | **詳細**
--- | ---
**新規作成 - VM の作成** | VM の簡易作成と同じです。 基本的な VM を復元ポイントから起動し実行します。<br/><br/> VM の設定は復元プロセスの一環として変更することができます。
**新規作成 - ディスクの復元** | 復元プロセスの一環として、カスタマイズ可能な VM を作成します。<br/><br/> このオプションでは、指定したストレージ アカウントに VHD がコピーされます。<br/><br/> - ストレージ アカウントは、コンテナーと同じ場所にある必要があります。<br/><br/> 適切なストレージ アカウントを持っていない場合は、作成する必要があります。<br/><br/> ストレージ アカウントのレプリケーションの種類がかっこ内に示されます。 ゾーン冗長ストレージ (ZRS) はサポートされていません。<br/><br/> ストレージ アカウントからは、復元されたディスクを既存の VM にアタッチするか、PowerShell を使用して復元されたディスクから新しい VM を作成することができます。
**既存の以下のものを置き換えます** | このオプションを使用する場合、VM を作成する必要はありません。<br/><br/> 現在の VM が存在する必要があります。 削除されている場合、このオプションは使用できません。<br/><br/> Azure Backup は、既存の VM のスナップショットを取得して、指定されたステージングの場所に格納します。 VM に接続されている既存のディスクは、選択された復元ポイントに置き換えられます。 以前に作成されたスナップショットは、コンテナーにコピーされ、バックアップ保持ポリシーに従って復旧ポイントとして格納されています。<br/><br/> 既存のものの置き換えは、暗号化されていないマネージド VM でサポートされています。 アンマネージド ディスク、[汎用化された VM](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)、または[カスタム イメージを使用して作成された](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/) VM では、サポートされていません。<br/><br/> 復元ポイントにあるディスクの数が現在の VM よりも多い (または少ない) 場合、復元ポイントのディスク数だけが VM に反映されます。

> [!NOTE]
> 高度なネットワーク設定の VM を復元する場合、たとえば、内部または外部のロード バランサーによって管理されていたり、複数の NIC や複数の予約済み IP アドレスがある場合は、PowerShell を使用して復元します。 [詳細情報](#restore-vms-with-special-network-configurations)。
> Windows VM で [HUB ライセンス](../virtual-machines/windows/hybrid-use-benefit-licensing.md)を使用している場合は、**[新規作成 - ディスクの復元]** オプションを使用し、PowerShell または復元テンプレートを使用して、**[ライセンスの種類]** を **Windows_Server** に設定して VM を作成します。 この設定は、VM の設定後にも適用できます。


復元の設定を次のように指定します。

1. **[復元]** で、[[復元ポイント]](#select-a-restore-point-for-restore) > **[復元の構成]** の順に選択します。
2. **[復元の構成]** で、上記の表にまとめられている設定に従って VM を復元する方法を選択します。

    ![復元の構成ウィザード](./media/backup-azure-arm-restore-vms/restore-configuration-create-new1.png)


## <a name="create-new-create-a-vm"></a>新規作成 - VM の作成

1. **[復元の構成]** > **[新規作成]** > **[復元の種類]** で、**[仮想マシンの作成]** を選択します。
2. **[仮想マシン名]** で、サブスクリプションに存在しない VM を指定します。
3. **[リソース グループ]** で、新しい VM の既存のリソース グループを選択するか、グローバルに一意の名前を持つ新しいリソース グループを作成します。 既に存在している名前を割り当てると、Azure は VM と同じ名前をグループに割り当てます。
4. **[仮想ネットワーク]** で、VM が配置される VNet を選択します。 サブスクリプションに関連付けられているすべての VNet が表示されます。 サブネットを選択します。 最初のサブネットが既定で選択されています。
5. **[ストレージの場所]** で、VM 用に使用するストレージの種類を指定します。

    ![復元の構成ウィザード](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. **[復元の構成]** で、**[OK]** をクリックします。 **[復元]** で **[復元]** をクリックして、復元操作を開始します。



## <a name="create-new-restore-disks"></a>新規作成 - ディスクの復元

1. **[復元の構成]** > **[新規作成]** > **[復元の種類]** で、**[ディスクの復元]** を選択します。
2. **[リソース グループ]** で、復元されたディスクの既存のリソース グループを選択するか、グローバルに一意の名前を持つ新しいリソース グループを作成します。
3. **[ストレージ アカウント]** で、VHD のコピー先のアカウントを指定します。 アカウントがコンテナーと同じリージョンにあることを確認してください。

    ![Recovery configuration completed](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. **[復元の構成]** で、**[OK]** をクリックします。 **[復元]** で **[復元]** をクリックして、復元操作を開始します。
5. ディスクが復元された後、次のいずれかを行って VM 復元操作を完了することができます。

    - 復元操作の一環として生成されたテンプレートを使用して設定をカスタマイズし、VM のデプロイを開始します。 既定のテンプレート設定を編集し、VM デプロイのテンプレートを送信します。
    - 既存の VM に[復元ディスクをアタッチする](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps)ことができます。
    - PowerShell を使用して、復元されたディスクから[新しい VM を作成する](backup-azure-vms-automation.md#restore-an-azure-vm)ことができます。


## <a name="replace-existing-disks"></a>既存のディスクの置き換え

このオプションを使用すると、現在の VM の既存のディスクが、選択した復元ポイントに置き換えられます。

1. **[復元の構成]** で、**[既存の以下のものを置き換えます]** をクリックします。
2. **[復元の種類]** で、**[ディスクの交換]** を選択します (復元ポイントによって既存の VM ディスクが置き換えられます)。
3. **[ステージングの場所]** で、現在のマネージド ディスクのスナップショットの保存場所を指定します。

   ![復元の構成ウィザードの [既存の以下のものを置き換えます]](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)


## <a name="track-the-restore-operation"></a>復元操作を追跡する
復元操作をトリガーすると、バックアップ サービスによって、復元操作を追跡するためのジョブが作成されます。 また、通知が作成されて、ポータルの **[通知]** 領域に一時的に表示されます。 通知が表示されない場合、通知を表示するには、**[通知]** 記号を選択します。

![Restore triggered](./media/backup-azure-arm-restore-vms/restore-notification1.png)

通知のハイパーリンクをクリックすると、**BackupJobs** の一覧が表示されます。 **BackupJobs** の一覧では、特定のジョブについて、操作の詳細をすべて確認できます。 [バックアップ ジョブ] タイルをクリックしてコンテナー ダッシュボードから **BackupJobs** に移動し、**[Azure 仮想マシン]** を選択すると 、コンテナーに関連付けられているジョブが表示されます。

**[バックアップ ジョブ]** ブレードが開き、ジョブの一覧が表示されます。

![コンテナーの VM の一覧](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

**[Restore Details]\(復元の詳細)** ブレードで**進行状況バー**が利用可能になっています。 **[Restore Details]\(復元の詳細)** ブレードは、ステータスが**進行中**の復元ジョブをクリックすると開くことがします。 **進行状況バー**は、**[新規作成]**、**[ディスクの復元]**、**[既存の以下のものを置き換えます]** などのすべての復元で使用できます。 復元の進行状況バーによって伝えられる詳細は、**復元の推定時間**、**復元の割合**、および**転送されたバイト数**です。

進行状況バーの詳細を次に示します。

- **復元の推定時間**は、最初は、復元操作の完了にかかる時間を示します。 操作の進行に伴って所要時間が減り、復元操作が完了すると 0 になります。
- **復元の割合**は、復元操作がどの程度の割合で完了しているかのデータを示します。
- **転送されたバイト数**は、VM の新規作成を通じて復元が行われるときにサブ タスクで使用できます。 これは、転送される総バイト数に対する転送済みバイト数の詳細を示します。


## <a name="use-templates-to-customize-a-restored-vm"></a>テンプレートを使用して復元 VM をカスタマイズする
[ディスクの復元操作が完了](#Track-the-restore-operation)したら、復元操作の一環として生成されたテンプレートを使用して、バックアップ構成とは異なる構成で新しい VM を作成します。 復元ポイントから新しい VM を作成しているときに作成されたリソースの名前を、そのテンプレートを使用してカスタマイズすることもできます。

![復元ジョブのドリルダウン](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

復元ディスクのオプションとして生成されたテンプレートを取得するには:

1. ジョブに対応する **[Restore Job Detail]\(復元ジョブの詳細\)** に移動します。

2. **[Restore Job Detail]\(復元ジョブの詳細\)** 画面で、**[テンプレートのデプロイ]** を選択して、テンプレートのデプロイを開始します。

3. カスタム デプロイの **[テンプレートのデプロイ]** ブレードで、テンプレートのデプロイを使用して[テンプレートを編集およびデプロイ](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)するか、デプロイの前に[テンプレートを作成](../azure-resource-manager/resource-group-authoring-templates.md)することによってさらにカスタマイズを追加します。

  ![テンプレート デプロイの読み込み](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. 必要な値を入力し、**使用条件**に同意して、**[購入]** を選択します。

  ![サンプル テンプレートの送信](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="post-restore-steps"></a>復元後の手順
* Ubuntu など cloud-init ベースの Linux ディストリビューションを使用している場合、セキュリティ上の理由から、復元後にパスワードがブロックされます。 復元した VM で VMAccess 拡張機能を使用して、[パスワードをリセット](../virtual-machines/linux/reset-password.md)してください。 これらのディストリビューションでは、SSH キーを使用して、復元後のパスワード リセットを回避するようお勧めします。
* バックアップの構成の間に存在した拡張機能はインストールされますが、有効にはされません。 問題がある場合は、拡張機能を再インストールしてください。
* バックアップされる VM に静的 IP がある場合は、復元後、復元された VM を作成するときの競合を避けるため、復元される VM には動的 IP が設定されます。 [復元された VM に静的 IP を追加する](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm)方法を参照してください。
* 復元された VM には可用性の値が設定されていません。 復元されたディスクを使用して PowerShell またはテンプレートから VM を作成する場合は、復元ディスク オプションを使って[可用性セットを追加する](../virtual-machines/windows/tutorial-availability-sets.md)ことをお勧めします。


## <a name="backup-for-restored-vms"></a>復元された VM のバックアップ
最初にバックアップされた VM と同じ名前で同じリソース グループに VM を復元した場合、復元後も VM に対するバックアップは引き続き行われます。 別のリソース グループに VM を復元した場合、または復元された VM に別の名前を指定した場合、この VM は新しい VM として扱われ、 復元された VM に対してバックアップを設定する必要があります。

## <a name="restore-a-vm-during-an-azure-datacenter-disaster"></a>Azure データ センターでの障害発生時に VM を復元する
VM が稼働しているプライマリ データ センターが被災した場合、バックアップ コンテナーが geo 冗長に構成されていると、Azure Backup ではバックアップされた VM をペアのデータセンターに復元することができます。 このようなシナリオでは、ペアのデータ センター内に存在するストレージ アカウントを選択します。 これ以外の復元処理は同じままです。 Azure Backup では、ペアの geo からコンピューティング サービスを使って、復元された VM を作成します。 詳細については、[Azure データセンターの回復性](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md)に関するページをご覧ください。

## <a name="restore-domain-controller-vms"></a>ドメイン コントローラー VM を復元する
ドメイン コントローラー (DC) VMのバックアップは、Backup でサポートされているシナリオです。 ただし、この復元プロセスでは注意が必要です。 適切な復元プロセスは、ドメインの構造によって異なります。 最も単純なのは、1 つのドメインに 1 つの DC があるケースです。 運用環境の負荷としてより一般的なのは、1 つのドメインに複数の DC があるケースです。オンプレミスに DC がいくつかあるケースも考えられます。 そして、1 つのフォレストに複数のドメインがあるケースもあります。

Active Directory の観点からは、Azure VM は、サポートされている最新のハイパーバイザー上にある他の VM と変わりません。 オンプレミスのハイパーバイザーとの大きな違いは、Azure では VM コンソールが使用できないことです。 コンソールは、ベア メタル回復 (BMR) タイプのバックアップを使用して回復するといった特定のシナリオで必要です。 ただし、バックアップ コンテナーからの VM の復元が、BMR の代わりとなります。 ディレクトリ サービス復元モード (DSRM) も利用できるので、Active Directory の復元シナリオはすべて実行可能です。 詳細については、[仮想化されたドメイン コントローラーのバックアップと復元の考慮事項](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv(v=ws.10).aspx#backup_and_restore_considerations_for_virtualized_domain_controllers)に関するページおよび[Active Directory フォレストの回復の計画](https://technet.microsoft.com/library/planning-active-directory-forest-recovery(v=ws.10).aspx)に関するページをご覧ください。

### <a name="single-dc-in-a-single-domain"></a>1 つのドメインに 1 つの DC がある
VM は (他の VM と同様に) Azure Portal または PowerShell を使用して復元できます。

### <a name="multiple-dcs-in-a-single-domain"></a>1 つのドメインに複数の DC がある
同じドメインの他の DC に、ネットワーク経由で到達できる場合は、VM と同様に DC を復元できます。 ドメインの最後の DC の場合や、分離ネットワークの回復を実行した場合は、フォレスト回復の手順に従う必要があります。

### <a name="multiple-domains-in-one-forest"></a>1 つのフォレストに複数のドメインがある
同じドメインの他の DC に、ネットワーク経由で到達できる場合は、VM と同様に DC を復元できます。 それ以外の場合は、フォレストの回復をお勧めします。

## <a name="restore-vms-with-special-network-configurations"></a>特別なネットワーク構成を持つ VM を復元する
次のような特別なネットワーク構成を持つ VM をバックアップして復元することができます。 ただし、次の構成では、復元プロセスを実行するときにいくつかの特別な配慮が必要となります。

* ロード バランサー (内部および外部) の対象になっている VM
* 予約済み IP が複数ある VM
* NIC が複数ある VM

> [!IMPORTANT]
> VM に対して特別なネットワーク構成を作成するときは、PowerShell を使用して、復元されたディスクから VM を作成する必要があります。
>
>

ディスクへの復元後に VM を完全に作成し直すには、次の手順を実行します。

1. [PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm) を使用して、Recovery Services コンテナーからディスクを復元します。

1. PowerShell コマンドレットを使用して、ロード バランサー、複数の NIC、複数の予約済み IP に必要な VM 構成を作成します。 その構成を使用して、目的の構成で VM を作成します。

   a. [内部ロード バランサー](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/)を使用して、クラウド サービスで VM を作成します。

   b. [インターネットに接続されているロード バランサー](https://azure.microsoft.com/documentation/articles/load-balancer-internet-getstarted/)に接続する VM を作成します。

   c. [複数 NIC](../virtual-machines/windows/multiple-nics.md) を持つ VM を作成します。

   d. [複数の予約済み IP](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md) を持つ VM を作成します。

## <a name="next-steps"></a>次の手順
これで、VM を復元することができます。VM で発生する一般的なエラーについては、トラブルシューティングの記事を参照してください。 また、VM を使用したタスク管理に関する記事もご覧ください。

* [エラーのトラブルシューティング](backup-azure-vms-troubleshoot.md#restore)
* [仮想マシンの管理](backup-azure-manage-vms.md)
