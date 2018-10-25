---
title: 'Azure Backup: Azure Portal を使用して仮想マシンを復元する'
description: Azure Portal を使用して復旧ポイントから Azure 仮想マシンを復元します
services: backup
author: geethalakshmig
manager: vijayts
keywords: バックアップの復元, 復元する方法, 回復ポイント
ms.service: backup
ms.topic: conceptual
ms.date: 09/04/2017
ms.author: geg
ms.openlocfilehash: 450314dddd49825bae689701b694f9a26758835e
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2018
ms.locfileid: "49377641"
---
# <a name="use-the-azure-portal-to-restore-virtual-machines"></a>Azure Portal を使用して仮想マシンを復元する
定義された間隔でデータのスナップショットを取得してデータを保護します。 こうしたスナップショットは復旧ポイントと呼ばれ、Recovery Services コンテナーに格納されます。 仮想マシン (VM) を修復または再構築する必要がある場合は、保存されている復旧ポイントのいずれかから VM を復元できます。 復旧ポイントを復元すると、次のことが可能です。

* 特定の時点のバックアップ VM である VM を新しく作成する。
* ディスクを復元し、そのプロセスに含まれるテンプレートを使用して、復元された VM をカスタマイズするか、個別にファイルを回復する。

この記事では、VM を新しい VM に復元するか、バックアップされたディスクすべてを復元する方法について説明します。 ファイルを個別に回復する方法については、[Azure VM バックアップからのファイルの回復](backup-azure-restore-files-from-vm.md)に関するページをご覧ください。

![VM バックアップからの 3 つの復元方法](./media/backup-azure-arm-restore-vms/azure-vm-backup-restore.png)

