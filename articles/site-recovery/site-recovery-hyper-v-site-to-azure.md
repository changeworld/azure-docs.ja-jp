---
title: "Hyper-V VM を Azure にレプリケートする | Microsoft Docs"
description: "オンプレミス Hyper-V VM の Azure へのレプリケーション、フェールオーバー、および復旧を調整する方法を説明します"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 1777e0eb-accb-42b5-a747-11272e131a52
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 04/05/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: 0ef782a7bb7a98da2ec63c91732b3d5ddd959848
ms.contentlocale: ja-jp
ms.lasthandoff: 05/17/2017

---

# <a name="replicate-hyper-v-virtual-machines-without-vmm-to-azure-using-azure-site-recovery-with-the-azure-portal"></a>Azure Portal で Azure Site Recovery を使用して、(VMM なしで) Hyper-V 仮想マシンを Azure にレプリケートする

> [!div class="op_single_selector"]
> * [Azure ポータル](site-recovery-hyper-v-site-to-azure.md)
> * [Azure クラシック](site-recovery-hyper-v-site-to-azure-classic.md)
> * [PowerShell - Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md)
>
>

この記事では、オンプレミスの Hyper-V 仮想マシンを、Azure Portal の [Azure Site Recovery](site-recovery-overview.md) を使用して Azure にレプリケートする方法について説明します。 このデプロイでは、Hyper-V VM は System Center Virtual Machine Manager (VMM) によって管理されません。

