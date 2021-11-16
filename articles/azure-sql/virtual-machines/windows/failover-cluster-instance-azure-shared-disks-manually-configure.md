---
title: Azure 共有ディスクを使用して FCI を作成する
description: Azure 共有ディスクを使用して、Azure Virtual Machines 上の SQL Server でフェールオーバー クラスター インスタンス (FCI) を作成します。
services: virtual-machines
documentationCenter: na
author: rajeshsetlem
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.custom: na, devx-track-azurepowershell
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/10/2021
ms.author: rsetlem
ms.reviewer: mathoma
ms.openlocfilehash: 142c84fa2006e6a5a99fc2997d62f79376758339
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158569"
---
# <a name="create-an-fci-with-azure-shared-disks-sql-server-on-azure-vms"></a>Azure 共有ディスクを使用して FCI を作成する (Azure VM 上の SQL Server)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!TIP]
> 同じ Azure 仮想ネットワーク内の[複数のサブネット](failover-cluster-instance-prepare-vm.md#subnets)に SQL Server VM を作成することで、フェールオーバー クラスター インスタンスに対して Azure Load Balancer または分散ネットワーク名 (DNN) が不要になります。

この記事では、Azure 共有ディスクを使用して、Azure Virtual Machines (VM) 上の SQL Server でフェールオーバー クラスター インスタンス (FCI) を作成する方法について説明します。 

詳細については、[Azure VM 上の SQL Server を使用した FCI](failover-cluster-instance-overview.md) および[クラスターのベスト プラクティス](hadr-cluster-best-practices.md)の概要に関する記事をご覧ください。 

> [!NOTE]
> これで Azure Migrate を使用して、フェールオーバー クラスター インスタンス ソリューションを Azure VM 上の SQL Server にリフト アンド シフトできるようになりました。 詳細については、[フェールオーバー クラスター インスタンスの移行](../../migration-guides/virtual-machines/sql-server-failover-cluster-instance-to-sql-on-azure-vm.md)に関するページを参照してください。 

## <a name="prerequisites"></a>前提条件 

この記事に記載されている手順を完了するには、次のものが必要です。

- Azure サブスクリプション。 [無料](https://azure.microsoft.com/free/)で開始できます。 
- 可用性セットまたは可用性ゾーン内の [2 つ以上の準備済みの Windows Azure 仮想マシン](failover-cluster-instance-prepare-vm.md)。 
- Azure の仮想マシンと Active Directory の両方にオブジェクトを作成するためのアクセス許可を持つアカウント。
- 最新バージョンの [Azure PowerShell](/powershell/azure/install-az-ps)。 

## <a name="add-azure-shared-disk"></a>Azure 共有ディスクを追加する

[共有ディスク機能を有効にしてマネージド Premium SSD ディスクをデプロイします](../../../virtual-machines/disks-shared-enable.md#deploy-a-premium-ssd-as-a-shared-disk)。 `maxShares` に設定し、**クラスター ノードの数に合わせる** と、全 FCI ノード間でディスクが共有可能になります。 

## <a name="attach-shared-disk-to-vms"></a>共有ディスクを VM にアタッチする

maxShares > 1 の共有ディスクをデプロイしたら、クラスターのノードとして参加する VM にディスクをマウントできます。 

共有ディスクを SQL Server VM にアタッチするには、次の手順に従います。 

1. Azure portal で、共有ディスクのアタッチ先の VM を選択します。 
1. **[設定]** ペインで **[ディスク]** を選択します。 
1. **[既存のディスクをアタッチする]** を選択して、共有ディスクを VM にアタッチします。 
1. **[ディスク名]** ドロップダウンから共有ディスクを選択します。 
1. **[保存]** を選択します。
1. すべてのクラスター ノードの SQL Server VM に対して、以上の手順を繰り返します。 

しばらくすると、共有データ ディスクが VM にアタッチされ、その VM に関する [データ ディスク] の一覧に表示されます。

## <a name="initialize-shared-disk"></a>共有ディスクを初期化する

すべての VM で共有ディスクがアタッチされたら、クラスターのノードとして参加する VM のディスクを初期化できます。 **すべて** の VM のディスクを初期化します。 


SQL Server VM のディスクを初期化するには、次の手順に従います。 
 
1. VM の 1 つに接続します。
2. VM 内から **[スタート]** メニューを開き、検索ボックスに「**diskmgmt.msc**」と入力して **[ディスクの管理]** コンソールを開きます。
3. 新しい未初期化ディスクがディスクの管理によって認識され、**[ディスクの初期化]** ウィンドウが表示されます。
4. 新しいディスクが選択されていることを確認し、**[OK]** をクリックしてディスクを初期化します。
5. 新しいディスクが、**未割り当て** として表示されます。 ディスクの任意の場所を右クリックし、**[新しいシンプル ボリューム]** を選択します。 **新しいシンプル ボリューム ウィザード** が開きます。
6. すべての設定を既定値のままウィザードを進め、完了したら **[完了]** を選択します。
7. **[ディスクの管理]** を閉じます。
8. ポップアップが表示され、新しいディスクは使用前にフォーマットする必要があることが通知されます。 **[ディスクのフォーマット]** を選択します。
9. **[新しいディスクのフォーマット]** ウィンドウで設定を確認し、**[開始]** を選択します。
10. ディスクをフォーマットするとデータがすべて削除されることを警告するメッセージが表示されます。 **[OK]** を選択します。
11. フォーマットが完了したら、**[OK]** を選択します。
12. FCI に参加する各 SQL Server VM で、この手順を繰り返します。 

## <a name="create-windows-failover-cluster"></a>Windows フェールオーバー クラスターの作成

Windows Server フェールオーバー クラスターを作成する手順は、SQL Server VM を 1 つのサブネットにデプロイしたか、それとも複数のサブネットにデプロイしたかによって異なります。 クラスターを作成するには、[複数のサブネットの場合](availability-group-manually-configure-tutorial-multi-subnet.md#add-failover-cluster-feature)または [1 つのサブネットの場合](availability-group-manually-configure-tutorial-single-subnet.md#create-the-cluster)のチュートリアルの手順に従います。 これらのチュートリアルは可用性グループを作成するためのものですが、クラスターを作成する手順は同じです。 

## <a name="configure-quorum"></a>クォーラムを構成する

ディスク監視は最も回復力のあるクォーラム オプションであり、FCI ソリューションでは Azure 共有ディスクを使用するため、クォーラム ソリューションとしてディスク監視を構成することをお勧めします。 

クラスターに多数の投票がある場合は、ビジネス ニーズに最適な[クォーラム ソリューション](hadr-cluster-quorum-configure-how-to.md)を構成します。 詳細については、[SQL Server VM でのクォーラム](hadr-windows-server-failover-cluster-overview.md#quorum)に関する記事をご覧ください。 

## <a name="validate-cluster"></a>クラスターを検証する

フェールオーバー クラスター マネージャーの UI または PowerShell を使用して、仮想マシンの 1 つでクラスターを検証します。 

UI を使用してクラスターを検証するには、次の手順に従います。 

1. **[サーバー マネージャー]** で、 **[ツール]** を選択し、 **[フェールオーバー クラスター マネージャー]** を選択します。
1. **[フェールオーバー クラスター マネージャー]** で、 **[操作]** を選択し、 **[構成の検証]** を選択します。
1. **[次へ]** を選択します。
1. **[サーバーまたはクラスターの選択]** で、両方の仮想マシンの名前を入力します。
1. **[テスト オプション]** で、 **[選択するテストのみを実行する]** を選択します。 
1. **[次へ]** を選択します。
1. **[テストの選択]** の下で、 **[記憶域]** を *除く* すべてのテストを選択します。
1. **[次へ]** を選択します。
1. **[確認]** で、 **[次へ]** を選択します。  **構成の検証** ウィザードにより、検証テストが実行されます。


PowerShell を使用してクラスターを検証するには、いずれかの仮想マシンの管理者 PowerShell セッションから次のスクリプトを実行します。

```powershell
Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
```

## <a name="test-cluster-failover"></a>クラスターのフェールオーバーをテストする

クラスターのフェールオーバーをテストします。 **[フェールオーバー クラスター マネージャー]** で、クラスターを右クリックし、 **[その他の操作]**  >  **[コア クラスター リソースの移動]**  >  **[ノードの選択]** の順に選択した後、クラスターの他のノードを選択します。 コア クラスター リソースをクラスターのすべてのノードに移動してから、プライマリ ノードに戻します。 SQL Server をインストールする前に、クラスターを各ノードに正常に移動できることを確認してください。

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="コア リソースを他のノードに移動して、クラスター フェールオーバーをテストする":::

## <a name="add-shared-disks-to-cluster"></a>共有ディスクをクラスターに追加する

フェールオーバー クラスター マネージャーを使用して、アタッチした Azure 共有ディスクをクラスターに追加します。 

ディスクをクラスターに追加するには、次の手順に従います。 

1. **[サーバー マネージャー]** ダッシュボードで、 **[ツール]** を選択し、 **[フェールオーバー クラスター マネージャー]** を選択します。
1. ナビゲーション ウィンドウでクラスターを選択して展開します。 
1. **[記憶域]** 、 **[ディスク]** の順に選択します。 
1. **[ディスク]** を右クリックして **[ディスクの追加]** を選択します。 

    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/cluster-add-disk.png" alt-text="ディスクの追加":::

1. **[クラスターへのディスクの追加]** ウィンドウで、Azure 共有ディスクを選択します。  **[OK]** を選択します。
 
    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/cluster-select-shared-disk.png" alt-text="ディスクの選択":::

1. 共有ディスクがクラスターに追加されると、フェールオーバー クラスター マネージャーに表示されます。

    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/cluster-shared-disk.png" alt-text="クラスター ディスク":::



## <a name="create-sql-server-fci"></a>SQL Server FCI を作成する

フェールオーバー クラスターと、ストレージを含むすべてのクラスター コンポーネントを構成したら、SQL Server FCI を作成できます。

1. リモート デスクトップ プロトコル (RDP) を使用して、最初の仮想マシンに接続します。

1. **[フェールオーバー クラスター マネージャー]** で、すべてのコア クラスター リソースが最初の仮想マシン上にあることを確認します。 必要に応じて、ディスクをその仮想マシンに移動します。

1. インストール メディアを探します。 仮想マシンでいずれかの Azure Marketplace イメージが使用されている場合、メディアは `C:\SQLServer_<version number>_Full` にあります。 

1. **[Setup]\(セットアップ\)** を選択します。

1. **[SQL Server インストール センター]** で、 **[インストール]** を選択します。

1. **[SQL Server フェールオーバー クラスターの新規インストール]** を選択します。 ウィザードの指示に従って、SQL Server FCI をインストールします。

1. **[クラスター ディスクの選択]** ページで、VM にアタッチされていたすべての共有ディスクを選択します。 

    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/sql-install-cluster-disk-selection.png" alt-text="クラスター ディスクの選択":::

1. **[クラスター ネットワークの構成]** ページで指定する IP は、SQL Server VM を 1 つのサブネットにデプロイしたのか、それとも複数のサブネットにデプロイしたのかによって異なります。 

   1. **単一サブネット環境** の場合、[Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) に追加する IP アドレスを指定します。
   1. **複数サブネット環境** の場合、[フェールオーバー クラスター インスタンスのネットワーク名の IP アドレス](failover-cluster-instance-prepare-vm.md#assign-secondary-ip-addresses)として以前に指定した、_最初の_ SQL Server VM のサブネット内のセカンダリ IP アドレスを指定します。

   :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/sql-install-cluster-network-secondary-ip-vm-1.png" alt-text="フェールオーバー クラスター インスタンスのネットワーク名の IP アドレスとして以前に指定した、最初の SQL Server VM のサブネット内のセカンダリ IP アドレスを指定する":::

1. **[データベース エンジンの構成]** ページで、データベース ディレクトリが Azure 共有ディスクにあることを確認します。 

1. ウィザードの手順を完了すると、セットアップにより、SQL Server FCI が最初のノードにインストールされます。

1. 最初のノードで FCI のインストールが成功したら、RDP を使用して 2 番目のノードに接続します。

1. **[SQL Server インストール センター]** を開き、 **[インストール]** を選択します。

1. **[SQL Server フェールオーバー クラスターにノードを追加]** を選択します。 ウィザードの指示に従って SQL Server をインストールし、このノードを FCI に追加します。

1. 複数サブネットの場合、 **[クラスター ネットワークの構成]** で、[フェールオーバー クラスター インスタンスのネットワーク名の IP アドレス](failover-cluster-instance-prepare-vm.md#assign-secondary-ip-addresses)として以前に指定した、_2 番目の_ SQL Server VM のサブネット内のセカンダリ IP アドレスを入力します。

    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/sql-install-cluster-network-secondary-ip-vm-2.png" alt-text="フェールオーバー クラスター インスタンスのネットワーク名の IP アドレスとして以前に指定した、2 番目の SQL Server VM のサブネット内のセカンダリ IP アドレスを入力する":::

    **[クラスター ネットワークの構成]** で **[次へ]** を選択した後、セットアップでは、サンプル画像のように SQL Server のセットアップによって複数のサブネットが検出されたことを示すダイアログ ボックスが表示されます。  **[はい]** を選択して確定します。 

    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/sql-install-multi-subnet-confirmation.png" alt-text="複数のサブネットの確認":::

1. ウィザードの手順を完了すると、セットアップによって 2 番目の SQL Server FCI ノードが追加されます。 

1. SQL Server フェールオーバー クラスター インスタンスに参加させる他のすべての SQL Server VM で、以上の手順を繰り返します。 


>[!NOTE]
> Azure Marketplace ギャラリー イメージには、SQL Server Management Studio がインストールされています。 マーケットプレース イメージを使用しなかった場合は、[SQL Server Management Studio (SSMS) をダウンロード](/sql/ssms/ownload-sql-server-management-studio-ssms)してください。


## <a name="register-with-sql-iaas-extension"></a>SQL IaaS 拡張機能に登録する 

ポータルから SQL Server VM を管理するには、それを[軽量管理モード](sql-agent-extension-manually-register-single-vm.md#lightweight-mode)で SQL IaaS Agent 拡張機能に登録します。このモードは、現時点では、FCI と Azure VM 上の SQL Server でサポートされている唯一のモードです。 

PowerShell を使用して軽量モードで SQL Server VM を登録します (-LicenseType は `PAYG` または `AHUB` にできます)。

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>

# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType PAYG -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>接続の構成 

複数のサブネットに SQL Server VM をデプロイした場合は、この手順をスキップしてください。 1 つのサブネットに SQL Server VM をデプロイした場合は、トラフィックを FCI にルーティングするための追加コンポーネントを構成する必要があります。 フェールオーバー クラスター インスタンスに対して、仮想ネットワーク名 (VNN) と Azure Load Balancer、または分散ネットワーク名を構成できます。 [この 2 つの違いを確認](hadr-windows-server-failover-cluster-overview.md#virtual-network-name-vnn)してから、フェールオーバー クラスター インスタンスに対して[分散ネットワーク名](failover-cluster-instance-distributed-network-name-dnn-configure.md)または[仮想ネットワーク名と Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) をデプロイします。  

## <a name="limitations"></a>制限事項

- Azure Virtual Machines では、CSV および [Standard Load Balancer](../../../load-balancer/load-balancer-overview.md) 上のストレージを備えた Windows Server 2019 で、 Microsoft 分散トランザクション コーディネーター (MSDTC) がサポートされています。 Windows Server 2016 およびそれ以前では MSDTC はサポートされていません。 
- [軽量管理モード](sql-server-iaas-agent-extension-automate-management.md#management-modes)での SQL IaaS Agent 拡張機能への登録のみがサポートされています。

## <a name="next-steps"></a>次のステップ

Azure 共有ディスクがお客様に適した FCI 記憶域ソリューションでない場合は、代わりに [Premium ファイル共有](failover-cluster-instance-premium-file-share-manually-configure.md)または[記憶域スペース ダイレクト](failover-cluster-instance-storage-spaces-direct-manually-configure.md)を使用して FCI を作成することを検討してください。 


詳細については、以下をご覧ください。

- [Windows Server フェールオーバー クラスターと Azure VM 上の SQL Server](hadr-windows-server-failover-cluster-overview.md)
- [Azure VM 上の SQL Server を使用したフェールオーバー クラスター インスタンス](failover-cluster-instance-overview.md)
- [フェールオーバー クラスター インスタンスの概要](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
- [Azure VM 上の SQL Server に対する HADR 設定](hadr-cluster-best-practices.md)
