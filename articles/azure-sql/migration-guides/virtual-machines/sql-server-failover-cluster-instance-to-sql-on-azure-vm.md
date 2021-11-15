---
title: フェールオーバー クラスター インスタンスの移行
description: Azure Migrate を使用して、Always On フェールオーバー クラスター インスタンスの高可用性ソリューションを Azure VM 上の SQL Server にリフト アンド シフトする方法について説明します。
ms.service: virtual-machines-sql
ms.subservice: migration-guide
author: rahugup
manager: bsiva
ms.topic: how-to
ms.date: 4/25/2021
ms.author: rahugup
ms.openlocfilehash: a1ae8cd56d705df0234425488029ca7073c6dfea
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347710"
---
# <a name="migrate-failover-cluster-instance-to-sql-server-on-azure-vms"></a>フェールオーバー クラスター インスタンスを Azure VM 上の SQL Server に移行する

この記事では、[Azure Migrate: Server Migration ツール](../../../migrate/migrate-services-overview.md#azure-migrate-server-migration-tool)を使用して、Always On フェールオーバー クラスター インスタンスを Azure VM 上の SQL Server に移行する方法について説明します。 この移行ツールを使用すると、フェールオーバー クラスター インスタンス内の各ノードだけでなく、クラスターや FCI メタデータも、SQL Server をホストする Azure VM に移行できます。  

この記事では、次のことについて説明します。 

> [!div class="checklist"]
> * Azure とソース環境を移行に向けて準備します。
> * VM のレプリケートを開始します。
> * レプリケーションを監視します。
> * 完全な VM 移行を実行します。
> * Azure 共有ディスクを使用して SQL フェールオーバー クラスターを再構成します。


このガイドでは、任意のサーバーまたは仮想マシンを物理サーバーとして扱う Azure Migrate のエージェントベースの移行アプローチを使用します。 物理マシンを移行するとき、Azure Migrate: Server Migration では、Azure Site Recovery サービスのエージェントベースのディザスター リカバリーと同じレプリケーション アーキテクチャが使用されており、一部のコンポーネントでは、同じコード ベースが共有されています。 一部のコンテンツは、Site Recovery のドキュメントにリンクされている場合があります。


## <a name="prerequisites"></a>前提条件

このチュートリアルを始める前に、次の準備が必要です。

1. Azure サブスクリプション。 必要に応じて、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)を作成します。 
1. [Azure PowerShell `Az` モジュール](/powershell/azure/install-az-ps)をインストールします。 
1. GitHub リポジトリから [PowerShell サンプル スクリプト](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/SQL%20Migration)をダウンロードします。

## <a name="prepare-azure"></a>Azure を準備する

Server Migration を使用した移行に向けて Azure を準備します。

**タスク** | **詳細**
--- | ---
**Azure Migrate プロジェクトの作成** | Azure アカウントには、[新規プロジェクトを作成する](../../../migrate/create-manage-projects.md)ために共同作成者または所有者のアクセス許可が必要です。
**Azure アカウントのアクセス許可の確認** | Azure アカウントでは、Key Vault を作成し、VM を作成し、Azure マネージド ディスクに書き込むために、Azure サブスクリプションに対する共同作成者または所有者のアクセス許可、Azure Active Directory (AAD) アプリを登録するためのアクセス許可、Azure サブスクリプションに対するユーザー アクセス管理者のアクセス許可が必要になります。
**Azure Virtual Network の設定** | Azure Virtual Network (VNet) を[設定](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network)します。 Azure にレプリケートすると、Azure VM が作成され、移行の設定時に指定した Azure VNet に参加させられます。


適切なアクセス許可を持っているか確認するには、次の手順に従います。 

1. Azure portal でサブスクリプションを開き、 **[アクセス制御 (IAM)]** を選択します。
2. **[アクセスの確認]** で関連するアカウントを探し、それを選択してアクセス許可を表示します。
3. **共同作成者** または **所有者** のアクセス許可を持っている必要があります。
    - 無料の Azure アカウントを作成したばかりであれば、自分のサブスクリプションの所有者になっています。
    - サブスクリプションの所有者でない場合は、所有者と協力してロールを割り当てます。