コメントはこの記事の末尾に投稿し、技術的な質問は [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。

Azure にマシンを (フェールバックなしで) 移行する方法について詳しくは、[この記事](site-recovery-migrate-to-azure.md)をご覧ください。



## <a name="deployment-steps"></a>デプロイメントの手順

記事に従って、次のデプロイ手順を実行します。

1. このデプロイのアーキテクチャの[詳細を確認](site-recovery-components.md#hyper-v-to-azure)します。 また、Site Recovery で Hyper-V レプリケーションがどのように動作するかの[詳細を確認](site-recovery-hyper-v-azure-architecture.md)します。
2. 前提条件と制限事項を確認します。
3. Azure ネットワークおよびストレージ アカウントを設定します。
4. Hyper-V ホストを準備します。
5. Recovery Services コンテナーを作成します。 このコンテナーには構成設定が含まれ、レプリケーションを調整します。
6. ソース設定を指定します。 Hyper-V ホストを含む Hyper-V サイトを作成し、コンテナーにそのサイトを登録します。 Azure Site Recovery プロバイダーをインストールし、Hyper-V ホストに Microsoft Recovery Services エージェントをインストールします。
7. ターゲットとレプリケーションを設定します。
8. VM のレプリケーションを有効にします。
9. テスト フェールオーバーを実行して、すべて想定どおりに動作していることを確認します。



## <a name="prerequisites"></a>前提条件


**要件** | **詳細** |
--- | --- |
**Azure** | [Azure の要件](site-recovery-prereq.md#azure-requirements)を確認します。
**オンプレミスのサーバー** | オンプレミスの Hyper-V ホストの要件の[詳細を確認](site-recovery-prereq.md#disaster-recovery-of-hyper-v-virtual-machines-to-azure-no-virtual-machine-manager)します。
**オンプレミスの Hyper-V VM** | レプリケートする VM では、[サポートされるオペレーティング システム](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)を実行し、[Azure の前提条件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)に準拠する必要があります。
**Azure の URL** | Hyper-V ホストには、これらの URL へのアクセス権が必要です。<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> IP アドレスベースのファイアウォール規則を使用している場合、その規則で Azure との通信が許可されていることを確認します。<br/></br> [Azure データセンターの IP の範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)と HTTPS (443) ポートを許可します。<br/></br> ご利用のサブスクリプションの Azure リージョンと米国西部の IP アドレス範囲を許可します (Access Control と ID 管理に使用されます)。



## <a name="prepare-for-deployment"></a>デプロイの準備をする

デプロイを準備するには、次の手順に従います。

1. [Azure ネットワークをセットアップ](#set-up-an-azure-network) します。
2. [Azure ストレージ アカウントを設定](#set-up-an-azure-storage-account) します。
3. [Hyper-V ホストを準備](#prepare-the-hyper-v-hosts) します。

### <a name="set-up-an-azure-network"></a>Azure ネットワークをセットアップ

Azure ネットワークをセットアップします。 これは、フェールオーバー後に作成された Azure VM がネットワークに接続するために必要となります。

* ネットワークは、Recovery Services コンテナーと同じリージョンにある必要があります。
* フェールオーバーされた Azure VM に使用するリソース モデルに応じて、Azure ネットワークを [Resource Manager モード](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)または[クラシック モード](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)でセットアップします。
* ネットワークをセットアップしてから、以下の作業を開始することをお勧めします。 行わない場合は、Site Recovery のデプロイ中に行う必要があります。
- Site Recovery で使用されているストレージ アカウントは、同じまたは異なるサブスクリプション内で[移動](../azure-resource-manager/resource-group-move-resources.md)できません。


### <a name="set-up-an-azure-storage-account"></a>Azure Storage アカウントを設定

- Azure にレプリケートされたデータを保持するには、Standard または Premium の Azure ストレージ アカウントが必要です。通常、[Premium Storage](../storage/storage-premium-storage.md) は、IO を集中的に行うワークロードをホストするために、常に高い IO パフォーマンスと低待機時間を必要とする仮想マシンに使用されます。
- レプリケートされたデータを Premium アカウントを使用して保存する場合は、オンプレミスのデータの継続的な変更をキャプチャするレプリケーション ログを保存するために、Standard ストレージ アカウントも必要になります。
- フェールオーバーされた Azure VM に使用するリソース モデルに応じて、アカウントを [Resource Manager モード](../storage/storage-create-storage-account.md)または[クラシック モード](../storage/storage-create-storage-account-classic-portal.md)でセットアップします。
- ストレージ アカウントをセットアップしてから、以下の作業を開始することをお勧めします。 行わない場合は、Site Recovery のデプロイ中に行う必要があります。 アカウントは、Recovery Services コンテナーと同じリージョンに存在する必要があります。
- 同じサブスクリプション内のリソース グループ間、または異なるサブスクリプション間で、Site Recovery で使用されるストレージ アカウントを移動することはできません。


### <a name="prepare-the-hyper-v-hosts"></a>Hyper-V ホストを準備

* Hyper-V ホストが[前提条件](site-recovery-prereq.md#disaster-recovery-of-hyper-v-virtual-machines-to-azure-no-virtual-machine-manager)を満たしていることを確認します。
- ホストが、必要な URL にアクセスできることを確認します。


## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する
1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. **[新規]** > 、**[監視 + 管理]**、 > **[Backup and Site Recovery (OMS) (バックアップと Site Recovery (OMS))]** の順にクリックします。  

    ![新しいコンテナー](./media/site-recovery-hyper-v-site-to-azure/new-vault.png)

3. **[名前]**に、コンテナーを識別するフレンドリ名を入力します。 複数のサブスクリプションがある場合は、いずれかを選択します。

4. [新しいリソース グループを作成](../azure-resource-manager/resource-group-template-deploy-portal.md) するか、既存のリソース グループを選択し、Azure リージョンを指定します。 マシンは、このリージョンにレプリケートされます。 サポートされているリージョンを確認するには、[Azure Site Recovery 価格の詳細](https://azure.microsoft.com/pricing/details/site-recovery/)に関するページで利用可能地域をご覧ください。

5. ダッシュボードからコンテナーにすばやくアクセスするには、**[ダッシュボードにピン留めする]** をクリックしてから **[作成]** をクリックします。

    ![新しいコンテナー](./media/site-recovery-hyper-v-site-to-azure/new-vault-settings.png)

新しいコンテナーは、**[ダッシュボード]** > **[すべてのリソース]** リストと、メインの **[Recovery Services コンテナー]** ブレードに表示されます。



## <a name="select-the-protection-goal"></a>保護の目標を選択する

レプリケートの対象とレプリケート先を選択します。

1. **[Recovery Services コンテナー]** で、コンテナーを選びます。
2. **[作業の開始]** で、**[Site Recovery]** > **[インフラストラクチャの準備]** > **[保護の目標]** の順にクリックします。

    ![Choose goals](./media/site-recovery-hyper-v-site-to-azure/choose-goals.png)
3. **[保護の目標]** で、**[Azure へ]** を選択し、**[Yes, with Hyper-V (はい、Hyper-V を使用する)]** を選択します。 VMM を使用していないことを確認するために、 **[いいえ]** を選択します。 次に、 **[OK]**をクリックします

    ![Choose goals](./media/site-recovery-hyper-v-site-to-azure/choose-goals2.png)

## <a name="set-up-the-source-environment"></a>ソース環境をセットアップする

Hyper-V サイトをセットアップし、Azure Site Recovery Provider と Azure Recovery Services エージェントを Hyper-V ホストにインストールして、サイトをコンテナーに登録します。

1. **[インフラストラクチャの準備]** で、**[ソース]** をクリックします。 Hyper-V ホストまたはクラスターのコンテナーとして、新しい Hyper-V サイトを追加するには、 **[+ Hyper-V サイト]**をクリックします。

    ![Set up source](./media/site-recovery-hyper-v-site-to-azure/set-source1.png)
2. **[Hyper-V サイトの作成]** で、サイトの名前を指定します。 次に、 **[OK]**をクリックします 作成したサイトを選択し、**[+Hyper-V Server]** をクリックしてサーバーをサイトに追加します。

    ![Set up source](./media/site-recovery-hyper-v-site-to-azure/set-source2.png)

3. **[サーバーの追加]**  >  **[サーバーの種類]** で、**[Hyper-V Server]** が表示されていることを確認します。

    - 追加する Hyper-V Server が[前提条件](#on-premises-prerequisites)に準拠しており、指定された URL にアクセスできることを確認します。
    - Azure Site Recovery プロバイダーのインストール ファイルをダウンロードします。 このファイルを実行して、各 Hyper-V ホストにプロバイダーと Recovery Services エージェントをインストールします。

    ![Set up source](./media/site-recovery-hyper-v-site-to-azure/set-source3.png)


## <a name="install-the-provider-and-agent"></a>プロバイダーとエージェントのインストール

1. Hyper-V サイトに追加した各ホストで、プロバイダーのセットアップ ファイルを実行します。 Hyper-V クラスターにインストールする場合は、各クラスター ノードでセットアップを実行します。 各 Hyper-V クラスター ノードにインストールして登録すると、VM をノード間で移行しても保護が維持されます。
2. **[Microsoft Update]** で更新プログラムを登録すると、Microsoft Update ポリシーに従ってプロバイダーの更新プログラムがインストールされます。
3. **[インストール]** で、プロバイダーの既定のインストール先をそのまま使用するか、インストール先を変更して、**[インストール]** をクリックします。
4. **[資格情報コンテナー設定]** で **[参照]** をクリックして、ダウンロードしたコンテナー キー ファイルを選択します。 Azure Site Recovery のサブスクリプション、コンテナー名、Hyper-V サーバーが属している Hyper-V サイトを指定します。

    ![サーバー登録](./media/site-recovery-hyper-v-site-to-azure/provider3.png)

5. **[プロキシの設定]** で、Hyper-V ホストで実行されているプロバイダーがインターネット経由で Azure Site Recovery に接続する方法を指定します。

    * プロバイダーから直接接続するように指定する場合は、 **[プロキシを使用せずに直接 Azure Site Recovery に接続する]**を選択します。
    * 既存のプロキシに認証が必要な場合、またはプロバイダーの接続用にカスタム プロキシを使用する場合は、**[プロキシ サーバーを使用して Azure Site Recovery に接続する]** を選択します。
    * プロキシを使用する場合は、
        - アドレス、ポート、資格情報を指定します。
        - [前提条件](#prerequisites)に関するセクションで示されている URL がプロキシを介して許可されていることを確認します。

    ![internet](./media/site-recovery-hyper-v-site-to-azure/provider7.PNG)

6. インストールが完了したら、**[登録]** をクリックして、サーバーをコンテナーに登録します。

    ![インストール場所](./media/site-recovery-hyper-v-site-to-azure/provider2.png)

7. 登録が完了すると、Azure Site Recovery によって Hyper-V サーバーからメタデータが取得され、**[Site Recovery インフラストラクチャ]** > **[Hyper-V ホスト]** にサーバーが表示されます。


## <a name="set-up-the-target-environment"></a>ターゲット環境をセットアップする

レプリケーションに使用する Azure ストレージ アカウントと、フェールオーバー後に Azure VM が接続する Azure ネットワークを指定します。

1. **[インフラストラクチャの準備]** > **[ターゲット]** の順にクリックします。
2. サブスクリプションと、フェールオーバー後に Azure VM を作成するリソース グループを選択します。 Azure で、VM に使用するデプロイ モデル (クラシックまたはリソース管理) を選択します。

3. Site Recovery によって、互換性のある Azure ストレージ アカウントとネットワークが 1 つ以上あるかどうかが確認されます。

    - ストレージ アカウントがない場合、**[+ストレージ]** をクリックして Resource Manager ベースのアカウントをインラインで作成します。 [ストレージ要件](site-recovery-prereq.md#azure-requirements)を確認します。
    - Azure ネットワークがない場合、**[+ネットワーク]** をクリックして Resource Manager ベースのネットワークをインラインで作成します。

    ![Storage](./media/site-recovery-vmware-to-azure/enable-rep3.png)




## <a name="configure-replication-settings"></a>レプリケーションの設定を構成する

1. 新しいレプリケーション ポリシーを作成するには、**[インフラストラクチャの準備]** > **[レプリケーションの設定]** > **[+ 作成と関連付け]** の順にクリックします。

    ![ネットワーク](./media/site-recovery-hyper-v-site-to-azure/gs-replication.png)
2. **[ポリシーの作成と関連付け]**で、ポリシー名を指定します。
3. **[コピーの頻度]**で、初期レプリケーションの後、差分データをレプリケートする頻度 (30 秒ごと、5 分ごと、または 15 分ごと) を指定します。

    > [!NOTE]
    > Premium Storage にレプリケートするときには、30 秒の頻度はサポートされません。 上限は、Premium Storage によってサポートされる BLOB ごとのスナップショットの数 (100) によって決まります。 [詳細情報](../storage/storage-premium-storage.md#snapshots-and-copy-blob)。

4. **[復旧ポイントの保持期間]** で、各復旧ポイントのリテンション期間の長さ (時間単位) を指定します。 VM はこの期間内のどのポイントにも復旧できます。
5. **[アプリ整合性スナップショットの頻度]** で、アプリケーション整合性スナップショットを含む復旧ポイントの作成頻度 (1 - 12 時間) を指定します。

    - Hyper-V では 2 種類のバックアップを使用します。1 つは標準バックアップで、仮想マシン全体の増分バックアップを実行します。もう 1 つは、アプリケーション整合性スナップショットで、仮想マシン内部のアプリケーション データの特定の時点のスナップショットを作成します。
    - アプリケーション整合性スナップショットでは、ボリューム シャドウ コピー サービス (VSS) を使用して、スナップショットを作成するときにアプリケーションを一貫性のある状態に保ちます。
    - アプリケーション整合性スナップショットを有効にすると、ソースの仮想マシンで実行されるアプリケーションのパフォーマンスに影響があります。 設定する値は、追加で構成する復旧ポイントの数より少ない数にしてください。

6. **[初期レプリケーションの開始時刻]** で、初期レプリケーションを開始する時刻を指定します。 レプリケーションはご利用のインターネット帯域幅で行われるため、トラフィックの多い時間帯を避けるようにスケジュールを設定することをお勧めします。 次に、 **[OK]**をクリックします

    ![Replication policy](./media/site-recovery-hyper-v-site-to-azure/gs-replication2.png)

新しいポリシーを作成すると、自動的に Hyper-V サイトに関連付けられます。 Hyper-V サイト (およびサイト内の VM) を複数のレプリケーション ポリシーに関連付けるには、**[レプリケーション]**、ポリシー名、**[Associate Hyper-V Site (Hyper-V サイトの関連付け)]** の順にクリックします。

## <a name="capacity-planning"></a>容量計画

基本的なインフラストラクチャをセットアップできたので、キャパシティ プランニングを立案し、追加のリソースが必要かどうかを検討できます。

Site Recovery が備えている Capacity Planner を使用して、コンピューティング、ネットワーク、およびストレージに適切なリソースを割り当てることができます。 このキャパシティ プランニング ツールは、VM、ディスク、ストレージの平均数に基づく見積もりを使用するクイック モードか、ワークロード レベルでカスタマイズされた数値を入力する詳細モードで実行できます。 開始する前に、次のことを行う必要があります。

* VM、VM あたりのディスク数、ディスクあたりのストレージなど、レプリケーション環境の情報を収集する。
* レプリケートされたデータの 1 日の変更 (チャーン) 率を見積もる。 この見積もりには、 [Capacity Planner for Hyper-V Replica](https://www.microsoft.com/download/details.aspx?id=39057) が役立ちます。

1. **[ダウンロード]** をクリックしてツールをダウンロードし、実行します。 [こちらの記事をご覧ください](site-recovery-capacity-planner.md) 。
2. 作業が完了したら、**[Have you run the Capacity Planner? (Capacity Planner を実行しましたか?)]** で **[はい]** を選択する。

   ![容量計画](./media/site-recovery-hyper-v-site-to-azure/gs-capacity-planning.png)

[ネットワーク帯域幅の制御](#network-bandwidth-considerations)について



## <a name="enable-replication"></a>Enable replication

開始する前に、Azure ユーザー アカウントに、新しい仮想マシンを Azure にレプリケートできるようにするために必要な特定の[アクセス許可](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)が付与されていることを確認してください。

以下のようにして、VM のレプリケーションを有効にします。          

1. **[アプリケーションをレプリケートする]** > **[ソース]** の順にクリックします。 レプリケーションを初めてセットアップした後、**[+ レプリケート]** をクリックして、追加のマシンのレプリケーションを有効にできます。

    ![Enable replication](./media/site-recovery-hyper-v-site-to-azure/enable-replication.png)
2. **[ソース]** で、Hyper-V サイトを選択します。 次に、 **[OK]**をクリックします
3. **[ターゲット]** で、コンテナーのサブスクリプションと、フェールオーバー後に Azure で使用するフェールオーバー モデル (クラシックまたは Resource Manager) を選択します。
4. 使用するストレージ アカウントを選択します。 使用するアカウントがない場合は、[作成](#set-up-an-azure-storage-account)できます。 次に、 **[OK]**をクリックします
5. フェールオーバー後に作成された Azure VM が接続する Azure ネットワークとサブネットを選択します。

    - レプリケーションを有効にするすべてのマシンにネットワーク設定を適用するには、**[選択したマシン用に今すぐ構成します。]** を選択します。
    - マシンごとに Azure ネットワークを選択する場合は、**[後で構成する]** を選択します。
    - 使用するネットワークがない場合は、[作成](#set-up-an-azure-network)できます。 該当する場合は、サブネットを選択します。 次に、 **[OK]**をクリックします

   ![Enable replication](./media/site-recovery-hyper-v-site-to-azure/enable-replication11.png)

6. **[仮想マシン]** > **[仮想マシンの選択]** で、レプリケートする各マシンをクリックして選択します。 選択できるのは、レプリケーションを有効にできるマシンのみです。 次に、 **[OK]**をクリックします

    ![Enable replication](./media/site-recovery-hyper-v-site-to-azure/enable-replication5-for-exclude-disk.png)

7. **[プロパティ]**  >  **[プロパティの構成]** で、選択した VM のオペレーティング システムと OS ディスクを選択します。
8. Azure VM の名前 (ターゲット名) が [Azure 仮想マシンの要件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)に準拠していることを確認します。
9. 既定では、VM のすべてのディスクがレプリケーションの対象として選択されていますが、ディスクを消去して除外することもできます。
    - レプリケーションの帯域幅を減らすために、ディスクを除外したほうがよい場合があります。 たとえば、一時的なデータや、マシンまたはアプリを再起動するたびに更新されるデータ (pagefile.sys や Microsoft SQL Server tempdb など) が保存されたディスクをレプリケーションから除外できます。 ディスクをレプリケーションから除外するには、ディスクの選択を解除します。
    - 除外できるのは、ベーシック ディスクだけです。 OS ディスクを除外することはできません。
    - ダイナミック ディスクは除外しないことをお勧めします。 Site Recovery は、ゲスト VM 内の仮想ハード ディスクがベーシック ディスクであるかダイナミック ディスクであるかを識別できません。 依存するダイナミック ボリューム ディスクすべてが除外されていない場合に VM がフェールオーバーし、そのディスクのデータにアクセスできなくなると、保護されたダイナミック ディスクは障害が発生したディスクとして表示されます。
        - レプリケーションが有効になった後で、レプリケーション用のディスクを追加または削除することはできません。 ディスクを追加または除外する場合は、VM の保護を無効にし、再度有効にする必要があります。
        - Azure で手動で作成したディスクはフェールバックされません。 たとえば、3 つのディスクをフェールオーバーし、Azure VM に直接 2 つのディスクを作成した場合、フェールオーバーされた 3 つのディスクだけが Azure から Hyper-V にフェールバックされます。 Hyper-V から Azure へのフェールバックまたはレプリケーションの反転に、手動で作成されたディスクを含めることはできません。
        - アプリケーションが動作するために必要なディスクを除外した場合、Azure へのフェールオーバー後、レプリケートされたアプリケーションを実行できるように、Azure でディスクを手動で作成する必要があります。 別の方法として、Azure Automation を復旧計画に組み込んで、マシンのフェールオーバー時にディスクを作成することもできます。

10. **[OK]** をクリックして変更を保存します。 後で追加のプロパティを設定できます。

     ![Enable replication](./media/site-recovery-hyper-v-site-to-azure/enable-replication6-with-exclude-disk.png)

11. **[レプリケーションの設定]**  >  **[レプリケーション設定の構成]** で、保護対象の VM に適用するレプリケーション ポリシーを選択します。 次に、 **[OK]**をクリックします レプリケーション ポリシーを変更するには、**[レプリケーション ポリシー]**ポリシー名 > **[設定の編集]** の順にクリックします。 適用する変更は、既にレプリケートしているマシンと、新しいマシンに使用されます。


   ![Enable replication](./media/site-recovery-hyper-v-site-to-azure/enable-replication7.png)

**[ジョブ]** > **[Site Recovery ジョブ]** の順にクリックして、**保護の有効化**ジョブの進行状況を追跡できます。 **保護の最終処理** ジョブが実行されると、マシンはフェールオーバーできる状態になります。

### <a name="view-and-manage-vm-properties"></a>VM プロパティを表示して管理する

ソース マシンのプロパティを確認することをお勧めします。

1. **[保護されたアイテム]** で、**[レプリケートされたアイテム]** をクリックし、マシンを選びます。

    ![Enable replication](./media/site-recovery-hyper-v-site-to-azure/test-failover1.png)
2. **[プロパティ]** で、VM のレプリケーションとフェールオーバーの情報を確認できます。

    ![Enable replication](./media/site-recovery-hyper-v-site-to-azure/test-failover2.png)
3. **[コンピューティングとネットワーク]** > **[コンピューティングのプロパティ]** で、Azure VM の名前とターゲットのサイズを指定できます。 必要に応じて、Azure の要件に準拠するように名前を変更します。 Azure VM に割り当てられるターゲット ネットワーク、サブネット、および IP アドレスに関する情報を表示し、変更することもできます。 以下の点に注意してください。

   * ターゲット IP アドレスを設定できます。 アドレスを指定しなかった場合、フェールオーバーされたマシンで DHCP が使用されます。 フェールオーバーで使用できないアドレスが設定された場合、フェールオーバーは失敗します。 テスト フェールオーバー ネットワークのアドレスを利用できる場合、テスト フェールオーバーに同じターゲット IP アドレスを使用できます。
   * ネットワーク アダプターの数は、次に示すように、ターゲット仮想マシンに指定したサイズによって異なります。

     * ソース マシン上のネットワーク アダプターの数が、ターゲット マシンのサイズに許可されているアダプターの数以下の場合、ターゲットのアダプターの数は、ソースと同じになります。
     * ソース仮想マシン用のアダプターの数が、ターゲットのサイズに許可されている数を超える場合は、ターゲットの最大サイズが使用されます。
     * たとえば、ソース マシンに 2 つのネットワーク アダプターがあり、ターゲット マシンのサイズが 4 つをサポートしている場合は、ターゲット マシンのアダプターの数は、2 つになります。 ソース マシンに 2 つのアダプターがあるが、サポートされているターゲット サイズで 1 つしかサポートしていない場合、ターゲット マシンのアダプターの数は 1 つだけになります。     
     * VM に複数のネットワーク アダプターがある場合は、すべて同じネットワークに接続されます。
     * 仮想マシンにネットワーク アダプターが複数ある場合は、一覧で最初に表示されるアダプターが、Azure 仮想マシンの*既定*のネットワーク アダプターとなります。

     ![Enable replication](./media/site-recovery-hyper-v-site-to-azure/test-failover4.png)

4. **[ディスク]** で、レプリケートされる VM のオペレーティング システム ディスクとデータ ディスクを確認できます。

#### <a name="managed-disks"></a>管理ディスク

Azure への移行時に管理ディスクをマシンに接続する場合、**[コンピューティングとネットワーク]** > **[コンピューティングのプロパティ]** で、VM の [管理ディスクを使用] 設定を [はい] に設定することができます。 管理ディスクを使用すると、VM ディスクに関連付けられているストレージ アカウントを管理できるため、Azure IaaS VM のディスク管理が簡素化されます。 [管理ディスクの詳細をご覧ください](https://docs.microsoft.com/en-us/azure/storage/storage-managed-disks-overview)。

   - 管理ディスクは、Azure へのフェールオーバー時にのみ作成され、仮想マシンに接続されます。 保護を有効にする際、オンプレミスのマシンからのデータは引き続きストレージ アカウントにレプリケートされます。
   管理ディスクは、Resource Manager デプロイメント モデルでデプロイされた仮想マシンに対してのみ作成することができます。

  > [!NOTE]
  > Azure からオンプレミス Hyper-V 環境へのフェールバックは、現時点では、管理ディスクを使用するマシンではサポートされていません。 このマシンを Azure に移行する場合にのみ、[管理ディスクを使用] を [はい] に設定してください。

   - [管理ディスクを使用] を [はい] に設定すると、そのリソース グループ内で [管理ディスクを使用] が [はい] に設定されている可用性セットのみ選択できるようになります。 これは、管理ディスクを使用する仮想マシンが、必ず [管理ディスクを使用] プロパティが [はい] に設定された可用性セットの一部になるためです。 フェールオーバー時に管理ディスクを使用するかどうかの判断に基づいて、[管理ディスクを使用] プロパティが設定された可用性セットを作成するようにしてください。 同様に、[管理ディスクを使用] を [いいえ] に設定すると、そのリソース グループ内で [管理ディスクを使用] プロパティが [いいえ] に設定されている可用性セットのみ選択できるようになります。 [管理ディスクと可用性セットの詳細をご覧ください](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set)。

  > [!NOTE]
  > レプリケーションに使用されるストレージ アカウントが、どこかの時点で Storage Service Encryption で暗号化されていた場合、フェールオーバー中の管理ディスクの作成は失敗します。 [管理ディスクを使用] を [いいえ] に設定してフェールオーバーを再試行できるほか、仮想マシンの保護を無効にしてから、Storage Service Encryption が有効になっていないストレージ アカウントで、任意の時点でその仮想マシンを保護することもできます。
  > [Storage Service Encryption と管理ディスクの詳細をご覧ください](https://docs.microsoft.com/en-us/azure/storage/storage-managed-disks-overview#managed-disks-and-encryption)。


## <a name="test-the-deployment"></a>展開をテスト

デプロイをテストするために、単一の仮想マシンに対して、または 1 つ以上の仮想マシンを含む復旧計画に対してテスト フェールオーバーを実行できます。

### <a name="before-you-start"></a>開始する前に

 - フェールオーバー後に RDP を使用して Azure VM に接続する場合は、[接続の準備](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)の詳細情報を確認します。
 - すべてをテストするには、テスト環境に Active Directory と DNS をコピーする必要があります。 [詳細情報](site-recovery-active-directory.md#test-failover-considerations)。

### <a name="run-a-test-failover"></a>テスト フェールオーバーの実行

1. 1 つの仮想マシンをフェールオーバーする場合は、**[レプリケートされたアイテム]** で、その VM をクリックし、**[+ テスト フェールオーバー]** をクリックします。
2. 復旧計画をフェールオーバーする場合は、**[復旧計画]** で、計画を右クリックし、**[テスト フェールオーバー]** をクリックします。 復旧計画を作成する場合は、[こちらの手順に従ってください](site-recovery-create-recovery-plans.md)。
3. **[テスト フェールオーバー]** で、フェールオーバー後に Azure VM が接続する Azure ネットワークを選択します。
4. **[OK]** をクリックすると、フェールオーバーが開始されます。 進行状況を追跡するには、VM をクリックしてそのプロパティを開くか、コンテナー名をクリックし、**[ジョブ]** > **[Site Recovery ジョブ]** の順にクリックして、**[テスト フェールオーバー]** ジョブをクリックします。
5. フェールオーバーの完了後は、Azure Portal の **[仮想マシン]** にレプリカの Azure マシンも表示されるようになります。 VM が適切なサイズであること、適切なネットワークに接続していること、実行されていることを確認する必要があります。
6. フェールオーバー後の接続の準備が完了したら、Azure VM に接続できるようになります。
7. 完了したら、復旧計画の **[Cleanup test failover (テスト フェールオーバーのクリーンアップ)]** をクリックします。 **[メモ]** を使用して、テスト フェールオーバーに関連する観察結果をすべて記録し、保存します。 これで、テスト フェールオーバー中に作成された仮想マシンが削除されます。

詳しくは、[Azure へのテスト フェールオーバー](site-recovery-test-failover-to-azure.md)に関する記事をご覧ください。



## <a name="monitor-the-deployment"></a>デプロイの監視

Site Recovery デプロイメントの構成設定、状態、および正常性を監視します。

1. コンテナー名をクリックして、 **[要点]** ダッシュボードにアクセスします。 このダッシュボードで、Site Recovery ジョブ、レプリケーションの状態、復旧計画、サーバーの状態、およびイベントを追跡できます。  

    ![[要点]](./media/site-recovery-hyper-v-site-to-azure/essentials.png)
2. **[正常性]** タイルで、問題が発生しているサイト サーバーと、Site Recovery によって過去 24 時間以内に生成されたイベントを監視できます。 [要点] ダッシュボードをカスタマイズして、他の Site Recovery コンテナーや Backup コンテナーの状態など、ニーズに最適なタイルとレイアウトを表示できます。
3. **[レプリケートされたアイテム]**、**[復旧計画]**、**[Site Recovery ジョブ]** の各タイルで、レプリケーションの管理と監視を実行できます。 **[ジョブ]** > **[Site Recovery ジョブ]** の順にクリックすると、ジョブの詳細を確認できます。

## <a name="command-line-provider-and-agent-installation"></a>コマンド ラインのプロバイダーとエージェントのインストール

Azure Site Recovery Provider とエージェントは、次のコマンド ラインを使用してインストールすることもできます。 このメソッドを使用すると、Windows Server 2012 R2 の Server Core にプロバイダーをインストールできます。

1. プロバイダーのインストール ファイルと登録キーをフォルダーにダウンロードします。 たとえば、C:\ASR です。
2. 管理者特権のコマンド プロンプトで次のコマンドを実行して、プロバイダーのインストーラーを抽出します。

            C:\Windows\System32> CD C:\ASR
            C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. 次のコマンドを実行して、コンポーネントをインストールします。

            C:\ASR> setupdr.exe /i
4. 次のコマンドを実行して、コンテナーにサーバーを登録します。

            CD C:\Program Files\Microsoft Azure Site Recovery Provider\
            C:\Program Files\Microsoft Azure Site Recovery Provider\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file>

<br/>
各値の説明:

* **/Credentials**: 登録キー ファイルが配置されている場所を指定する必須パラメーターです。  
* **/Friendlyname**: Azure Site Recovery ポータルに表示される、Hyper-V ホスト サーバーの名前を表す必須パラメーターです。
* **/proxyAddress**: 省略可能。プロキシ サーバーのアドレスを指定します。
* **/proxyport**: 省略可能。プロキシ サーバーのポートを指定します。
* **/proxyUsername**: 省略可能。プロキシのユーザー名を指定します (認証が必要なプロキシの場合)。
* **/proxyPassword**: 省略可能。プロキシ サーバーの認証に使用するパスワードを指定します (認証が必要なプロキシの場合)。


## <a name="network-bandwidth-considerations"></a>ネットワーク帯域幅に関する考慮事項
[Hyper-V Capacity Planner ツール](site-recovery-capacity-planner.md)を使用して、レプリケーション (初期レプリケーションとその後の差分レプリケーション) に必要な帯域幅を計算できます。 レプリケーションの帯域幅の使用量を制御する方法には、いくつかのオプションがあります。

* **帯域幅を調整する**: Azure にレプリケートされる Hyper-V トラフィックは、特定の Hyper-V ホストを経由します。 このホスト サーバーの帯域幅をスロットルできます。
* **帯域幅の微調整**: いくつかのレジストリ キーを使用して、レプリケーションに使用される帯域幅に影響を与えることができます。

### <a name="throttle-bandwidth"></a>帯域幅のスロットル
1. Hyper-V ホスト サーバーの Microsoft Azure Backup MMC スナップインを開きます。 既定では、Microsoft Azure Backup のショートカットが、デスクトップか C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin にあります。
2. スナップインで **[プロパティの変更]**をクリックします。
3. **[調整]** タブで、**[バックアップ操作用のインターネット使用帯域幅の調整を有効にする]** チェック ボックスをオンにし、勤務時間用と勤務時間外用の制限値を設定します。 有効な範囲は、1 秒あたり 512 Kbps ～ 102 Mbps です。

    ![帯域幅を調整する](./media/site-recovery-hyper-v-site-to-azure/throttle2.png)

[Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) コマンドレットを使用して、スロットルを設定することもできます。 サンプルを次に示します。

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** は、スロットルが不要であることを示します。

### <a name="influence-network-bandwidth"></a>ネットワーク帯域幅に影響を与える
1. レジストリで、**HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication** に移動します。
   * レプリケートするディスクで帯域幅のトラフィックに影響を与えるには、 **UploadThreadsPerVM**値を変更するか、このキーが存在しない場合は作成します。
   * Azure からのフェールバックのトラフィックの帯域幅に影響を与えるには、 **DownloadThreadsPerVM**値を変更します。
2. 既定値は 4 ですが、 "プロビジョニング超過" 状態のネットワークの場合、このレジストリ キーを既定値から変更する必要があります。 最大値は 32 です。 トラフィックを監視して値を最適化できます。

## <a name="next-steps"></a>次のステップ

初期レプリケーションが完了し、デプロイをテストしたら、必要に応じてフェールオーバーを呼び出すことができます。 さまざまな種類のフェールオーバーとそれらを実行する方法の[詳細を確認](site-recovery-failover.md)します。

