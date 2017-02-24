---
title: "VMware VM を Azure にレプリケートする | Microsoft Docs"
description: "VMware VM 上で実行されているワークロードを Azure Storage にレプリケートするために必要な手順の概要を示します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: dab98aa5-9c41-4475-b7dc-2e07ab1cfd18
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 002d5651c7848a9c8fa2d049e334639a58bd9f6b
ms.openlocfilehash: 95a0f8b8bb7d9448dc7ce0c9ec786a182c85328b


---
# <a name="replicate-vmware-virtual-machines-to-azure-with-azure-site-recovery"></a>Azure Site Recovery を使用して VMware 仮想マシンを Azure にレプリケートする

> [!div class="op_single_selector"]
> * [Azure ポータル](site-recovery-vmware-to-azure.md)
> * [Azure クラシック](site-recovery-vmware-to-azure-classic.md)


この記事では、オンプレミスの VMware 仮想マシンを、Azure Portal の [Azure Site Recovery](site-recovery-overview.md) サービスを使用して Azure にレプリケートする方法について説明します。

 完全なレプリケーション (レプリケート、フェールオーバー、フェールバック) を行うことなく、単純なフェールオーバーのみで VMware VM を移行する場合は、[この記事](site-recovery-migrate-to-azure.md)を参照してください。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。

## <a name="steps"></a>手順

ここでは、次の手順を実行します。

1. 前提条件と制限事項を確認します。
2. Azure ネットワークおよびストレージ アカウントを設定します。
3. 構成サーバーとしてデプロイするオンプレミス コンピューターを準備します。
4. VM の自動検出に使用する VMware アカウントと、必要に応じてモビリティ サービスのプッシュ インストールに使用する VMware アカウントを準備します。
4. Recovery Services コンテナーを作成します。 このコンテナーには構成設定が含まれ、レプリケーションを調整します。
5. ソース、ターゲット、レプリケーションの設定を指定します。
6. レプリケートする VM 上でモビリティ サービスをデプロイします。
7. VM のレプリケーションを有効にします。
7. テスト フェールオーバーを実行して、すべて想定どおりに動作していることを確認します。

## <a name="prerequisites"></a>前提条件

