---
title: FCI 用の仮想マシンを準備する
description: Azure Virtual Machines 上でフェールオーバー クラスター インスタンス (FCI) と SQL Server を使用するように Azure Virtual Machines を準備します。
services: virtual-machines
documentationCenter: na
author: rajeshsetlem
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/10/2021
ms.author: rsetlem
ms.reviewer: mathoma
ms.openlocfilehash: ac7b5617cf81b2845701360cf1d7af3fbc4858c0
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132156122"
---
# <a name="prepare-virtual-machines-for-an-fci-sql-server-on-azure-vms"></a>FCI 用に仮想マシンを準備する (Azure VM 上の SQL Server)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

この記事では、Azure 仮想マシン (VM) を SQL Server フェールオーバー クラスター インスタンス (FCI) で使用するように準備する方法について説明します。 構成設定は FCI ストレージ ソリューションによって異なるため、環境やビジネスに適した構成を選択していることを確認してください。 

詳細については、[Azure VM 上の SQL Server を使用した FCI](failover-cluster-instance-overview.md) および[クラスターのベスト プラクティス](hadr-cluster-best-practices.md)の概要に関する記事をご覧ください。 

> [!NOTE]
> これで Azure Migrate を使用して、フェールオーバー クラスター インスタンス ソリューションを Azure VM 上の SQL Server にリフト アンド シフトできるようになりました。 詳細については、[フェールオーバー クラスター インスタンスの移行](../../migration-guides/virtual-machines/sql-server-failover-cluster-instance-to-sql-on-azure-vm.md)に関するページを参照してください。 

## <a name="prerequisites"></a>前提条件 