アクセス許可を割り当てる必要がある場合は、「[Azure ユーザー アカウントを準備する](../../../migrate/tutorial-discover-vmware.md#prepare-an-azure-user-account)」の手順に従います。


## <a name="prepare-for-migration"></a>移行を準備する

サーバーの移行を準備するには、サーバー設定を確認し、レプリケーション アプライアンスをデプロイする準備を行う必要があります。

### <a name="check-machine-requirements"></a>マシンの要件を確認する

マシンが Azure に移行するための要件を満たしていることを確認します。 

1. サーバーの要件を[確認](../../../migrate/migrate-support-matrix-physical-migration.md#physical-server-requirements)します。
2. Azure にレプリケートするソース マシンが、[Azure VM の要件](../../../migrate/migrate-support-matrix-physical-migration.md#azure-vm-requirements)に準拠していることを確認します。
1. 一部の [Windows](../../../migrate/prepare-for-migration.md#windows-machines) ソースでは、いくつかの追加の変更が必要です。 これらの変更を行う前にソースを移行すると、VM が Azure で起動しなくなる可能性があります。 一部のオペレーティング システムでは、これらの変更が Azure Migrate によって自動的に行われます。 

### <a name="prepare-for-replication"></a>レプリケーションの準備 

Azure Migrate: Server Migration では、レプリケーション アプライアンスを使用してマシンを Azure にレプリケートします。 レプリケーション アプライアンスは、次のコンポーネントを実行します。 

- **構成サーバー**: 構成サーバーは、オンプレミスと Azure の間の通信を調整し、データのレプリケーションを管理します。
- **プロセス サーバー**:プロセス サーバーはレプリケーション ゲートウェイとして機能します。 レプリケーション データを受信し、それをキャッシュ、圧縮、暗号化によって最適化して、Azure のキャッシュ ストレージ アカウントに送信します。 

次のようにして、アプライアンスのデプロイの準備をします。

- レプリケーション アプライアンスをホストするように Windows Server 2016 マシンを作成します。  [マシンの要件](../../../migrate/migrate-replication-appliance.md#appliance-requirements)を確認します。
- レプリケーション アプライアンスでは MySQL が使用されます。 アプライアンスに MySQL をインストールするためのいくつかの[方法](../../../migrate/migrate-replication-appliance.md#mysql-installation)を確認します。
- [パブリック](../../../migrate/migrate-replication-appliance.md#url-access) クラウドおよび[政府機関向け](../../../migrate/migrate-replication-appliance.md#azure-government-url-access)クラウドにアクセスするレプリケーション アプライアンスに必要な Azure URL を確認します。
- レプリケーション アプライアンスの[ポート](../../../migrate/migrate-replication-appliance.md#port-access) アクセス要件を確認します。

> [!NOTE]
> レプリケーション アプライアンスは、レプリケートまたは移行しているソース マシン以外のマシンにインストールする必要があり、Azure Migrate Discovery and Assessment アプライアンスを以前にインストールしたことのあるマシンにはインストールできません。

### <a name="download-replication-appliance-installer"></a>レプリケーション アプライアンス インストーラーをダウンロードする

レプリケーション アプライアンス インストーラーをダウンロードするには、次の手順に従います。 

1. Azure Migrate プロジェクトの **[サーバー]** の **[Azure Migrate: Server Migration]** で、 **[検出]** を選択します。

    ![Discover VMs](../../../migrate/media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

1. **[マシンの検出]**  >  **[マシンは仮想化されていますか?]** で、 **[物理またはその他 (AWS、GCP、Xen など)]** を選択します。
1. **[ターゲット リージョン]** で、マシンの移行先にする Azure リージョンを選択します。
1. **[移行先のリージョンが <リージョン名> であることを確認してください]** を選択します。
1. **[リソースの作成]** を選択します。 これで、Azure Site Recovery コンテナーがバックグラウンドで作成されます。
    - Azure Migrate Server Migration を使用した移行を既に設定してある場合は、リソースが以前に設定されているため、ターゲット オプションを構成できません。    
    - このボタンの選択後は、このプロジェクトのターゲット リージョンを変更することはできません。
    - 後続のすべての移行は、このリージョンに対して行われます。

1. **[新しいレプリケーション アプライアンスをインストールしますか?]** で、 **[レプリケーション アプライアンスのインストール]** を選択します。
1. **[レプリケーション アプライアンス ソフトウェアをダウンロードしてインストールする]** で、アプライアンスのインストーラーと登録キーをダウンロードします。 このキーは、アプライアンスを登録するために必要です。 キーは、ダウンロード後 5 日間有効です。

    ![プロバイダーのダウンロード](../../../migrate/media/tutorial-migrate-physical-virtual-machines/download-provider.png)

1. アプライアンスの設定ファイルとキー ファイルを、アプライアンス用に作成した Windows Server 2016 マシンにコピーします。
1. インストールが完了すると、アプライアンス構成ウィザードが自動的に起動します (アプライアンス マシンのデスクトップに作成された cspsconfigtool ショートカットを使用して、ウィザードを手動で起動することもできます)。 ウィザードの **[アカウントの管理]** タブを使用して、次の詳細を含むダミー アカウントを作成します。

   -  フレンドリ名としての "guest"
   -  ユーザー名としての "username"
   -  アカウントのパスワードとしての "password"。 
   
   このダミー アカウントは、「レプリケーションを有効にする」ステップで使用します。 

1. セットアップが完了しアプライアンスが再起動した後に、 **[マシンの検出]** の **[構成サーバーの選択]** で新しいアプライアンスを選択し、 **[登録の終了処理]** を選択します。 登録の終了処理では、レプリケーション アプライアンスを準備するための終了タスクがいくつか実行されます。

    ![登録の終了処理](../../../migrate/media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)


## <a name="install-the-mobility-service"></a>モビリティ サービスをインストールする 

移行するサーバーにモビリティ サービス エージェントをインストールします。 エージェント インストーラーは、レプリケーション アプライアンス上で使用できます。 適切なインストーラーを見つけて、移行したい各マシンにエージェントをインストールします。 

モビリティ サービスをインストールするには、次の手順に従います。 

1. レプリケーション アプライアンスにサインインします。
2. **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository** に移動します。
3. マシンのオペレーティング システムとバージョンに合ったインストーラーを見つけます。 [サポートされているオペレーティング システム](../../../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines)を確認してください。 
4. 移行したいマシンにインストーラー ファイルをコピーします。
5. アプライアンスをデプロイしたときに生成されたパスフレーズを持っていることを確認します。
    - マシン上の一時テキスト ファイルにファイルを格納します。
    - レプリケーション アプライアンスのパスフレーズを取得できます。 現在のパスフレーズを表示するには、コマンド ラインで **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe -v** を実行します。
    - パスフレーズを再生成しないでください。 そうすると接続が切断され、レプリケーション アプライアンスを再登録しなければならなくなります。
    - */Platform* パラメーターで、VMware マシンと物理マシンの両方に *VMware* を指定します。 

1. マシンに接続し、インストーラー ファイルの内容をローカル フォルダー (c:\temp など) に抽出します。 管理者のコマンド プロンプトでこれを実行します。 

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```

2. モビリティ サービスのインストーラーを実行します。

    ```
    UnifiedAgent.exe /Role "MS" /Platform "VmWare" /Silent
    ```

3. レプリケーション アプライアンスにエージェントを登録します。

    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

インストール後、検出されたマシンが Azure Migrate: Server Migration に表示されるまでに、いくらか時間がかかることがあります。 VM が検出されると、 **[検出済みサーバー]** の数が増えます。

![検出済みサーバー](../../../migrate/media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)

## <a name="prepare-source-machines"></a>ソース マシンを準備する

ソース マシンを準備するには、クラスターからの情報が必要になります。 

> [!CAUTION]
> - 最終的な切り替えが行われるまで、レプリケーション プロセス全体でディスクの所有権を維持します。 ディスクの所有権が変更されると、ボリュームが破損し、レプリケーションを再度実行する必要が生じる可能性があります。 各ディスクの優先所有者を設定して、レプリケーション プロセス中に所有権の移行が発生しないようにします。 
> - ディスクの所有権の移行が発生しないようにするため、レプリケーション プロセス中に、修正プログラムの適用やシステムの再起動は行わないようにします。

ソース マシンを準備するには、次の操作を行います。 

1. **ディスクの所有権を特定する:** いずれかのクラスター ノードにログインし、フェールオーバー クラスター マネージャーを開きます。 ディスクの所有者ノードを特定し、各サーバーで移行する必要があるディスクを判別します。  
2. **クラスター情報を取得する:** クラスター ノードで `Get-ClusterInfo.ps1` スクリプトを実行して、クラスター リソースに関する情報を取得します。 スクリプトによって、ロール名、リソース名、IP、およびプローブ ポートが `Cluster-Config.csv` ファイルに出力されます。 この記事の後の方で、Azure にリソースを作成して割り当てる際に、この CSV ファイルを使用します。  
    
   ```powershell
   ./Get-ClusterInfo.ps1
   ```

## <a name="create-load-balancer"></a>ロード バランサーの作成

クラスターとクラスター ロールが要求に適切に応答するには、Azure ロード バランサーが必要です。 ロード バランサーを使用しない場合、VM は、ネットワークまたはクラスターに所属しているとは認識されないので、クラスター IP アドレスに到達できません。 

1. `Cluster-Config.csv` ファイルの列に入力します。 

    **列ヘッダー** | **説明**
    --- | ---
    NewIP | CSV ファイル内の各リソースの Azure 仮想ネットワーク (またはサブネット) の IP アドレスを指定します。
    ServicePort | CSV ファイル内の各リソースで使用するサービス ポートを指定します。 SQL クラスター リソースの場合は、CSV のプローブ ポートと同じ値をサービス ポートに使用します。 他のクラスター ロールの場合、使用される既定値は 1433 ですが、現在のセットアップで構成されているポート番号を引き続き使用できます。 


1. 次の必須パラメーターを使用して、`Create-ClusterLoadBalancer.ps1` スクリプトを実行し、ロード バランサーを作成します。 

    **パラメーター** | **Type** | **説明**
    --- | --- | ---
    ConfigFilePath | Mandatory |  前の手順で入力した `Cluster-Config.csv` ファイルのパスを指定します。
    ResourceGroupName | Mandatory| ロード バランサーを作成するリソース グループの名前を指定します。 
    VNetName | Mandatory| ロード バランサーが関連付けられる Azure 仮想ネットワークの名前を指定します。 
    SubnetName | Mandatory| ロード バランサーが関連付けられる Azure 仮想ネットワークのサブネットの名前を指定します。 
    VNetResourceGroupName | Mandatory| ロード バランサーが関連付けられる Azure 仮想ネットワークのリソース グループの名前を指定します。 
    場所 | Mandatory| ロード バランサーを作成する場所を指定します。 
    LoadBalancerName | Mandatory| 作成するロード バランサーの名前を指定します。 


    ```powershell
    ./Create-ClusterLoadBalancer.ps1 -ConfigFilePath ./cluster-config.csv -ResourceGroupName $resoucegroupname -VNetName $vnetname -subnetName $subnetname -VnetResourceGroupName $vnetresourcegroupname -Location “eastus” -LoadBalancerName $loadbalancername
    ```

## <a name="replicate-machines"></a>マシンをレプリケートする

今度は、移行の対象となるマシンを選択します。 最大 10 台のマシンをまとめてレプリケートできます。 レプリケートするマシンがそれより多い場合は、10 台をひとまとまりとして同時にレプリケートしてください。

1. Azure Migrate プロジェクトの **[サーバー]** の **[Azure Migrate: Server Migration]** で、 **[レプリケート]** を選択します。

    ![Azure Migrate で選択した [レプリケート] ボタンが表示されている Azure Migrate の [サーバー] 画面のスクリーンショット。移行ツールでのサーバーの移行。](../../../migrate/media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

1. **[レプリケート]** > **[ソース設定]**  >  **[マシンは仮想化されていますか?]** で、 **[物理またはその他 (AWS、GCP、Xen など)]** を選択します。
1. **[オンプレミスのアプライアンス]** で、自分が設定した Azure Migrate アプライアンスの名前を選択します。
1. **[プロセス サーバー]** で、レプリケーション アプライアンスの名前を選択します。
1. **[ゲストの資格情報]** で、「[レプリケーション インストーラーのセットアップ](#download-replication-appliance-installer)」で作成したダミー アカウントを選択します。 その後、 **[次へ: 仮想マシン]** を選択します。

    ![[レプリケート] 画面で [ゲストの資格情報] フィールドが強調表示されている [ソースの設定] タブのスクリーンショット。](../../../migrate/media/tutorial-migrate-physical-virtual-machines/source-settings.png)

1. **[仮想マシン]** の **[評価から移行設定をインポートしますか?]** は、既定の設定である **[いいえ。移行設定を手動で指定します]** のままにしておきます。
1. 移行したい各 VM を確認します。 次に、 **[次のステップ: ターゲット設定]** をクリックします。

    ![VM を選択する](../../../migrate/media/tutorial-migrate-physical-virtual-machines/select-vms.png)


1. **[ターゲット設定]** で、サブスクリプションと、移行先となるターゲット リージョンを選択し、移行後に Azure VM が配置されるリソース グループを指定します。
1. **[仮想ネットワーク]** で、移行後に Azure VM の参加先となる Azure VNet およびサブネットを選択します。
1. **[可用性オプション]** で、以下を選択します。
    -  可用性ゾーン。移行されたマシンをリージョン内の特定の可用性ゾーンにピン留めします。 このオプションを使用して、複数ノードのアプリケーション層を形成するサーバーを可用性ゾーン間で分散させます。 このオプションを選択した場合は、[コンピューティング] タブで選択した各マシンに使用する可用性ゾーンを指定する必要があります。このオプションは、移行用に選択したターゲット リージョンで Availability Zones がサポートされている場合にのみ使用できます。
    -  可用性セット。移行されたマシンを可用性セットに配置します。 このオプションを使用するには、選択されたターゲット リソース グループに 1 つ以上の可用性セットが必要です。
    - [インフラストラクチャ冗長は必要ありません] オプション (移行されたマシンに対してこれらの可用性構成がいずれも不要な場合)。
    
1. **[Disk encryption type]\(ディスク暗号化の種類\)** で、以下を選択します。
    - プラットフォーム マネージド キーを使用した保存時の暗号化
    - カスタマー マネージド キーを使用した保存時の暗号化
    - プラットフォーム マネージド キーとカスタマー マネージド キーを使用した二重暗号化

    > [!NOTE]
    > CMK を使用して VM をレプリケートするには、ターゲット リソース グループに[ディスク暗号化セットを作成する](../../../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set)必要があります。 ディスク暗号化セット オブジェクトによって、SSE に使用する CMK を含む Key Vault にマネージド ディスクがマップされます。
  
1. **[Azure ハイブリッド特典]** で、

    - Azure ハイブリッド特典を適用しない場合は、 **[いいえ]** を選択します。 **[次へ]** を選択します。
    - アクティブなソフトウェア アシュアランスまたは Windows Server のサブスクリプションの対象となっている Windows Server マシンがあり、移行中のマシンにその特典を適用する場合は、 **[はい]** を選択します。 **[次へ]** を選択します。

    :::image type="content" source="../../../migrate/media/tutorial-migrate-vmware/target-settings.png" alt-text="ターゲットの設定":::

1. **[コンピューティング]** で、VM の名前、サイズ、OS ディスクの種類、および可用性構成 (前の手順で選択した場合) を確認します。 VM は [Azure の要件](../../../migrate/migrate-support-matrix-physical-migration.md#azure-vm-requirements)に準拠している必要があります。

    - **VM サイズ**: 評価の推奨事項を使用している場合は、[VM サイズ] ドロップダウンに推奨サイズが表示されます。 それ以外の場合は、Azure Migrate によって、Azure サブスクリプション内の最も近いサイズが選択されます。 または、 **[Azure VM サイズ]** でサイズを手動で選択します。
    - **OS ディスク**:VM の OS (ブート) ディスクを指定します。 OS ディスクは、オペレーティング システムのブートローダーとインストーラーがあるディスクです。
    - **可用性ゾーン**:使用する可用性ゾーンを指定します。
    - **可用性セット**:使用する可用性セットを指定します。

    ![コンピューティングの設定](../../../migrate/media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

1. **[ディスク]** で、VM ディスクを Azure にレプリケートするかどうかを指定し、Azure でのディスクの種類 (Standard SSD か HDD、または Premium マネージド ディスク) を選択します。 **[次へ]** を選択します。
    - 前に作成した一覧を使用して、各サーバーでレプリケートするディスクを選択します。 レプリケーションからその他のディスクを除外します。
   

    ![ディスクの設定](../../../migrate/media/tutorial-migrate-physical-virtual-machines/disks.png)

1. **[レプリケーションの確認と開始]** で設定を確認し、 **[レプリケート]** を選択して、サーバーの初期レプリケーションを開始します。

> [!NOTE]
> レプリケーションが開始される前であれば、 **[管理]**  >  **[マシンのレプリケート]** でレプリケーションの設定をいつでも更新できます。 レプリケーションの開始後は、設定を変更することができません。

## <a name="track-and-monitor"></a>追跡して監視する

レプリケーションは、次の順序で進められます。 

- **[レプリケート]** を選択すると、_レプリケーションの開始_ ジョブが開始されます。 
- _レプリケーションの開始_ ジョブが正常に終了すると、マシンで Azure への初期レプリケーションが開始されます。
- 初期レプリケーションが完了すると、差分レプリケーションが開始されます。 オンプレミスのディスクに対する増分変更は、Azure のレプリカ ディスクに定期的にレプリケートされます。
- 初期レプリケーションが完了したら、各 VM の [コンピューティング] 項目と [ネットワーク] 項目を構成します。 通常、クラスターには複数の NIC がありますが、移行に必要な NIC は 1 つのみです (他の NIC は作成しないように設定します)。

ジョブの状態は、ポータルの通知で追跡できます。

レプリケーションの状態を監視するには、**Azure Migrate: Server Migration** 内で **[レプリケート中のサーバー]** を選択します。
![レプリケーションの監視](../../../migrate/media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)


## <a name="migrate-vms"></a>VM の移行

マシンがレプリケートされると、移行の準備が整います。 サーバーを移行するには、次の手順に従います。 

1. Azure Migrate プロジェクトの **[サーバー]**  >  **[Azure Migrate: Server Migration]** で、 **[サーバーをレプリケートしています]** を選択します。

    ![サーバーをレプリケートしています](../../../migrate/media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

1. 移行するサーバーが移行元サーバーと同期されるようにするには、クラスター ディスクでオンラインの状態を保ちながら、SQL Server リソース ( **[フェールオーバー クラスター マネージャー]**  >  **[ロール]**  >  **[その他のリソース]** ) を停止します。   
1. 次の手順に進む前に、 **[コンピューターのレプリケート]** > サーバー名の選択 > **[概要]** で、最新の同期タイムスタンプが、移行するサーバーで SQL Server サービスを停止した後のタイムスタンプであることを確認します。 わずか数分で完了します。 
1. **[マシンのレプリケート]** で VM を右クリックし、 **[移行]** を選択します。
1. **[移行]**  >  **[仮想マシンをシャットダウンし、データ損失のない計画された移行を実行しますか]** で、 **[いいえ]**  >  **[OK]** の順に選択します。
   
   > [!NOTE]
   > 物理サーバーを移行する場合、ソース マシンのシャットダウンは自動的にはサポートされません。 移行期間の一環としてアプリケーションを停止させ (アプリケーションが接続を受け付けないようにして)、移行を開始することをお勧めします (移行の完了前に残りの変更を同期できるように、サーバーは動作させ続けておく必要があります)。

1. VM に対して移行ジョブが開始されます。 Azure 通知でジョブを追跡します。
1. ジョブが完了したら、 **[仮想マシン]** ページで VM を表示して管理できます。

## <a name="reconfigure-cluster"></a>クラスターを再構成する 

VM を移行し終えたら、クラスターを再構成します。 次の手順に従います。 

1. Azure で移行されたサーバーをシャットダウンします。
1. 移行したマシンをロード バランサーのバックエンド プールに追加します。 **[ロード バランサー]**  >  **[バックエンド プール] に移動** > バックエンド プールを選択 > **移行したマシンを追加** します。 

1. `Create-SharedDisks.ps1` スクリプトを実行して、サーバーの移行するディスクを共有ディスクとして再構成します。 スクリプトは対話型であり、マシンの一覧が表示された後に、抽出可能なディスクが表示されます (データ ディスクのみ)。 共有ディスクに変更するドライブが含まれるマシンを選択するようにというメッセージが表示されます。 選択すると、マシンごとに 1 回、特定のディスクを選択するようにというメッセージが表示されます。 

   **パラメーター** | **Type** | **説明**
   --- | --- | ---
   ResourceGroupName | Mandatory |  移行するサーバーが含まれるリソース グループの名前を指定します。
   NumberofNodes | オプション | フェールオーバー クラスター インスタンス内のノード数を指定します。 このパラメーターは、作成する共有ディスクの適切な SKU を識別するために使用されます。 既定では、このスクリプトは、クラスター内のノード数が 2 であると想定しています。   
   DiskNamePrefix | オプション | 共有ディスクの名前に追加するプレフィックスを指定します。 
   
   ```powershell
   ./Create-SharedDisks.ps1 -ResourceGroupName $resoucegroupname -NumberofNodes $nodesincluster -DiskNamePrefix $disknameprefix 
   ```

1. `Attach-SharedDisks.ps1` スクリプトを実行して、移行するサーバーに共有ディスクを接続します。 

   **パラメーター** | **Type** |**説明**
   --- | ---  | ---
   ResourceGroupName | Mandatory | 移行するサーバーが含まれるリソース グループの名前を指定します。
   StartingLunNumber | オプション |共有ディスクの接続先として使用できる開始 LUN 番号を指定します。 既定では、スクリプトは 0 の LUN の共有ディスクから接続しようとします。  
   
   ```powershell
   ./Attach-ShareDisks.ps1 -ResourceGroupName $resoucegroupname 
   ```

1. Azure で移行したサーバーを起動し、任意のノードにログインします。 

1. `ClusterConfig.csv` ファイルをコピーし、CSV をパラメーターとして渡して `Update-ClusterConfig.ps1` スクリプトを実行します。 これにより、クラスターが Azure で動作するための新しい構成でクラスター リソースが更新されます。 

   ```powershell
   ./Update-ClusterConfig.ps1 -ConfigFilePath $filepath
   ```

SQL Server のフェールオーバー クラスター インスタンスの準備が完了しました。 

## <a name="complete-the-migration"></a>移行を完了する

1. 移行が完了したら、VM を右クリックして、 **[移行を停止する]** を選択します。 次の処理が実行されます。
    - オンプレミス マシンのレプリケーションを停止します。
    - Azure Migrate: Server Migration の **[サーバーをレプリケートしています]** のカウントからマシンを削除します。Server Migration に関するエラーのトラブルシューティングに役立つ情報を提供しています。
    - マシンのレプリケーション状態情報をクリーンアップします。
1. Azure VM の [Windows](../../../virtual-machines/extensions/agent-windows.md) エージェントを、移行するマシンにインストールします。
1. データベース接続文字列、および Web サーバー構成の更新など、移行後のアプリの微調整を実行します。
1. Azure で現在実行されている移行後のアプリケーション上で、最終的なアプリケーションと移行の受け入れのテストを実行します。
1. 移行された Azure VM インスタンスにトラフィックを切り替えます。
1. ローカル VM インベントリからオンプレミスの VM を削除します。
1. ローカル バックアップからオンプレミスの VM を削除します。
1. Azure VM の新しい場所と IP アドレスを示すように内部ドキュメントを更新します。 

## <a name="post-migration-best-practices"></a>移行後のベスト プラクティス

- SQL Server の場合:
    -  [SQL Server IaaS Agent 拡張機能](../../virtual-machines/windows/sql-server-iaas-agent-extension-automate-management.md) をインストールして、管理タスクを自動化します。 
    - Azure VM 上での SQL Server のパフォーマンスを[最適化](../../virtual-machines/windows/performance-guidelines-best-practices-checklist.md)します。 
    - Azure での SQL Server の[価格](../../virtual-machines/windows/pricing-guidance.md#free-licensed-sql-server-editions)を把握します。 
- 復元性の向上:
    - [Azure Backup サービス](../../../backup/quick-backup-vm-portal.md)を使用して、Azure VM をバックアップすることで、データの安全性を保持します。 
    - Azure VM を [Site Recovery](../../../site-recovery/azure-to-azure-tutorial-enable-replication.md) のセカンダリ リージョンにレプリケートし、継続的にワークロードを実行して利用可能にします。
- セキュリティの強化：
    - [Microsoft Defender for Cloud のジャスト イン タイム管理](../../../security-center/security-center-just-in-time.md)を利用し、インバウンド トラフィック アクセスをロックダウンし、制限します。
    - [ネットワーク セキュリティ グループ](../../../virtual-network/network-security-groups-overview.md)を使って、ネットワーク トラフィックを管理エンドポイントに制限します。
    - [Azure Disk Encryption](../../../security/fundamentals/azure-disk-encryption-vms-vmss.md) をデプロイして、ディスクをセキュリティ保護し、盗難や不正アクセスからデータを安全に保護します。
    - [IaaS リソースのセキュリティ保護](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/)に関する詳細を読み、[Microsoft Defender for Cloud](https://azure.microsoft.com/services/security-center/) を確認してください。
- 監視と管理：
    - [Azure Cost Management](../../../cost-management-billing/cost-management-billing-overview.md) をデプロイして、リソースの使用率と消費量を監視します。


## <a name="next-steps"></a>次のステップ

Azure クラウド導入フレームワークでの[クラウド移行の工程](/azure/architecture/cloud-adoption/getting-started/migrate)を調査します。