**サポート要件** | **詳細**
--- | ---
**Azure** | [Azure の要件](site-recovery-prereq.md#azure-requirements)を参照してください。
**オンプレミスの構成サーバー** | Windows Server 2012 R2 以降が実行されている VMware VM が必要です。 このサーバーは、Site Recovery のデプロイの際に設定します。<br/><br/> 既定では、プロセス サーバーとマスター対象サーバーもこの VM にインストールされます。 スケールアップする場合は、別のプロセス サーバーが必要になります。 その場合の要件は構成サーバーと同じです。<br/><br/> これらのコンポーネントについては、[詳細を確認してください](site-recovery-components.md#replicate-vmware-vmsphysical-servers-to-azure)。
**オンプレミスの VMware サーバー** | 1 台以上の VMware vSphere サーバー (6.0、5.5、または 5.1 を実行し、最新の更新プログラムがインストール済みであること)。 サーバーは、構成サーバー (または別のプロセス サーバー) と同じネットワークに配置する必要があります。<br/><br/> vCenter サーバー (6.0 または 5.5 を実行し、最新の更新プログラムがインストール済みであること) を使用してホストを管理することをお勧めします。 バージョン 6.0 をデプロイする場合は、5.5 で利用できる機能のみがサポートされます。
**オンプレミスの VM** | レプリケートする VM では、[サポートされるオペレーティング システム](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)を実行し、[Azure の前提条件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)に準拠する必要があります。 VM では VMware ツールを実行する必要があります。
**URL** | 構成サーバーは以下の URL にアクセスできる必要があります。<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> IP アドレスベースのファイアウォール規則を使用している場合、その規則で Azure との通信が許可されていることを確認します。<br/></br> [Azure データセンターの IP の範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)と HTTPS (443) ポートを許可します。<br/></br> ご利用のサブスクリプションの Azure リージョンと米国西部の IP アドレス範囲を許可します (Access Control と ID 管理に使用されます)。<br/><br/> MySQL をダウンロードするために、http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi を許可します。
**モビリティ サービス** | レプリケートされた VM すべてにインストールします。



## <a name="limitations"></a>制限事項

**制限事項** | **詳細**
--- | ---
**Azure** | ストレージ アカウントとネットワーク アカウントは、コンテナーと同じリージョンに存在する必要があります。<br/><br/> Premium Storage アカウントを使用する場合は、レプリケーション ログを格納するために Standard ストア アカウントも必要になります。<br/><br/> インド中部およびインド南部では Premium アカウントにレプリケートすることはできません。
**オンプレミスの構成サーバー** | VMware VM アダプターの種類は、VMXNET3 である必要があります。 そうでない場合は、[この更新プログラムをインストールしてください](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1)。<br/><br/> vSphere PowerCLI 6.0 をインストールする必要があります。<br/><br> このマシンは、ドメイン コントローラーであってはなりません。また、静的 IP アドレスを使用しないでください。<br/><br/> ホスト名は 15 文字以下で指定し、オペレーティング システムを英語にする必要があります。
**VMware** | vCenter 6.0 では 5.5 の機能のみがサポートされます。 Site Recovery は、クロス vCenter vMotion、仮想ボリューム、ストレージ DRS などの vCenter および vSphere 6.0 の新しい機能をサポートしていません。
**VM** | [Azure VM の制限事項](site-recovery-prereq.md#azure-requirements)を確認してください。<br/><br/> 暗号化されたディスクを使用した VM または UEFI/EFI ブートを使用した VM をレプリケートすることはできません。<br/><br> 共有ディスク クラスターはサポートされていません。 ソース VM で NIC チーミングを使用している場合、フェールオーバー後に単一の NIC に変換されます。<br/><br/> iSCSI ディスクが VM で使用されている場合は、フェールオーバー後に、Site Recovery によって VHD ファイルに変換されます。 Azure VM から iSCSI ターゲットにアクセスできる場合は、Azure VM から iSCSI ターゲットに接続され、iSCSI ターゲットと VHD の両方が確認されます。 このような場合は、iSCSI ターゲットを切断します。<br/><br/> 同じワークロードを実行する VM を整合性データ ポイントに同時に復旧できるようにマルチ VM の整合性を有効にする場合は、VM のポート 20004 を開いてください。<br/><br/> Windows は C ドライブにインストールする必要があります。 OS ディスクは、ダイナミックではなく、ベーシックである必要があります。 データ ディスクはダイナミックでもかまいません。<br/><br/> VM 上の Linux/etc/hosts ファイルには、ローカル ホスト名をすべてのネットワーク アダプターに関連付けられた IP アドレスにマップするエントリが含まれている必要があります。 ホスト名、マウント ポイント、デバイス名、システム パス、およびファイル名 (/etc; /usr) には英語のみを使用できます。<br/><br/> 特定の種類の [Linux ストレージ](site-recovery-support-matrix-to-azure.md#support-for-storage)がサポートされています。<br/><br/>VM の設定で **disk.enableUUID=true** を作成または設定します。 これによって、正常にマウントできるように、一貫性のある UUID が VMDK に提供され、完全なレプリケーションを行うことなく、フェールバック中に差分の変更のみがオンプレミスに転送されて戻されるようになります。


## <a name="set-up-azure"></a>Azure をセットアップする

1. [Azure ネットワークをセットアップ](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)します。

    - Azure VM は、フェールオーバー後に作成されたときに、このネットワークに配置されます。
    - [Resource Manager](../resource-manager-deployment-model.md) またはクラシック モードでネットワークをセットアップすることができます。

2. レプリケートされるデータ用に [Azure ストレージ アカウント](../storage/storage-create-storage-account.md#create-a-storage-account)をセットアップします。

    - このアカウントには、Standard または [Premium](../storage/storage-premium-storage.md) を使用できます。
    - Resource Manager またはクラシック モードでアカウントをセットアップすることができます。

3. VMware VM を Site Recovery が自動的に検出できるように、vCenter サーバーまたは vSphere ホストで[アカウントを準備](#prepare-for-automatic-discovery-and-push-installation)します。

## <a name="prepare-the-configuration-server"></a>構成サーバーを準備

1. VMware VM で Windows Server 2012 R2 以降をインストールします。
2. [前提条件](#prerequisites)に記載されている URL に VM からアクセスできることを確認します。
3. [VMware vSphere PowerCLI 6.0](https://developercenter.vmware.com/tool/vsphere_powercli/6.0) をインストールします。


## <a name="prepare-for-automatic-discovery-and-push-installation"></a>自動検出およびプッシュ インストールを準備する

- **自動検出用のアカウントの準備**: Site Recovery のプロセス サーバーによって VM が自動的に検出されます。 これを行うには、Site Recovery で vCenter サーバー/vSphere ESXi ホストにアクセスできる資格情報が必要になります。

    1. 専用のアカウントを使用するには、(vCenter レベルで、これらの[権限](#vmware-account-permissions)を持つ) ロールを作成します。 名前は **Azure_Site_Recovery** などにします。
    2. 次に、vSphere ホスト/vCenter サーバーにユーザーを作成し、そのユーザーにこのロールを割り当てます。 Site Recovery のデプロイの際に、このユーザー アカウントを指定します。

- **モビリティ サービスをプッシュするアカウントの準備**: モビリティ サービスを VM にプッシュする場合、VM にアクセスするためにプロセス サーバーで使用できるアカウントが必要になります。 このアカウントは、プッシュ インストールのみで使用されます。 ドメイン アカウントまたはローカル アカウントを使用することができます。

    - Windows の場合、ドメイン アカウントを使用していなければ、ローカル マシンでリモート ユーザー アクセス コントロールを無効にする必要があります。 無効にするには、レジスタで、**HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** の下に、値 1 を指定した DWORD エントリの **LocalAccountTokenFilterPolicy** を追加します。
    - CLI から Windows 用のレジストリ エントリを追加する場合は、次のように入力します。  ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
    - Linux の場合、アカウントは、ソース Linux サーバーの root ユーザーである必要があります。




## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する
1. サインインして [[Azure Portal]](https://portal.azure.com) > **[Site Recovery]** の順に移動します。
2. **[新規]** > **[管理]** > の順にクリックします。
3. **[名前]**に、コンテナーを識別するフレンドリ名を入力します。 複数のサブスクリプションがある場合は、いずれかを選択します。
4. [リソース グループを作成](../azure-resource-manager/resource-group-template-deploy-portal.md)するか、既存のリソース グループを選択します。 Azure リージョンを指定します。 サポートされているリージョンを確認するには、[Azure Site Recovery の価格の詳細](https://azure.microsoft.com/pricing/details/site-recovery/)に関するページで利用可能地域をご覧ください。
5. ダッシュボードからコンテナーにすばやくアクセスするには、**[ダッシュボードにピン留めする]** をクリックしてから **[作成]** をクリックします。

    ![新しいコンテナー](./media/site-recovery-vmware-to-azure/new-vault-settings.png)

新しいコンテナーは、**[ダッシュボード]** > **[すべてのリソース]** と、メインの **[Recovery Services コンテナー]** ブレードに表示されます。


## <a name="select-the-protection-goal"></a>保護の目標を選択する

レプリケートの対象とレプリケート先を選択します。

1. **[Recovery Services コンテナー]** > [コンテナー] の順にクリックします。
2. リソース メニューで、**[Site Recovery]** > **[手順 1: インフラストラクチャを準備する]** > **[保護の目標]** の順にクリックします。

    ![Choose goals](./media/site-recovery-vmware-to-azure/choose-goals.png)
3. **[保護の目標]** で、**[To Azure] (Azure へ)** を選択し、**[Yes, with VMware vSphere Hypervisor] (はい、VMware vSphere ハイパーバイザーを使用する)** を選択します。

    ![Choose goals](./media/site-recovery-vmware-to-azure/choose-goals2.png)

## <a name="set-up-the-source-environment"></a>ソース環境をセットアップする

構成サーバーをセットアップし、コンテナーに登録して、VM を検出します。

1. **[Site Recovery]** > **[手順 1: インフラストラクチャを準備する]** > **[ソース]** の順にクリックします。
2. 構成サーバーがない場合は **[+ 構成サーバー]** をクリックします。

    ![Set up source](./media/site-recovery-vmware-to-azure/set-source1.png)
3. **[サーバーの追加]** で、**[サーバーの種類]** に **[構成サーバー]** が表示されていることを確認します。
4. Site Recovery 統合セットアップ インストール ファイルをダウンロードします。
5. コンテナー登録キーをダウンロードします。 このキーは、統合セットアップを実行するときに必要になります。 キーは生成後&5; 日間有効です。

   ![Set up source](./media/site-recovery-vmware-to-azure/set-source2.png)
6. 構成サーバーの VM でシステム クロックが[タイム サーバー](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)と同期されていることを確認し、統合セットアップを実行して、構成サーバー、プロセス サーバー、およびマスター対象サーバーをインストールします。

## <a name="run-site-recovery-unified-setup"></a>Site Recovery 統合セットアップを実行する

開始する前に次の操作を実行してください。

- VM の時刻がご使用のローカル タイム ゾーンの時刻と同じであることを確認します。 これは一致している必要があります。 15 分進んでいるか遅れている場合は、セットアップが失敗する可能性があります。
- 構成サーバーの VM のローカル管理者としてセットアップを実行します。
- VM で TLS 1.0 が有効になっていることを確認します。

構成サーバーで統合セットアップのインストール ファイルを実行します。

1. [統合セットアップ] > **[開始する前に]** で **[構成サーバーとプロセス サーバーをインストールする]** を選択します。

   ![開始する前に](./media/site-recovery-vmware-to-azure/combined-wiz1.png)
2. **[Third-Party Software License (サードパーティ製ソフトウェア ライセンス)]** で、**[I Accept the third party license agreement (サード パーティの使用許諾契約書に同意する)]** をクリックして MySQL をダウンロードし、インストールします。

    ![サード パーティ製ソフトウェア](./media/site-recovery-vmware-to-azure/combined-wiz105.PNG)
3. **[登録]** で、コンテナーからダウンロードした登録キーを参照して選択します。

    ![登録](./media/site-recovery-vmware-to-azure/combined-wiz3.png)
4. **[インターネット設定]** で、構成サーバーで実行されているプロバイダーがインターネット (443) 経由で Site Recovery に接続する方法を指定します。

   * プロバイダーから直接接続するように指定する場合は、**[プロキシを使用せずに直接 Azure Site Recovery に接続する]** を選択します。
   * プロキシを使用する場合は、**[プロキシ サーバーを使用して Azure Site Recovery に接続する]** を選択し、設定を指定します。

     ![ファイアウォール](./media/site-recovery-vmware-to-azure/combined-wiz4.png)
5. **[前提条件の確認]** で、セットアップによって設定が確認されます。  時刻の警告が表示されたら、**[日付と時刻]** の設定内の時刻がタイム ゾーンと同じであることを確認します。

    ![前提条件](./media/site-recovery-vmware-to-azure/combined-wiz5.png)
6. **[MySQL Configuration (MySQL の構成)]** で、インストールする MySQL サーバー インスタンスへのログオンに使用する資格情報を指定します。

    ![MySQL](./media/site-recovery-vmware-to-azure/combined-wiz6.png)
7. **[環境の詳細]** で、VMware VM をレプリケートするかどうかを選択します。 レプリケートする場合、PowerCLI 6.0 がインストールされているかどうかがチェックされます。

    ![MySQL](./media/site-recovery-vmware-to-azure/combined-wiz7.png)
8. **[インストール場所]** で、バイナリをインストールしキャッシュを格納する場所を選択します。 使用可能な記憶域が 5 GB ある任意のドライブを選択できますが、600 GB 以上の空き領域があるキャッシュ ドライブを使用することをお勧めします。

    ![インストール場所](./media/site-recovery-vmware-to-azure/combined-wiz8.png)
9. **[ネットワークの選択]** で、構成サーバーがレプリケーション データを送受信するリスナー (ネットワーク アダプターと SSL ポート) を指定します。 既定のポート (9443) は変更できます。 このポートに加えて、ポート 443 がレプリケーションを調整するために使用されます。 レプリケーション トラフィック用に 443 を使用しないでください。

    ![[ネットワークの選択]](./media/site-recovery-vmware-to-azure/combined-wiz9.png)


10. **[概要]** で情報を確認し、**[インストール]** をクリックします。 インストールが完了すると、パスフレーズが生成されます。 このパスフレーズはレプリケーションを有効にするときに必要になるので、コピーしてセキュリティで保護された場所に保管してください。 登録が完了すると、コンテナーの **[サーバー]** に、サーバーが表示されます。

   ![概要](./media/site-recovery-vmware-to-azure/combined-wiz10.png)




### <a name="add-the-account-for-automatic-discovery"></a>自動検出用のアカウントを追加する

 VMware VM の自動検出用に作成したアカウントを追加します。

1. 構成サーバーで、**CSPSConfigtool.exe** を実行します。 これはデスクトップのショートカットとして、または **[インストール場所]\home\svsystems\bin** フォルダー内で使用できます。
2. **[アカウントの管理]** > **[アカウントの追加]** の順にクリックします。

    ![[アカウントの追加]](./media/site-recovery-vmware-to-azure/credentials1.png)
3. **[アカウントの詳細]** で、自動検出に使用するアカウントを追加します。 アカウント名がポータルに表示されるまでに 15 分以上かかることがあります。 すぐに更新するには、**[構成サーバー]**、サーバー名、**[サーバーを最新の情報に更新する]** の順にクリックします。

    ![詳細](./media/site-recovery-vmware-to-azure/credentials2.png)

### <a name="connect-to-vmware-servers"></a>VMware サーバーに接続する

VMware VM を検出するには、vSphere ESXi ホストまたは vCenter サーバーに接続します。

- サーバーで管理者特権のないアカウントを使用して vCenter サーバーまたは vSphere ホストを追加する場合は、そのアカウントで次の特権が有効になっている必要があります。
    - データセンター、データストア、フォルダー、ホスト、ネットワーク、リソース、仮想マシン、vSphere Distributed Switch。
    - vCenter サーバーは Storage ビューの権限が必要です。
- VMware サーバーを追加するとき、VMware サーバーがポータルに表示されるまで 15 分以上かかることがあります。


1. 構成サーバーが vSphere ホストと vCenter サーバーにネットワーク アクセスできることを確認します。
2. **[インフラストラクチャの準備]** > **[ソース]** の順にクリックします。 **[ソースの準備]** で、構成サーバーを選択します。 **[+ vCenter]** をクリックして vSphere ホストまたは vCenter サーバーを追加します。
3. **[vCenter の追加]** で、サーバーのフレンドリ名、IP アドレスまたは FQDN を指定します。 別のポートで要求をリッスンするように VMware サーバーが構成されている場合を除き、ポートは 443 の設定のままにしておきます。 次に、自動検出用に作成したアカウントを選択し、**[OK]** をクリックします。

    ![VMware](./media/site-recovery-vmware-to-azure/vmware-server.png)

Site Recovery は指定された設定を使用して VMware サーバーに接続し、VM を検出します。

## <a name="set-up-the-target"></a>ターゲットをセットアップする


ターゲット環境をセットアップする前に、[Azure ストレージ アカウントとネットワーク](#set-up-azure)が存在することを確認します。

1. **[インフラストラクチャの準備]** > **[ターゲット]** の順にクリックし、使用する Azure サブスクリプションを選択します。
2. ターゲット デプロイ モデルを Resource Manager ベースとクラシック モードのどちらにするかを指定します。
3. Site Recovery によって、互換性のある Azure ストレージ アカウントとネットワークが&1; つ以上あるかどうかが確認されます。

   ![[ターゲット]](./media/site-recovery-vmware-to-azure/gs-target.png)
4. ストレージ アカウントまたはネットワークを作成していない場合は、**[+ ストレージ アカウント]** または **[+ ネットワーク]** をクリックして、Resource Manager アカウントまたはネットワーク インラインを作成します。

## <a name="set-up-replication-settings"></a>レプリケーション設定をセットアップする

1. 新しいレプリケーション ポリシーを作成するには、**[Site Recovery インフラストラクチャ]** > **[レプリケーション ポリシー]** > **[+ レプリケーション ポリシー]** の順にクリックします。
2. **[レプリケーション ポリシーの作成]** で、ポリシー名を指定します。
3. **[RPO しきい値]** で、RPO の制限を指定します。 この値で、データの復旧ポイントを作成する頻度を指定します。 継続的なレプリケーションがこの制限を超えると、アラートが生成されます。
4. **[復旧ポイントの保持期間]** で、各復旧ポイントのリテンション期間の長さ (時間単位) を指定します。 レプリケートされた VM は、期間内の任意の時点に復旧できます。 Premium Storage にレプリケートされたマシンでは最大 24 時間のリテンション期間がサポートされ、Standard Storage の場合は 72 時間です。
5. **[アプリ整合性スナップショットの頻度]**で、アプリケーション整合性スナップショットを含む復旧ポイントの作成頻度 (分単位) を指定します。 **[OK]** をクリックしてポリシーを作成します。

    ![Replication policy](./media/site-recovery-vmware-to-azure/gs-replication2.png)
8. 新しいポリシーを作成すると、自動的に構成サーバーに関連付けられます。 既定でフェールバックの照合ポリシーが自動的に作成されます。 たとえば、レプリケーション ポリシーが **rep-policy** の場合、フェールバック ポリシーは **rep-policy-failback** になります。 このポリシーは、Azure からフェールバックを開始するまで使用されません。  


## <a name="plan-capacity"></a>容量を計画する

1. 基本的なインフラストラクチャをセットアップできたので、キャパシティ プランニングを立案し、追加のリソースが必要かどうかを検討できます。 [詳細情報](site-recovery-plan-capacity-vmware.md)。
2. 容量計画の作業が完了したら、**[容量計画は完了していますか?]** で **[はい]** を選択します。

   ![容量計画](./media/site-recovery-vmware-to-azure/gs-capacity-planning.png)


## <a name="prepare-vms-for-replication"></a>レプリケーション用の VM を準備する

レプリケートするすべてのマシンにモビリティ サービスをインストールする必要があります。 モビリティ サービスはさまざまな方法でインストールできます。

1. プロセス サーバーからのプッシュ インストールを使用してインストールします。 この方法を使用するには VM を準備する必要があります。
2. System Center Configuration Manager、または Azure Automation DSC などのデプロイ ツールを使用してインストールします。
3.  手動でインストールします。

[詳細情報](site-recovery-vmware-to-azure-install-mob-svc.md)


## <a name="enable-replication"></a>Enable replication

開始する前に次の操作を実行してください。

- VM を追加または変更するときに、変更が有効になってポータルに表示されるまでに 15 分以上かかることがあります。
- VM の最終検出時刻は、**[構成サーバー]** > **[前回のアクセス]** で確認できます。
- 定期検出を待たずに VM を追加するには、構成サーバーを強調表示し (クリックしないでください)、**[更新]** をクリックします。
* VM をプッシュ インストール用に準備した場合は、レプリケーションを有効にすると、プロセス サーバーでモビリティ サービスが自動的にインストールされます。


### <a name="exclude-disks-from-replication"></a>レプリケーションからディスクを除外する

既定では、マシンのすべてのディスクがレプリケートされます。 レプリケーションからディスクを除外できます。 たとえば、一時的なデータや、マシンまたはアプリケーションを再起動するたびに更新されるデータ (pagefile.sys や SQL Server tempdb など) が保存されたディスクをレプリケートから除外できます。

### <a name="replicate-vms"></a>VM をレプリケートする

1. **[手順 2: アプリケーションをレプリケートする]** > **[ソース]** の順にクリックします。
2. **[ソース]** で、構成サーバーを選択します。
3. **[マシンの種類]** で、**[仮想マシン]** を選択します。
4. **[vCenter/vSphere Hypervisor] (vCenter/vSphere ハイパーバイザー)** で、vSphere ホストを管理する vCenter サーバーを選択するか、ホストを選択します。
5. プロセス サーバーを選択します。 追加のプロセス サーバーを作成していない場合、これは構成サーバーになります。 次に、 **[OK]**をクリックします

    ![Enable replication](./media/site-recovery-vmware-to-azure/enable-replication2.png)

6. **[ターゲット]** で、サブスクリプションと、フェールオーバー対象の VM を作成するリソース グループを選択します。 Azure で、フェールオーバー対象の VM に使用するデプロイ モデル (クラシックまたはリソース管理) を選択します。


7. データのレプリケーションに使用する Azure Storage アカウントを選択します。 既にセットアップしたアカウントを使用しない場合は、新しいアカウントを作成できます。

8. フェールオーバー後に作成された Azure VM が接続する Azure ネットワークとサブネットを選択します。 保護の対象として選択したすべてのマシンにネットワーク設定を適用する場合は、**[選択したマシン用に今すぐ構成します。]** を選択します。 マシンごとに Azure ネットワークを選択する場合は、**[後で構成する]** を選択します。 既存のネットワークを使用しない場合は、ネットワークを作成することができます。

    ![Enable replication](./media/site-recovery-vmware-to-azure/enable-rep3.png)
9. **[仮想マシン]** > **[仮想マシンの選択]** で、レプリケートする各マシンをクリックして選択します。 選択できるのは、レプリケーションを有効にできるマシンのみです。 次に、 **[OK]**をクリックします

    ![Enable replication](./media/site-recovery-vmware-to-azure/enable-replication5.png)
10. **[プロパティ]** >  の **[プロパティの構成]** で、モビリティ サービスをマシンに自動的にインストールするためにプロセス サーバーが使用するアカウントを選択します。
11. 既定では、すべてのディスクがレプリケートされます。 **[すべてのディスク]** をクリックし、レプリケートしないディスクをオフにします。 次に、 **[OK]**をクリックします 後で追加の VM プロパティを設定できます。

    ![Enable replication](./media/site-recovery-vmware-to-azure/enable-replication6.png)
11. **[レプリケーションの設定]** > **[レプリケーション設定の構成]** で、正しいレプリケーション ポリシーが選択されていることを確認します。 ポリシーを変更する場合、変更はレプリケートしているマシンと新しいマシンに適用されます。
12. マシンをレプリケーション グループにまとめる場合は、**[マルチ VM 整合性]** を有効にし、グループの名前を指定します。 次に、 **[OK]**をクリックします 以下の点に注意してください。

    * レプリケーション グループのマシンはまとめてレプリケートされ、フェールオーバー時にクラッシュ整合性復旧ポイントとアプリ整合性復旧ポイントを共有します。
    * VM と物理サーバーがワークロードをミラー化できるように、これらをまとめることをお勧めします。 マルチ VM 整合性を有効にすると、ワークロードのパフォーマンスに影響を及ぼす可能性があるので、複数のマシンが同じワークロードを実行しており、整合性が必要な場合にのみ使用します。

    ![Enable replication](./media/site-recovery-vmware-to-azure/enable-replication7.png)
13. **[レプリケーションを有効にする]**をクリックします。 **[設定]** > **[ジョブ]** > **[Site Recovery ジョブ]** の順にクリックして、**保護の有効化**ジョブの進行状況を追跡できます。 **保護の最終処理** ジョブが実行されると、マシンはフェールオーバーできる状態になります。

レプリケーションを有効にした後、プッシュ インストールを設定している場合はモビリティ サービスがインストールされます。 モビリティ サービスが VM でプッシュ インストールされると、保護ジョブが開始されて失敗します。 ジョブの失敗後、各マシンを手動で再起動する必要があります。 その後、保護ジョブが再び開始され、最初のレプリケーションが実行されます。


### <a name="view-and-manage-vm-properties"></a>VM プロパティを表示して管理する

VM プロパティを確認し、必要な変更を加えることをお勧めします。

1. **[レプリケートされたアイテム]** をクリックし、マシンを選択します。 **[要点]** ブレードにマシンの設定と状態に関する情報が表示されます。
1. **[プロパティ]** で、VM のレプリケーションとフェールオーバーの情報を確認できます。

    ![Enable replication](./media/site-recovery-vmware-to-azure/test-failover2.png)
1. **[コンピューティングとネットワーク]** > **[コンピューティングのプロパティ]** で、Azure VM の名前とターゲットのサイズを指定できます。 必要に応じて、 [Azure の要件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) に準拠するように名前を変更します。
2. Azure VM に割り当てられるターゲット ネットワーク、サブネット、および IP アドレスの設定を変更することができます。

   - ターゲット IP アドレスを設定できます。

    - アドレスを指定しなかった場合、フェールオーバーされたマシンで DHCP が使用されます。
    - フェールオーバーで使用できないアドレスを設定した場合、フェールオーバーは機能しません。
    - テスト フェールオーバー ネットワークのアドレスを利用できる場合、テスト フェールオーバーに同じターゲット IP アドレスを使用できます。

   - ネットワーク アダプターの数は、ターゲット仮想マシンに指定したサイズによって異なります。

     - ソース マシン上のネットワーク アダプターの数が、ターゲット マシンのサイズに許可されているアダプターの数以下の場合、ターゲットのアダプターの数は、ソースと同じになります。
     - ソース仮想マシン用のアダプターの数が、ターゲットのサイズに許可されている数を超える場合は、ターゲットの最大サイズが使用されます。
     - たとえば、ソース マシンに&2; つのネットワーク アダプターがあり、ターゲット マシンのサイズが&4; つをサポートしている場合は、ターゲット マシンのアダプターの数は、2 つになります。 ソース マシンに&2; つのアダプターがあるが、サポートされているターゲット サイズで&1; つしかサポートしていない場合、ターゲット マシンのアダプターの数は&1; つだけになります。     
   - 仮想マシンにネットワーク アダプターが複数ある場合、これらのアダプターはすべて同じネットワークに接続されます。
   - 仮想マシンにネットワーク アダプターが複数ある場合は、一覧で最初に表示されるアダプターが、Azure 仮想マシンの*既定*のネットワーク アダプターとなります。

     ![Enable replication](./media/site-recovery-vmware-to-azure/test-failover4.png)
1. **[ディスク]** で、VM のオペレーティング システム ディスクと、レプリケートされるデータ ディスクを確認できます。

## <a name="run-a-test-failover"></a>テスト フェールオーバーの実行

すべてのセットアップが完了したら、[テスト フェールオーバーを実行](site-recovery-test-failover-to-azure.md)して、すべて想定どおりに動作していることを確認します。



## <a name="vmware-account-permissions"></a>VMware アカウントのアクセス許可

Site Recovery は、プロセス サーバーで VM を自動検出するためと VM のフェールオーバーおよびフェールバックのために、VMware にアクセスできる必要があります。

- **移行**: フェールバックせずに VMware VM を Azure に移行するだけの場合は、読み取り専用のロールを持つ VMware アカウントを使用することができます。 このようなロールでフェールオーバーを実行できますが、保護されたソース マシンをシャットダウンすることはできません。 これは移行では必要ありません。
- **レプリケート/回復**: 完全なレプリケーション (レプリケーション、フェールオーバー、フェールバック) をデプロイする場合は、そのアカウントでディスクの作成および削除や VM の電源投入などの操作を実行できる必要があります。
- **自動検出**: 少なくとも読み取り専用のアカウントが必要です。


**タスク** | **必要なアカウントとロール** | **アクセス許可** | **詳細**
--- | --- | --- | ---
**プロセス サーバーによる VMware VM の自動検出** | 少なくとも読み取り専用ユーザーが必要です。 | データ センター オブジェクト -> 子オブジェクトへのプロパゲート、ロール=読み取り専用 | ユーザーはデータセンター レベルで割り当てられ、データセンター内のすべてのオブジェクトに対してアクセス権を持ちます。<br/><br/> アクセスを制限するには、**子オブジェクトへの伝達**特権を持つ**アクセスなし**ロールを子オブジェクト (vSphere ホスト、データストア、VM、ネットワーク) に割り当てます。
**フェールオーバー** | 少なくとも読み取り専用ユーザーが必要です。 | データ センター オブジェクト -> 子オブジェクトへのプロパゲート、ロール=読み取り専用 | ユーザーはデータセンター レベルで割り当てられ、データセンター内のすべてのオブジェクトに対してアクセス権を持ちます。<br/><br/> アクセスを制限するには、**子オブジェクトへの伝達**特権を持つ**アクセスなし**ロールを子オブジェクト (vSphere ホスト、データストア、VM、ネットワーク) に割り当てます。<br/><br/> 移行の目的では役立ちますが、完全なレプリケーション、フェールオーバー、フェールバックでは役立ちません。
**フェールオーバーとフェールバック** | 必要なアクセス許可を備えた ロール (Azure_Site_Recovery) を作成し、このロールを VMware のユーザーまたはグループに割り当てることをお勧めします。 | データ センター オブジェクト –> 子オブジェクトへの伝達、ロール = Azure_Site_Recovery<br/><br/> データストア -> 空間の割り当て、データストアの参照、ファイルの低レベルの操作、ファイルの削除、仮想マシン ファイルの更新<br/><br/> ネットワーク -> ネットワークの割り当て<br/><br/> リソース -> リソース プールへの VM の割り当て、電源がオフの VM の移行、電源がオンの VM の移行<br/><br/> タスク -> タスクの作成、タスクの更新<br/><br/> 仮想マシン -> 構成<br/><br/> 仮想マシン -> 対話 -> 質問への回答、デバイス接続、CD メディアの構成、フロッピー メディアの構成、電源オフ、電源オン、VMware ツールのインストール<br/><br/> 仮想マシン -> インベントリ -> 作成、登録、登録解除<br/><br/> 仮想マシン -> プロビジョニング -> 仮想マシンのダウンロードの許可、仮想マシン ファイルのアップロードの許可<br/><br/> 仮想マシン -> スナップショット -> スナップショットの削除 | ユーザーはデータセンター レベルで割り当てられ、データセンター内のすべてのオブジェクトに対してアクセス権を持ちます。<br/><br/> アクセスを制限するには、**子オブジェクトへの伝達**特権を持つ**アクセスなし**ロールを子オブジェクト (vSphere ホスト、データストア、VM、ネットワーク) に割り当てます。


## <a name="next-steps"></a>次のステップ

レプリケーションの準備を完了して実行した後に障害が発生したら、Azure にフェールオーバーし、レプリケートされたデータから Azure VM が作成されます。 通常の運用に戻るときは、プライマリ ロケーションにフェールバックするまで、Azure でワークロードとアプリにアクセスできます。

- さまざまな種類のフェールオーバーとそれらを実行する方法の[詳細を確認](site-recovery-failover.md)します。
- レプリケートやフェールバックではなく、マシンを移行する場合は、[詳細](site-recovery-migrate-to-azure.md#migrate-on-premises-vms-and-physical-servers)を確認してください。
- [フェールバックの詳細を確認して](site-recovery-failback-azure-to-vmware.md)、Azure からの Azure VM のフェールバックおよびレプリケーションと、オンプレミスのプライマリ サイトへフェールバックを実行します。

## <a name="third-party-software-notices-and-information"></a>サード パーティ製ソフトウェアに関する通知および情報
Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”).  Microsoft is the not original author of the Third Party Code.  The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only.  This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.  

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.



<!--HONumber=Feb17_HO3-->