- Microsoft Azure サブスクリプションが必要です。 [無料](https://azure.microsoft.com/free/)で開始できます。 
- Azure 仮想マシン上の Windows ドメイン、または仮想ネットワーク ペアリングで Azure に拡張されたオンプレミスの Active Directory。
- Azure 仮想マシンと Active Directory にオブジェクトを作成するためのアクセス許可を持つアカウント。
- 次のコンポーネント用の十分な IP アドレス空間を持つ Azure 仮想ネットワークと 1 つまたは複数のサブネット。
   - 両方の仮想マシン
   - Windows フェールオーバー クラスターの IP アドレス
   - 各 FCI の IP アドレス
- Azure ネットワーク上で構成された、ドメイン コントローラーを指す DNS。



## <a name="choose-an-fci-storage-option"></a>FCI ストレージ オプションを選択する

仮想マシンの構成設定は、SQL Server フェールオーバー クラスター インスタンスに使用する予定のストレージ オプションによって異なります。 仮想マシンを準備する前に、[使用可能な FCI ストレージ オプション](failover-cluster-instance-overview.md#storage)を確認し、環境やビジネス ニーズに最も適したオプションを選択します。 次に、ストレージの選択に基づいて、この記事全体で適切な VM 構成オプションを選択します。 

## <a name="choose-vm-availability"></a>VM の可用性の選択 

フェールオーバー クラスター機能を使用するには、仮想マシンを[可用性セット](../../../virtual-machines/linux/tutorial-availability-sets.md)または[可用性ゾーン](../../../availability-zones/az-overview.md#availability-zones)に配置する必要があります。

目的のクラスター構成に適した VM 可用性オプションを慎重に選択します。 

- **Azure 共有ディスク**: Premium SSD または UltraDisk を使用している場合、使用可能なオプションは異なります。
   - **Premium SSD ゾーン冗長ストレージ (ZRS)** : [可用性ゾーン](../../../availability-zones/az-overview.md#availability-zones)を別のゾーンに配置します。 [Premium SSD ZRS](../../../virtual-machines/disks-redundancy.md#zone-redundant-storage-for-managed-disks) では、選択されたリージョンにある 3 つの Azure 可用性ゾーン間で Azure マネージド ディスクを同期的にレプリケートします。 フェールオーバー クラスターの VM 部分を別の可用性ゾーンに配置できるため、99.99% の VM 可用性 SLA を提供するゾーン冗長 SQL Server FCI の実現に役立ちます。 データのクロスゾーン コピーのために、ZRS のほうがディスク待機時間が長くなります。
   - **Premium SSD ローカル冗長ストレージ (LRS)** : [Premium SSD LRS](../../../virtual-machines/disks-redundancy.md#locally-redundant-storage-for-managed-disks) 用の別の障害/更新ドメインに[可用性セット](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set)を配置します。 VM が互いにもっと近くなるよう、[近接配置グループ](../../../virtual-machines/windows/proximity-placement-groups-portal.md)内に VM を配置することも選択できます。 可用性セットと近接配置グループを組み合わせると、1 つのデータセンター内でデータがローカルに複製されるため共有ディスクの待機時間が最小になり、99.95% の VM 可用性 SLA が実現します。
   - **Ultra Disk ローカル冗長ストレージ (LRS)** : [可用性ゾーン](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address)ですが、VM を同じ可用性ゾーンに配置する必要があります。 [Ultra Disk](../../../virtual-machines/disks-enable-ultra-ssd.md) はディスク待機時間が最小のため、IO 負荷の高いワークロードに最適です。 FCI の VM 部分全体が同じ可用性ゾーンにあるため、VM 可用性は 99.9% にとどまります。 
- **Premium ファイル共有**: [可用性セット](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set)または[可用性ゾーン](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address)。
- **記憶域スペース ダイレクト**: [可用性セット](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set)。

> [!IMPORTANT]
> 仮想マシンを作成した後に可用性セットを設定または変更することはできません。

## <a name="subnets"></a>サブネット 

Azure VM 上の SQL Server では、SQL Server VM を 1 つのサブネットまたは複数のサブネットにデプロイするオプションがあります。 

複数のサブネットに VM をデプロイすると、IP アドレスに関してクラスターの OR 依存関係が利用され、フェールオーバー クラスター インスタンスに接続するときのオンプレミス エクスペリエンスに一致します。 管理が簡素化され、フェールオーバー時間も短縮されるため、Azure VM 上の SQL Server に関しては複数のサブネットのアプローチをお勧めしています。 

1 つのサブネットに VM を デプロイする場合、トラフィックを FCI にルーティングするために、Azure Load Balancer または分散ネットワーク名 (DNN) への追加の依存関係が必要になります。 

複数のサブネットに SQL Server VM をデプロイする場合は、このセクションの手順に従って、追加のサブネットを持つ仮想ネットワークを作成し、SQL Server VM が作成されたら、それらのサブネット内の[セカンダリ IP アドレスを VM に割り当てます](#assign-secondary-ip-addresses)。 1 つのサブネットに SQL Server VM をデプロイする場合、追加のネットワーク構成は必要ありません。 

# <a name="single-subnet"></a>[1 つのサブネット](#tab/single-subnet)

両方の仮想マシンと、最終的にクラスターにインストールする可能性があるすべての FCI のための十分な IP アドレスがある 1 つのサブネットに、両方の仮想マシンを配置します。 このアプローチでは、Azure Load Balancer や分散ネットワーク名 (DNN) など、FCI に接続をルーティングするための追加のコンポーネントが必要になります。 

1 つのサブネットに SQL Server VM を デプロイすることを選択した場合は、環境のその他の部分を FCI のために準備する前に、[Azure Load Balancer オプションと DNN 接続オプションの違いについて確認し](hadr-windows-server-failover-cluster-overview.md#distributed-network-name-dnn)、どちらのオプションが最適であるかを判断してください。

1 つのサブネットに SQL Server VM をデプロイする場合、追加のネットワーク構成は必要ありません。 

# <a name="multi-subnet"></a>[複数のサブネット](#tab/multi-subnet)

接続を SQL Server FCI に直接ルーティングする場合は、1 つの仮想ネットワーク内の別々のサブネットに両方の仮想マシンを配置します。 フェールオーバー クラスター インスタンス用の SQL Server VM にセカンダリ IP アドレスを割り当てます。また、Windows Server 2016 以下を使用している場合は、Windows Server フェールオーバー クラスター用のセカンダリ IP アドレスも追加で割り当てます。 Windows Server 2019 以降では、分散ネットワーク名 (DNN) をクラスター名に使用するため、クラスターのセカンダリ IP アドレスは不要です。 

このアプローチでは、SQL Server FCI に接続するときに Azure Load Balancer または分散ネットワーク名 (DNN) は必要ありません。 

複数のサブネットに SQL Server VM をデプロイすることを選択した場合、まず、2 つの追加サブネットがある仮想ネットワークを作成する必要があります。SQL Server VM が作成されたら、[セカンダリ IP アドレスを VM に割り当てます](#assign-secondary-ip-addresses)。  詳細については、[仮想ネットワークの概要](../../../virtual-network/virtual-networks-overview.md)に関するページを参照してください。 このセクションで示しているサブネット名と IP アドレスは単なる例であり、環境によって異なる場合があります。 Azure portal で仮想ネットワークを作成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) で、ご利用のリソース グループに移動して **[+ 作成]** を選択します。

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/02-create-resource-rg.png" alt-text="新しいリソースをリソース グループに作成する":::

1. **[マーケットプレース]** 検索ボックスで「**仮想ネットワーク**」を検索し、Microsoft の **[仮想ネットワーク]** タイルを選択します。 **[仮想ネットワーク]** ページで **[作成]** を選択します。  
1. **[仮想ネットワークの作成]** ページで、 **[基本]** タブに次の情報を入力します。 
   1. **[プロジェクトの詳細]** で、適切な Azure **サブスクリプション** と、SQL Server VM のデプロイを計画している **リソース グループ** を選択します。 
   1. **[インスタンスの詳細]** で、ご利用の仮想ネットワークの名前を指定し、ドロップダウンからリソース グループと同じリージョンを選びます。

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/03-create-vnet-basics.png" alt-text="前に作成したリソース グループを選び、仮想ネットワークの名前を指定します":::

1. **[IP アドレス]** タブで、 **[+ サブネットの追加]** を選択し、最初の SQL Server VM にサブネットを追加し、次の値を入力します。 
   1. **[サブネット名]** に値 (**SQL-subnet-1** など) を指定します。 
   1. 仮想ネットワーク アドレス空間内に一意のサブネット アドレス範囲を指定します。 たとえば、DC サブネット アドレス範囲の 3 番目のオクテットを 1 回繰り返すことができます。 
      - たとえば、**既定** の範囲が *10.38.0.0/24* の場合、**SQL-subnet-1** の IP アドレス範囲 `10.38.1.0/24` を入力します。 
      - 同様に、**既定** の IP 範囲が *10.5.0.0/24* の場合、新しいサブネットに対して `10.5.1.0/24` と入力します。 
   1. **[追加]** を選択して、新しいサブネットを追加します。 

     :::image type="content" source="./media/failover-cluster-instance-prepare-vm/05-create-vnet-ip-address-add-sql-subnet-1.png" alt-text="最初のサブネットに sql-subnet-1 などの名前を付け、3 番目のオクテットを 1 回繰り返します。これにより、DC サブネットの IP アドレスが 10.5.0.0 の場合、新しいサブネットは 10.5.1.0 になります":::

1. 前の手順を繰り返して、**SQL-subnet-2** などの名前で 2 つ目の SQL Server VM に対して一意のサブネット範囲を追加します。 もう一度 3 番目のオクテットを 1 つ反復処理できます。 
   - たとえば、**既定** の IP 範囲が *10.38.0.0/24* で、**SQL-subnet-1** が *10.38.1.0/24* の場合、新しいサブネットに対して `10.38.2.0/24` と入力します。
   - 同様に、**既定** の IP 範囲が *10.5.0.0/24* で、**SQL-subnet-1** が *10.5.1.0/24* の場合、**SQL-subnet-2** の IP アドレス範囲 `10.5.2.0/24` を入力します。 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/06-create-vnet-ip-address-add-sql-subnet-2.png" alt-text="2 番目のサブネットに sql-subnet-2 などの名前を付け、3 番目のオクテットを 2 回繰り返します。これにより、DC サブネットの IP アドレスが 10.38.0.0/24 の場合、新しいサブネットは 10.38.2.0/24 になります":::

1. 2 つ目のサブネットを追加したら、サブネットの名前と範囲を確認します (IP アドレス範囲は画像と異なる場合があります)。 すべて正しければ、 **[確認と作成]** 、 **[作成]** の順に選択して、新しい仮想ネットワークを作成します。 

   :::image type="content" source="./media/failover-cluster-instance-prepare-vm/07-create-vnet-ip-address.png" alt-text="2 つ目のサブネットを追加したら、サブネットの名前と範囲が画像のようになっていることを確認します (ただし IP アドレス範囲は異なる場合があります)。すべて正しければ、[確認と作成]、[作成] の順に選択して、新しい仮想ネットワークを作成します。":::

   新しいネットワークが作成されると、画面がポータル ダッシュボードに切り替わって通知が表示されます。

---

## <a name="configure-dns"></a>DNS を構成する

DNS サーバーを使用するように仮想ネットワークを構成します。 まず、DNS の IP アドレスを特定して仮想ネットワークに追加します。 

### <a name="identify-dns-ip-address"></a>DNS IP アドレスの特定

DNS サーバーの IP アドレスを特定して、仮想ネットワーク構成に追加します。 ここでは、DNS サーバーが Azure の仮想マシンにある場合に DNS の IP アドレスを特定する方法を示します。 

Azure portal で DNS サーバー VM の IP アドレスを特定するには、次の手順に従います。 

1. [Azure portal](https://portal.azure.com) で、リソース グループに移動して、DNS サーバー VM を選択します。 
1. VM ページで、 **[設定]** ペインの **[ネットワーク]** を選択します。 
1. これは DNS サーバーの IP アドレスであるため、**NIC の プライベート IP** アドレスをメモしておいてください。 この例の画像では、プライベート IP アドレスは **10.38.0.4** です。 

:::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/12-dc-vm-1-private-ip.png" alt-text="[DC-VM-1] ページの [設定] ペインで [ネットワーク] を選び、NIC のプライベート IP アドレスをメモします。この IP アドレスは、他の仮想マシンの DNS サーバーとして使用します。":::

### <a name="configure-virtual-network-dns"></a>仮想ネットワーク DNS の構成

これを DNS サーバーの IP アドレスとして使用するように仮想ネットワークを構成します。 

DNS 用に仮想ネットワークを構成するには、次の手順に従います。 

1. [Azure portal](https://portal.azure.com) で、リソース グループに移動して、仮想ネットワークを選択します。 
1. **[設定]** ペインで **[DNS サーバー]** を選択してから、 **[カスタム]** を選択します。 
1. 以前に特定した (`10.38.0.4` などの) プライベート IP アドレスを **[IP アドレス]** フィールドに入力するか、内部 DNS サーバーの内部 IP アドレスを入力します。 
1. **[保存]** を選択します。 

:::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/12-identify-dns-ip-address.png" alt-text="[設定] ペインで [DNS サーバー] を選択してから、[カスタム] を選択します。[IP アドレス] フィールドに、前に特定したプライベート IP アドレス (10.38.0.4 など) を入力します。":::

## <a name="create-the-virtual-machines"></a>仮想マシンの作成

VM の仮想ネットワークを構成して VM の可用性を選択したら、仮想マシンを作成する準備が整います。 SQL Server が既にインストールされている、またはインストールされていない、Azure Marketplace のイメージを使用できます。 ただし、Azure VM に SQL Server が付属するイメージを選択する場合は、フェールオーバー クラスター インスタンスを構成する前に、仮想マシンから SQL Server をアンインストールする必要があります。 

### <a name="nic-considerations"></a>NIC に関する考慮事項

Azure VM ゲスト フェールオーバー クラスターでは、サーバー (クラスター ノード) ごとに 1 つの NIC を推奨しています。 Azure ネットワークは物理的な冗長性を備えているので、Azure IaaS VM ゲスト クラスターで NIC を追加する必要はありません。 クラスター検証レポートでは、1 つのネットワークでしかノードに到達できないという警告が出ますが、Azure IaaS VM ゲスト フェールオーバー クラスターではこの警告を無視しても安全です。

両方の仮想マシンを配置します。

- 可用性セットを使用している場合は、可用性セットと同じ Azure リソース グループ内。
- ドメイン コントローラーおよび DNS サーバーと同じ仮想ネットワーク上、またはドメイン コントローラーへの適切な接続がある仮想ネットワーク上。
- Azure 可用性セット内または可用性ゾーン内。

SQL Server がプレインストールされて[いる](sql-vm-create-portal-quickstart.md)または[いない](../../../virtual-machines/windows/quick-create-portal.md)イメージを使用して、Azure 仮想マシンを作成できます。 SQL Server イメージを選択した場合は、フェールオーバー クラスター インスタンスをインストールする前に、手動で SQL Server インスタンスをアンインストールする必要があります。  

### <a name="assign-secondary-ip-addresses"></a>セカンダリ IP アドレスの割り当て

1 つのサブネットに SQL Server VM をデプロイした場合は、この手順をスキップしてください。 FCI への接続性を向上させるために SQL Server VM を複数のサブネットにデプロイした場合は、各 VM にセカンダリ IP アドレスを割り当てる必要があります。 

フェールオーバー クラスター インスタンスのネットワーク名に使用するセカンダリ IP アドレスを各 SQL Server VM に割り当てます。Windows Server 2016 およびそれ以前の場合は、クラスターのネットワーク名のセカンダリ IP アドレスも各 SQL Server VM に割り当てます。 これを行うと、単一のサブネット環境での要件と同様に、Azure Load Balancer の必要性がなくなります。  

Windows Server 2016 以前では、クラスターで Windows Server 2019 で導入された既定の分散ネットワーク名 (DNN) ではなく **クラスター ネットワーク名** が使用されるため、Windows クラスター IP に使用する各 SQL Server VM に追加のセカンダリ IP アドレスを割り当てる必要があります。 DNN を使用すると、クラスター名オブジェクト (CNO) がクラスターのすべてのノードの IP アドレスに自動的に登録されるため、専用の Windows クラスター IP アドレスは不要になります。

Windows Server 2016 以前を使用している場合は、このセクションの手順に従って、FCI ネットワーク名 "*および*" クラスターの "*両方*" の各 SQL Server VM にセカンダリ IP アドレスを割り当てます。 

Windows Server 2019 以降を使用している場合は、FCI ネットワーク名のセカンダリ IP アドレスのみを割り当て、Windows クラスター IP を割り当てる手順をスキップします。ただし、仮想ネットワーク名 (VNN) を使用してクラスターを構成する予定がない場合は、Windows Server 2016 の場合と同様に両方の IP アドレスを各 SQL Server VM に割り当てます。 

追加のセカンダリ IP を VM に割り当てるには、次の手順を実行します。 

1. [Azure portal](https://portal.azure.com/) で、リソース グループに移動して、最初の SQL Server VM を選択します。 
1. **[設定]** ペインで **[ネットワーク]** を選択してから、 **[ネットワーク インターフェイス]** を選択します。 

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/19-sql-vm-network-interface.png" alt-text="[設定] ペインで [ネットワーク] を選択してから [ネットワーク インターフェイス] を選択する":::

1. **[ネットワーク インターフェイス]** ページで、 **[設定]** ペインの **[IP 構成]** を選択してから、 **[+ 追加]** を選択して追加の IP アドレスを追加します。 

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/20-ip-configurations-add.png" alt-text="IP 構成":::

1. **[IP 構成の追加]** ページで、次の操作を行います。 
   1. Windows 2016 およびそれ以前の場合、Windows クラスター IP アドレスの **[名前]** を **windows-cluster-ip** のように指定します。 Windows Server 2019 以降を使用している場合は、この手順をスキップします。 
   1. **[割り当て]** を **[静的]** に設定します。
   1. SQL Server VM と同じサブネット (**SQL-subnet-1**) 内の未使用の **IP アドレス** (`10.38.1.10` など) を入力します。 
   1. **[パブリック IP アドレス]** は既定値の **[関連付けの解除]** のままにします。 
   1. **[OK]** を選択して、IP 構成の追加を終了します。 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/21-add-ip-windows-cluster.png" alt-text="最初の SQL Server VM のサブネット内の未使用 IP アドレスを入力してクラスター IP を追加する":::

1. もう一度 **[+ 追加]** を選択して、(**FCI-network-name** のような名前の) FCI ネットワーク名に対する追加の IP アドレスを構成します。ここでも、**SQL-subnet-1** 内の未使用の IP アドレス (`10.38.1.11` など) を指定します。 

   :::image type="content" source="./media/failover-cluster-instance-prepare-vm/22-add-fci-ip-address.png" alt-text="もう一度 [+ 追加] を選択して、(availability-group-listener のような名前の) 可用性グループ リスナーに対する追加の IP アドレスを構成する。ここでも、SQL-subnet-1 内の未使用の IP アドレス (10.31.1.11 など) を指定する":::

1. 2 番目の SQL Server VM について、以上の手順をもう一度繰り返します。 **SQL-subnet-2** 内の未使用のセカンダリ IP アドレスを 2 つ割り当てます。 IP 構成を追加するには、次の表の値を使用します (ただし、IP アドレスは単なる例であり、環境によって異なる場合があります)。 

   
    | **フィールド** | 入力 | 入力 | 
    | --- | --- | --- |
    | **名前** |windows-cluster-ip | FCI-network-name |
    | **Allocation** | 静的 | 静的 |
    | **IP アドレス (IP address)** | 10.38.2.10 | 10.38.2.11 | 
    |  |  |  |



## <a name="uninstall-sql-server"></a>SQL Server のアンインストール

FCI 作成プロセスの一環として、SQL Server をクラスター化されたインスタンスとしてフェールオーバー クラスターにインストールします。 "*SQL Server のない Azure Marketplace イメージを使用して仮想マシンをデプロイした場合は、このステップを省略できます。* " SQL Server がプレインストールされたイメージをデプロイした場合は、SQL IaaS Agent 拡張機能から SQL Server VM の登録を解除し、SQL Server をアンインストールする必要があります。 

### <a name="unregister-from-the-sql-iaas-agent-extension"></a>SQL IaaS Agent 拡張機能からの登録解除

Azure Marketplace の SQL Server VM イメージは、SQL IaaS Agent 拡張機能に自動的に登録されます。 プレインストールされている SQL Server インスタンスをアンインストールする前に、まず [SQL IaaS Agent 拡張機能から各 SQL Server VM を登録解除する](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension)必要があります。 

### <a name="uninstall-sql-server"></a>SQL Server のアンインストール

拡張機能から登録を解除した後、SQL Server をアンインストールできます。 各仮想マシンで、次の手順のようにします。 

1. RDP を使用して仮想マシンに接続します。 RDP を使用して最初に仮想マシンに接続する際、この PC をネットワーク上で検出可能にするかどうかを確認するメッセージが表示されます。 **[はい]** を選択します。
1. **[コントロール パネル]** の **[プログラムと機能]** を開きます。 
1. **[プログラムと機能]** で **[Microsoft SQL Server 201_ (64 ビット)]** を右クリックし、 **[アンインストールと変更]** を選択します。
1. **[削除]** を選択します。
1. 既定のインスタンスを選択します。
1. **[データベース エンジン サービス]** 、 **[Analysis Services]** 、 **[Reporting Services - ネイティブ]** のすべての機能を削除します。 **[SharedFeatures]** の下にあるものは何も削除しないでください。 次のスクリーンショットのように表示されます。![機能の選択](./media/failover-cluster-instance-prepare-vm/remove-features-updated.png)
1. **[次へ]** 、 **[削除]** の順に選択します。
1. インスタンスが正常に削除されたら、仮想マシンを再起動します。 

## <a name="open-the-firewall"></a>ファイアウォールを開く 

各仮想マシンで、SQL Server で使用されている Windows ファイアウォール TCP ポートを開きます。 SQL Server では既定でポート 1433 を使用しますが、環境でこれを変更した場合は、SQL Server インスタンスで使用するように構成したポートを開いてください。 ポート 1433 は、Azure Marketplace からデプロイされた SQL Server イメージでは自動的に開かれます。 

1 つのサブネットのシナリオで[ロード バランサー](failover-cluster-instance-vnn-azure-load-balancer-configure.md)を使用する場合は、正常性プローブで使用されるポートも開く必要があります。 正常性プローブでは既定でポート 59999 を使用しますが、ロード バランサーの作成時に指定する任意の TCP ポートにすることもできます。 

次の表では、FCI の構成に応じて、開くことが必要になる場合があるポートについて詳しく説明します。 

   | 目的 | Port | Notes
   | ------ | ------ | ------
   | SQL Server | TCP 1433 | SQL Server の既定のインスタンスの通常のポートです。 ギャラリーからイメージを使用した場合、このポートが自動的に開きます。 </br> </br> **使用元**: すべての FCI 構成。 |
   | 正常性プローブ | TCP 59999 | 開いている任意の TCP ポートです。 このポートを使用するように、ロード バランサーの[正常性プローブ](failover-cluster-instance-vnn-azure-load-balancer-configure.md#configure-health-probe)とクラスターを構成します。 </br> </br> **使用元**: サブネットが 1 つの場合のロード バランサーを伴った FCI。 |
   | ファイル共有 | UDP 445 | ファイル共有サービスによって使用されるポート。 </br> </br> **使用元**: FCI と Premium ファイル共有。 |

## <a name="join-the-domain"></a>ドメインに参加する

また、仮想マシンをドメインに参加させる必要があります。 [クイックスタート テンプレート](../../../active-directory-domain-services/join-windows-vm-template.md#join-an-existing-windows-server-vm-to-a-managed-domain)を使用して、それを行うことができます。 

## <a name="review-storage-configuration"></a>ストレージの構成を確認する

Azure Marketplace から作成された仮想マシンには、アタッチされたストレージが付属しています。 Premium ファイル共有または Azure 共有ディスクを使用して FCI ストレージを構成する場合は、ローカル ストレージはフェールオーバー クラスター インスタンスに使用されないため、アタッチされたストレージを削除してコストを節約できます。 ただし、アタッチされたストレージは記憶域スペース ダイレクトの FCI ソリューションに使用できるので、その場合は削除しても役に立たない可能性があります。 お使いの FCI ストレージ ソリューションを確認して、アタッチされたストレージの削除がコストの節約に最適かどうかを判断します。 


## <a name="next-steps"></a>次のステップ

仮想マシン環境の準備ができたので、フェールオーバー クラスター インスタンスを構成できる状態になりました。 

次のいずれかのガイドを選択して、ビジネスに適した FCI 環境を構成します。 
- [Azure 共有ディスクを使用して FCI を構成する](failover-cluster-instance-azure-shared-disks-manually-configure.md)
- [Premium ファイル共有を使用して FCI を構成する](failover-cluster-instance-premium-file-share-manually-configure.md)
- [記憶域スペース ダイレクトを使用して FCI を構成する](failover-cluster-instance-storage-spaces-direct-manually-configure.md)


詳細については、以下をご覧ください。

- [Windows Server フェールオーバー クラスターと Azure VM 上の SQL Server](hadr-windows-server-failover-cluster-overview.md)
- [Azure VM 上の SQL Server を使用したフェールオーバー クラスター インスタンス](failover-cluster-instance-overview.md)
- [フェールオーバー クラスター インスタンスの概要](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
- [Azure VM 上の SQL Server に対する HADR 設定](hadr-cluster-best-practices.md)
