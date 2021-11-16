---
title: 記憶域スペース ダイレクトで FCI を作成する
description: 記憶域スペース ダイレクトを使用して、Azure Virtual Machines 上の SQL Server でフェールオーバー クラスター インスタンス (FCI) を作成します。
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
ms.openlocfilehash: 56a939e11c9daabeb44da7fa79b6e05841401ab3
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158865"
---
# <a name="create-an-fci-with-storage-spaces-direct-sql-server-on-azure-vms"></a>記憶域スペース ダイレクトで FCI を作成する (Azure VM 上の SQL Server)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!TIP]
> 同じ Azure 仮想ネットワーク内の[複数のサブネット](failover-cluster-instance-prepare-vm.md#subnets)に SQL Server VM を作成することで、フェールオーバー クラスター インスタンスに対して Azure Load Balancer または分散ネットワーク名 (DNN) が不要になります。

この記事では、[記憶域スペース ダイレクト](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)を使用して、Azure Virtual Machines (VM) 上の SQL Server でフェールオーバー クラスター インスタンス (FCI) を作成する方法について説明します。 記憶域スペース ダイレクトは、Windows クラスター内のノード (Azure VM) 間でストレージ (データ ディスク) を同期するソフトウェア ベースの仮想記憶域ネットワーク (VSAN) として機能します。 

詳細については、[Azure VM 上の SQL Server を使用した FCI](failover-cluster-instance-overview.md) および[クラスターのベスト プラクティス](hadr-cluster-best-practices.md)の概要に関する記事をご覧ください。 

> [!NOTE]
> これで Azure Migrate を使用して、フェールオーバー クラスター インスタンス ソリューションを Azure VM 上の SQL Server にリフト アンド シフトできるようになりました。 詳細については、[フェールオーバー クラスター インスタンスの移行](../../migration-guides/virtual-machines/sql-server-failover-cluster-instance-to-sql-on-azure-vm.md)に関するページを参照してください。 


## <a name="overview"></a>概要 

[記憶域スペース ダイレクト (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) では、コンバージド型とハイパーコンバージド型の 2 種類のアーキテクチャがサポートされています。 ハイパーコンバージド インフラストラクチャでは、SQL Server FCI ノードごとにストレージが存在するように、クラスター化されたアプリケーションをホストしている同じサーバーにストレージが配置されます。 

次の図では、Azure VM 上の SQL Server でハイパーコンバージド記憶域スペース ダイレクトを使用する完全なソリューションを示します。 

![ハイパーコンバージド記憶域スペース ダイレクトを使用する完全なソリューションの図](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/00-sql-fci-s2d-complete-solution.png)

上の図では、次のリソースが同じリソース グループ内に示されています。

- Windows Server フェールオーバー クラスター内の 2 つの仮想マシン。 仮想マシンがフェールオーバー クラスター内にある場合、仮想マシンは *クラスター ノード* または *ノード* とも呼ばれます。
- 各仮想マシンには、2 つ以上のデータ ディスクがあります。
- 記憶域スペース ダイレクトは、データ ディスク上のデータを同期し、同期されたストレージを記憶域プールとして提供します。
- 記憶域プールは、クラスターの共有ボリューム (CSV) をフェールオーバー クラスターに提供します。
- SQL Server FCI のクラスター ロールは、CSV をデータ ドライブ用に使用します。
- サブネットが 1 つの場合に SQL Server FCI の IP アドレスを保持するための Azure ロード バランサー。
- Azure 可用性セットにより、すべてのリソースが保持されます。

 > [!NOTE]
> このソリューション全体を、Azure でテンプレートから作成できます。 テンプレートの例は、GitHub の[Azure クイック スタート テンプレート](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad)のページから使用できます。 この例は、特定のワークロード向けに設計およびテストされたものではありません。 このテンプレートを実行すれば、ドメインに接続された記憶域スペース ダイレクト ストレージで SQL Server FCI を作成できます。 このテンプレートは、評価のうえ、目的に応じた変更が可能です。


## <a name="prerequisites"></a>前提条件

この記事に記載されている手順を完了するには、次のものが必要です。

- Azure サブスクリプション。 [無料](https://azure.microsoft.com/free/)で開始できます。 
- [可用性セット](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set)内の [2 つ以上の準備済みの Windows Azure 仮想マシン](failover-cluster-instance-prepare-vm.md)。
- Azure の仮想マシンと Active Directory の両方にオブジェクトを作成するためのアクセス許可を持つアカウント。
- 最新バージョンの [PowerShell](/powershell/azure/install-az-ps)。 

## <a name="create-windows-failover-cluster"></a>Windows フェールオーバー クラスターの作成

Windows Server フェールオーバー クラスターを作成する手順は、SQL Server VM を 1 つのサブネットにデプロイしたか、それとも複数のサブネットにデプロイしたかによって異なります。 クラスターを作成するには、[複数のサブネットの場合](availability-group-manually-configure-tutorial-multi-subnet.md#add-failover-cluster-feature)または [1 つのサブネットの場合](availability-group-manually-configure-tutorial-single-subnet.md#create-the-cluster)のチュートリアルの手順に従います。 これらのチュートリアルは可用性グループを作成するためのものですが、クラスターを作成する手順は同じです。 

## <a name="configure-quorum"></a>クォーラムを構成する

ディスク監視は最も回復力のあるクォーラム オプションですが、記憶域スペース ダイレクトで構成されたフェールオーバー クラスター インスタンスではサポートされていません。 そのため、クラウド監視は、Azure VM 上の SQL Server 向けのこの種類のクラスター構成に推奨されるクォーラム ソリューションです。

クラスターに多数の投票がある場合は、ビジネス ニーズに最適な[クォーラム ソリューション](hadr-cluster-quorum-configure-how-to.md)を構成します。 詳細については、[SQL Server VM でのクォーラム](hadr-windows-server-failover-cluster-overview.md#quorum)に関する記事をご覧ください。 

## <a name="validate-the-cluster"></a>クラスターを検証する

フェールオーバー クラスター マネージャーの UI で、または PowerShell を使用してクラスターを検証します。

UI を使用してクラスターを検証するには、いずれかの仮想マシンで次の手順を実行します。

1. **[サーバー マネージャー]** で、 **[ツール]** を選択し、 **[フェールオーバー クラスター マネージャー]** を選択します。
1. **[フェールオーバー クラスター マネージャー]** で、 **[操作]** を選択し、 **[構成の検証]** を選択します。
1. **[次へ]** を選択します。
1. **[サーバーまたはクラスターの選択]** で、両方の仮想マシンの名前を入力します。
1. **[テスト オプション]** で、 **[選択するテストのみを実行する]** を選択します。 
1. **[次へ]** を選択します。
1. 次に示すように、 **[テストの選択]** で、 **[ストレージ]** を除くすべてのテストを選択します。

   ![クラスター検証テストを選択する](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/10-validate-cluster-test.png)

1. **[次へ]** を選択します。
1. **[確認]** で、 **[次へ]** を選択します。

    **構成の検証** ウィザードにより、検証テストが実行されます。

PowerShell を使用してクラスターを検証するには、いずれかの仮想マシンの管理者 PowerShell セッションから次のスクリプトを実行します。

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```


## <a name="add-storage"></a>ストレージを追加する

記憶域スペース ダイレクト用のディスクは空にする必要があります。 パーティションまたはその他のデータを含めることはできません。 ディスクをクリーニングするには、「[記憶域スペース ダイレクトの展開](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-31-clean-drives)」の手順に従います。

1. [記憶域スペース ダイレクトを有効にします](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-35-enable-storage-spaces-direct)。

   次の PowerShell スクリプトを実行すると、記憶域スペース ダイレクトが有効化されます。  

   ```powershell
   Enable-ClusterS2D
   ```

   **[フェールオーバー クラスター マネージャー]** に、記憶域プールが表示されるようになります。

1. [ボリュームを作成します](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-36-create-volumes)。

   記憶域スペース ダイレクトは、記憶域プールをユーザーが有効化した場合に、記憶域プールを自動的に作成します。 これでボリュームを作成する準備が整いました。 PowerShell コマンドレット `New-Volume` は、ボリュームの作成プロセスを自動化します。 このプロセスには、書式設定、クラスターへのボリュームの追加、および CSV の作成が含まれます。 次の例では、800 ギガバイト (GB) の CSV が作成されます。

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   前記のコマンドを実行すると、800 GB のボリュームがクラスター リソースとしてマウントされます。 ボリュームは `C:\ClusterStorage\Volume1\` にあります。

   このスクリーンショットでは、記憶域スペース ダイレクトが有効な CSV が示されています。

   ![記憶域スペース ダイレクトが有効なクラスター共有ボリュームのスクリーンショット](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/15-cluster-shared-volume.png)



## <a name="test-cluster-failover"></a>クラスターのフェールオーバーをテストする

クラスターのフェールオーバーをテストします。 **[フェールオーバー クラスター マネージャー]** で、クラスターを右クリックし、 **[その他の操作]**  >  **[コア クラスター リソースの移動]**  >  **[ノードの選択]** の順に選択した後、クラスターの他のノードを選択します。 コア クラスター リソースをクラスターのすべてのノードに移動してから、プライマリ ノードに戻します。 クラスターを各ノードに正常に移動できる場合は、SQL Server をインストールする準備ができています。  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="コア リソースを他のノードに移動して、クラスター フェールオーバーをテストする":::

## <a name="create-sql-server-fci"></a>SQL Server FCI を作成する

フェールオーバー クラスターと、ストレージを含むすべてのクラスター コンポーネントを構成したら、SQL Server FCI を作成できます。

1. RDP を使用して最初の仮想マシンに接続します。

1. **フェールオーバー クラスター マネージャー** で、すべてのコア クラスター リソースが最初の仮想マシン上にあることを確認します。 必要に応じて、すべてのリソースをこの仮想マシンに移動します。

1. インストール メディアを探します。 仮想マシンでいずれかの Azure Marketplace イメージが使用されている場合、メディアは `C:\SQLServer_<version number>_Full` にあります。 **[Setup]\(セットアップ\)** を選択します。

1. **[SQL Server インストール センター]** で、 **[インストール]** を選択します。

1. **[SQL Server フェールオーバー クラスターの新規インストール]** を選択します。 ウィザードの指示に従って、SQL Server FCI をインストールします。

1. **[クラスター ネットワークの構成]** ページで指定する IP は、SQL Server VM を 1 つのサブネットにデプロイしたのか、それとも複数のサブネットにデプロイしたのかによって異なります。 

   1. **単一サブネット環境** の場合、[Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) に追加する IP アドレスを指定します。
   1. **複数サブネット環境** の場合、[フェールオーバー クラスター インスタンスのネットワーク名の IP アドレス](failover-cluster-instance-prepare-vm.md#assign-secondary-ip-addresses)として以前に指定した、_最初の_ SQL Server VM のサブネット内のセカンダリ IP アドレスを指定します。

   :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/sql-install-cluster-network-secondary-ip-vm-1.png" alt-text="フェールオーバー クラスター インスタンスのネットワーク名の IP アドレスとして以前に指定した、最初の SQL Server VM のサブネット内のセカンダリ IP アドレスを指定する":::

1. **[データベース エンジンの構成]** で、FCI のデータ ディレクトリは、クラスター化されたストレージにある必要があります。 記憶域スペース ダイレクトでは、これは共有ディスクではなく、各サーバー上のボリュームのマウント ポイントです。 記憶域スペース ダイレクトは、両方のノード間でボリュームを同期します。 ボリュームは、CSV としてクラスターに表示されます。 データ ディレクトリとして CSV のマウント ポイントを使用します。

   ![データ ディレクトリ](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/20-data-dicrectories.png)

1. ウィザードの手順を完了すると、セットアップにより、SQL Server FCI が最初のノードにインストールされます。

1. 最初のノードで FCI のインストールが成功したら、RDP を使用して 2 番目のノードに接続します。

1. **[SQL Server インストール センター]** を開きます。 **[インストール]** を選択します。

1. **[SQL Server フェールオーバー クラスターにノードを追加]** を選択します。 ウィザードの指示に従って SQL Server をインストールし、このノードを FCI に追加します。

1. 複数サブネットの場合、 **[クラスター ネットワークの構成]** で、[フェールオーバー クラスター インスタンスのネットワーク名の IP アドレス](failover-cluster-instance-prepare-vm.md#assign-secondary-ip-addresses)として以前に指定した、_2 番目の_ SQL Server VM のサブネット内のセカンダリ IP アドレスを入力します。

    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/sql-install-cluster-network-secondary-ip-vm-2.png" alt-text="フェールオーバー クラスター インスタンスのネットワーク名の IP アドレスとして以前に指定した、2 番目の SQL Server VM のサブネット内のセカンダリ IP アドレスを入力する":::

    **[クラスター ネットワークの構成]** で **[次へ]** を選択した後、セットアップでは、サンプル画像のように SQL Server のセットアップによって複数のサブネットが検出されたことを示すダイアログ ボックスが表示されます。  **[はい]** を選択して確定します。 

    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/sql-install-multi-subnet-confirmation.png" alt-text="複数のサブネットの確認":::

1. ウィザードの手順を完了すると、セットアップによって 2 番目の SQL Server FCI ノードが追加されます。 

1. SQL Server フェールオーバー クラスター インスタンスに追加する他のすべてのノードで、以上の手順を繰り返します。 


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
- NTFS でフォーマットされたディスクとして接続されているディスクは、ストレージがクラスターに追加されるときに、ディスク適格性オプションがオフにされている場合にのみ、記憶域スペース ダイレクトで使用できます。 
- [軽量管理モード](sql-server-iaas-agent-extension-automate-management.md#management-modes)での SQL IaaS Agent 拡張機能への登録のみがサポートされています。
- 共有ストレージとして 記憶域スペース ダイレクトを使用するフェールオーバー クラスター インスタンスでは、クラスターのクォーラムに対するディスク監視の使用はサポートされていません。 代わりにクラウド監視を使用してください。 

## <a name="next-steps"></a>次のステップ

記憶域スペース ダイレクトがお客様に適した FCI ストレージ ソリューションでない場合は、代わりに [Azure 共有ディスク](failover-cluster-instance-azure-shared-disks-manually-configure.md)または [Premium ファイル共有](failover-cluster-instance-premium-file-share-manually-configure.md)を使用して FCI を作成することを検討してください。 

詳細については、以下をご覧ください。

- [Windows Server フェールオーバー クラスターと Azure VM 上の SQL Server](hadr-windows-server-failover-cluster-overview.md)
- [Azure VM 上の SQL Server を使用したフェールオーバー クラスター インスタンス](failover-cluster-instance-overview.md)
- [フェールオーバー クラスター インスタンスの概要](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
- [Azure VM 上の SQL Server に対する HADR 設定](hadr-cluster-best-practices.md)