> [!NOTE]
> Azure には、リソースの作成と操作に関して、[Azure Resource Manager とクラシック](../azure-resource-manager/resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、デプロイされた VM を、Resource Manager モデルで復元するときに使用する情報と手順を示します。
>
>

VM バックアップから VM またはすべてのディスクを復元するには、次の 2 つの手順を実行します。

* 復元を行うための復元ポイントを選択します。
* 復元の種類を選択し、新しい VM を作成するかディスクを復元して、必要なパラメーターを指定します。

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
復元は、このブレードからさまざまな方法で実行できます。 なお、このブレードには直近 30 日間の復旧ポイントしか表示されません。

    a) このブレードで復旧ポイント (過去 30 日以内) を右クリックし、**VM の復元**を開始します。

    b) 30 日以上前の復旧ポイントを復元するには、ブレード内にある [ここをクリック] を使用できます。

    c) メニュー ヘッダーの **[VM の復元]** では、VM を一覧表示し、カスタムの日付でフィルター処理するためのオプションが提供されます。

    フィルターを使用すると、表示される復元ポイントの時間範囲を変更できます。 既定では、すべての一貫性の復元ポイントが表示されます。 復元ポイントの特定の一貫性を選択するには、[すべての復元ポイント] フィルターを変更します。 各種の復元ポイントの詳細については、「[データの一貫性](backup-azure-vms-introduction.md#data-consistency)」を参照してください。

    復元ポイントの一貫性のオプション:
    - クラッシュ整合復元ポイント
    - アプリケーション整合復元ポイント
    - ファイル システム整合復元ポイント
    - すべての復元ポイント

  ![復元ポイント](./media/backup-azure-arm-restore-vms/vm-blade1.png)

    >  [!NOTE]
    > 回復の種類は、データがお客様のストレージ アカウント内にあるのか、コンテナー内にあるのか、それともその両方にあるのかを表します。 詳しくは、[インスタント復旧ポイント](https://azure.microsoft.com/blog/large-disk-support/)に関する記事をご覧ください。

8. **[復元]** ブレードで、**[復元ポイント]** を選択します。

    ![復元ポイントの選択](./media/backup-azure-arm-restore-vms/select-recovery-point1.png)

    **[OK]** をクリックすると、**[復元]** ブレードに復元ポイントが設定されていることが表示されます。
9. **[復元]** ブレードに移動します (まだ移動していない場合)。 [復元ポイントが選択されている](#select-a-restore-point-for-restore)ことを確認し、**[復元の構成]** を選択します。 **[復元の構成]** ブレードが開きます。

## <a name="choose-a-vm-restore-configuration"></a>VM の復元の構成を選択する
復元ポイントを選択したら、VM の復元の構成を選択します。 復元された VM を構成するには、Azure Portal または PowerShell を使用します。

1. **[復元]** ブレードに移動します (まだ移動していない場合)。 [復元ポイントが選択されている](#select-a-restore-point-for-restore)ことを確認し、**[復元の構成]** を選択します。 **[復元の構成]** ブレードが開きます。
2. このブレードには現在 2 つのオプションがあります。1 つは既定のオプションである **[新規作成]** で、もう 1 つは **[既存の以下のものを置き換えます]** です。これは、既存の構成と拡張機能だけを保持してディスクを置き換える、インプレース復元です。

> [!NOTE]
> Microsoft では、今後数か月以内に、ディスク、ネットワーク設定、構成、拡張機能を含む VM 全体を置き換えられるようにするべく取り組んでいます。
>
>

 新しい VM や新しいディスクにデータを復元する **[新規作成]** オプションには、次の 2 つの選択肢があります。

 ![復元の構成ウィザード](./media/backup-azure-arm-restore-vms/restore-configuration-create-new.png)

 - **仮想マシンの作成**
 - **ディスクの復元**

![復元の構成ウィザード](./media/backup-azure-arm-restore-vms/restore-configuration-create-new1.png)

ポータルには、復元された VM の **[簡易作成]** オプションがあります。 VM 構成をカスタマイズするには、または、新しい VM 選択肢の作成操作の一環として作成されたリソースの名前をカスタマイズするには、PowerShell またはポータルを使用してバックアップ ディスクを復元し、 そのディスクを、PowerShell コマンドで VM 構成の選択肢にアタッチします。 また、復元ディスクに含まれるテンプレートを使用して、復元された VM をカスタマイズすることもできます。 複数の NIC を持つ VM、またはロード バランサーの VM を復元する方法については、「[特別なネットワーク構成を持つ VM の復元](#restore-vms-with-special-network-configurations)」を参照してください。 Windows VM が [HUB ライセンス](../virtual-machines/windows/hybrid-use-benefit-licensing.md)を使用している場合は、この記事で指定したように、ディスクを復元し、PowerShell/テンプレートを使用して VM を作成します。 復元された VM で HUB の利点を利用するには、VM を作成する際に、**ライセンスの種類**を "Windows_Server" と指定します。 なお、これは VM の作成後に実行することもできます。

## <a name="create-a-new-vm-from-a-restore-point"></a>復元ポイントから新しい VM を作成する
1. 前のセクションで説明した **[復元の構成]** ブレードで、次の各フィールドの値を入力または選択します。

    a. **復元の種類**。 仮想マシンを作成します。

    b. **仮想マシン名**。 サブスクリプション内に存在しない VM 名を入力します。

    c. **リソース グループ**。 既存のリソース グループを使用するか、新しいリソース グループを作成します。 クラシック VM を復元する場合は、このフィールドを使用して、新しいクラウド サービスの名前を指定します。 新しいリソース グループ/クラウド サービスを作成する場合、名前はグローバルに一意である必要があります。 通常、クラウド サービス名は、公開された URL に関連付けられています (例: [cloudservice].cloudapp.net)。 既に使用中のクラウド リソース グループ/クラウド サービスの名前を指定した場合、Azure はリソース グループ/クラウド サービスに VM と同じ名前を割り当てます。 一覧には、どのアフィニティ グループにも関連付けられていないリソース グループまたはクラウド サービス、および仮想マシンが表示されます。 詳細については、[アフィニティ グループからリージョン仮想ネットワークに移行する方法](../virtual-network/virtual-networks-migrate-to-regional-vnet.md)に関するページをご覧ください。

    d. **Virtual network**。 VM を作成する場合は、仮想ネットワークを選択します。 このフィールドには、サブスクリプションに関連付けられているすべての仮想ネットワークが表示されます。 VM のリソース グループがかっこ内に表示されます。

    e. **サブネット**。 仮想ネットワークにサブネットがある場合は、最初のサブネットが既定で選択されています。 追加のサブネットがある場合は、必要なサブネットを選択します。

    f. **保存場所**。 ストレージ アカウントはステージングの場所です。 このメニューには、Recovery Services コンテナーと同じ場所にあるストレージ アカウントが表示されます。 ゾーン冗長であるストレージ アカウントはサポートされていません。 Recovery Services コンテナーと同じ場所を共有するストレージ アカウントがない場合は、復元操作を開始する前にアカウントを作成する必要があります。 ストレージ アカウントのレプリケーションの種類がかっこ内に表示されます。

    ![復元の構成ウィザード](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

    > [!NOTE]
    > * 仮想ネットワークは、クラシック VM については省略可能で、Resource Manager でデプロイされた VM については必須です。

    > * ステージング場所のストレージ アカウント (Premium または Standard) で指定されたストレージの種類によって、復元ディスクのストレージの種類が決まります。 現在、復元時に混合モードはサポートされていません。
    >
    >

2. **[復元の構成]** ブレードで **[OK]** を選択し、復元の構成を完了します。 **[復元]** ブレードで **[復元]** を選択し、復元操作をトリガーします。

## <a name="restore-backed-up-disks"></a>バックアップ ディスクを復元する
**[復元の構成]** ブレードで復元の種類を **[ 	ディスクの復元]** と指定した場合は、カスタムの構成で VM を作成できます。 ディスクを復元する際には、選択するストレージ アカウントが Recovery Services コンテナーと同じ場所にある必要があります。 Recovery Services コンテナーと同じ場所を共有するストレージ アカウントがない場合は、ストレージ アカウントを作成することが必須となります。 ZRS ストレージ アカウントはサポートされていません。 ストレージ アカウントのレプリケーションの種類は、かっこ内に表示されます。

復元後の操作については、以下の記事をご覧ください。
* [テンプレートを使用して、復元された VM をカスタマイズする](#use-templates-to-customize-restore-vm)
* [復元ディスクを使用して、既存の VM にアタッチする](../virtual-machines/windows/attach-managed-disk-portal.md)
* [PowerShell を使用して、復元されたディスクから新しい VM を作成する](./backup-azure-vms-automation.md#restore-an-azure-vm)

**[復元の構成]** ブレードで **[OK]** を選択し、復元の構成を完了します。 **[復元]** ブレードで **[復元]** を選択し、復元操作をトリガーします。

![Recovery configuration completed](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

イン**プレース復元**は、**[既存の以下のものを置き換えます]** タブを通じて実行します。

## <a name="replace-existing-disks-from-a-restore-point"></a>既存のディスクを復元ポイントから置き換える
**[既存の以下のものを置き換えます]** オプションを使用すると、現在の VM の既存のディスクを、選択した復元ポイントに置き換えることができます。 この操作は、現在の VM が存在する場合にのみ実行できます。 何らかの理由で削除された場合は、この操作を実行することはできません。その場合は、VM またはディスクの**新規作成**を行って、復元操作を続行することを勧めします。 既存ディスクの置き換え操作時には、ディスクの置き換え操作を開始する前に、予防措置としてデータがバックアップされます。 復元ポイントにあるディスクの数が現在の VM よりも多い (または少ない) 場合、復元ポイントのディスク数だけが VM に反映されます。 現在のところ、[既存の以下のものを置き換えます] オプションは、マネージド ディスクと非暗号化 VM についてのみサポートされています。  

 **[復元の構成]** ブレードで選択する必要があるのは、**[ステージングの場所]** だけです。

   ![復元の構成ウィザードの [既存の以下のものを置き換えます]](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

 a. **復元の種類**。 [ディスクの交換] は、選択された復元ポイントによって既存の VM 内のディスクが置き換えられることを表します。

 b. **ステージングの場所**。 ストレージ アカウントはマネージド ディスクのステージングの場所です。 このメニューには、Recovery Services コンテナーと同じ場所にあるストレージ アカウントが表示されます。 ゾーン冗長であるストレージ アカウントはサポートされていません。 Recovery Services コンテナーと同じ場所を共有するストレージ アカウントがない場合は、復元操作を開始する前にアカウントを作成する必要があります。 ストレージ アカウントのレプリケーションの種類がかっこ内に表示されます。

## <a name="track-the-restore-operation"></a>復元操作を追跡する
復元操作をトリガーすると、バックアップ サービスによって、復元操作を追跡するためのジョブが作成されます。 また、通知が作成されて、ポータルの **[通知]** 領域に一時的に表示されます。 通知が表示されない場合、通知を表示するには、**[通知]** 記号を選択します。

![Restore triggered](./media/backup-azure-arm-restore-vms/restore-notification1.png)

通知のハイパーリンクをクリックすると、**BackupJobs** の一覧が表示されます。 **BackupJobs** の一覧では、特定のジョブについて、操作の詳細をすべて確認できます。 [バックアップ ジョブ] タイルをクリックしてコンテナー ダッシュボードから **BackupJobs** に移動し、**[Azure 仮想マシン]** を選択すると 、コンテナーに関連付けられているジョブが表示されます。

**[バックアップ ジョブ]** ブレードが開き、ジョブの一覧が表示されます。

![コンテナーの VM の一覧](./media/backup-azure-arm-restore-vms/restore-job-completed.png)

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
