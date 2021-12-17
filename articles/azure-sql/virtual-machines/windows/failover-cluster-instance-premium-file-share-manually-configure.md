---
title: Premium ファイル共有を使用して FCI を作成する
description: Premium ファイル共有 (PFS) を使用して、Azure Virtual Machines 上の SQL Server でフェールオーバー クラスター インスタンス (FCI) を作成します。
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
ms.openlocfilehash: 8d9e94ae12600223c6f54fb75cbc6c9648ecf7f9
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132156970"
---
# <a name="create-an-fci-with-a-premium-file-share-sql-server-on-azure-vms"></a>Premium ファイル共有を使用して FCI を作成する (Azure VM 上の SQL Server)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!TIP]
> 同じ Azure 仮想ネットワーク内の[複数のサブネット](failover-cluster-instance-prepare-vm.md#subnets)に SQL Server VM を作成することで、フェールオーバー クラスター インスタンスに対して Azure Load Balancer または分散ネットワーク名 (DNN) が不要になります。


この記事では、[Premium ファイル共有](../../../storage/files/storage-how-to-create-file-share.md)を使用して Azure Virtual Machines (VM) 上の SQL Server でフェールオーバー クラスター インスタンス (FCI) を作成する方法について説明します。

Premium ファイル共有は、SSD によってバックアップおよび提供される、継続的に待機時間の短いファイル共有であり、Windows Server (2012 以降) 上の SQL Server (2012 以降) のフェールオーバー クラスター インスタンスとの使用が完全にサポートされています。 Premium ファイル共有を使用すると、柔軟性が向上し、ダウンタイムなしでファイル共有のサイズを変更したり拡張したりすることができます。

詳細については、[Azure VM 上の SQL Server を使用した FCI](failover-cluster-instance-overview.md) および[クラスターのベスト プラクティス](hadr-cluster-best-practices.md)の概要に関する記事をご覧ください。 

> [!NOTE]
> これで Azure Migrate を使用して、フェールオーバー クラスター インスタンス ソリューションを Azure VM 上の SQL Server にリフト アンド シフトできるようになりました。 詳細については、[フェールオーバー クラスター インスタンスの移行](../../migration-guides/virtual-machines/sql-server-failover-cluster-instance-to-sql-on-azure-vm.md)に関するページを参照してください。 

## <a name="prerequisites"></a>前提条件

この記事に記載されている手順を完了するには、次のものが必要です。

- Azure サブスクリプション。
- Azure の仮想マシンと Active Directory の両方にオブジェクトを作成するためのアクセス許可を持つアカウント。
- [可用性セット](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set)内または異なる[可用性ゾーン](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address)内の [2 つ以上の準備済みの Windows Azure 仮想マシン](failover-cluster-instance-prepare-vm.md)。
- データ ファイル用のデータベースのストレージ クォータに基づいて、クラスター化されたドライブとして使用される [Premium ファイル共有](../../../storage/files/storage-how-to-create-file-share.md)。
- 最新バージョンの [PowerShell](/powershell/azure/install-az-ps)。 

## <a name="mount-premium-file-share"></a>Premium ファイル共有をマウントする

Premium ファイル共有をマウントするには、次の手順に従います。 

1. [Azure portal](https://portal.azure.com) にサインインします。 そして、ストレージ アカウントに移動します。
1. **[データ ストレージ]** の **[ファイル共有]** に移動し、SQL ストレージに使用する Premium ファイル共有を選択します。
1. **[接続]** を選択して、ファイル共有の接続文字列を表示します。
1. ドロップダウン リストで、使用するドライブ文字を選択し、認証方法として **ストレージ アカウント キー** を選択して、メモ帳などのテキスト エディターにコード ブロックをコピーします。

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/premium-file-storage-commands.png" alt-text="ファイル共有接続ポータルから PowerShell コマンドをコピーする":::

1. リモート デスクトップ プロトコル (RDP) を使用して、**SQL Server FCI がサービス アカウントに使用するアカウント** で SQL Server VM に接続します。
1. 管理 PowerShell コマンド コンソールを開きます。
1. 前にファイル共有ポータルからテキスト エディターにコピーしたコマンドを実行します。
1. エクスプローラーまたは **[ファイル名を指定して実行]** ダイアログ ボックス (キーボード上の Windows + R キー) を使用して、共有に移動します。 ネットワークパス `\\storageaccountname.file.core.windows.net\filesharename` を使用します。 たとえば、`\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare` のように指定します。
1. 新しく接続されたファイル共有に、SQL データ ファイルを配置するフォルダーを少なくとも 1 つ作成します。
1. クラスターに参加する各 SQL Server VM で、この手順を繰り返します。

  > [!IMPORTANT]
  > バックアップ ファイル用に別のファイル共有を使用して、この共有の 1 秒間の入出力操作数 (IOPS) と領域の容量をデータとログ ファイル用に確保することを検討してください。 バックアップ ファイルには、Premium または Standard のいずれのファイル共有も使用できます。

## <a name="create-windows-failover-cluster"></a>Windows フェールオーバー クラスターの作成

Windows Server フェールオーバー クラスターを作成する手順は、SQL Server VM を 1 つのサブネットにデプロイしたか、それとも複数のサブネットにデプロイしたかによって異なります。 クラスターを作成するには、[複数のサブネットの場合](availability-group-manually-configure-tutorial-multi-subnet.md#add-failover-cluster-feature)または [1 つのサブネットの場合](availability-group-manually-configure-tutorial-single-subnet.md#create-the-cluster)のチュートリアルの手順に従います。 これらのチュートリアルは可用性グループを作成するためのものですが、クラスターを作成する手順は同じです。 


## <a name="configure-quorum"></a>クォーラムを構成する

クラウド監視は、Azure VM 上の SQL Server 向けのこの種類のクラスター構成に推奨されるクォーラム ソリューションです。  

クラスターに多数の投票がある場合は、ビジネス ニーズに最適な[クォーラム ソリューション](hadr-cluster-quorum-configure-how-to.md)を構成します。 詳細については、[SQL Server VM でのクォーラム](hadr-windows-server-failover-cluster-overview.md#quorum)に関する記事をご覧ください。 

## <a name="validate-cluster"></a>クラスターを検証する

フェールオーバー クラスター マネージャーの UI または PowerShell を使用して、仮想マシンの 1 つでクラスターを検証します。

UI を使用してクラスターを検証するには、いずれかの仮想マシンで次の手順を実行します。

1. **[サーバー マネージャー]** で、 **[ツール]** を選択し、 **[フェールオーバー クラスター マネージャー]** を選択します。
1. **[フェールオーバー クラスター マネージャー]** で、 **[操作]** を選択し、 **[構成の検証]** を選択します。
1. **[次へ]** を選択します。
1. **[サーバーまたはクラスターの選択]** で、両方の仮想マシンの名前を入力します。
1. **[テスト オプション]** で、 **[選択するテストのみを実行する]** を選択します。 
1. **[次へ]** を選択します。
1. 次に示すように、 **[テストの選択]** で、 **[ストレージ]** と **[記憶域スペース ダイレクト]** を除くすべてのテストを選択します。

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/cluster-validation.png" alt-text="クラスター検証テストを選択する":::

1. **[次へ]** を選択します。
1. **[確認]** で、 **[次へ]** を選択します。 **構成の検証** ウィザードにより、検証テストが実行されます。


PowerShell を使用してクラスターを検証するには、いずれかの仮想マシンの管理者 PowerShell セッションから次のスクリプトを実行します。

```powershell
Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
```



## <a name="test-cluster-failover"></a>クラスターのフェールオーバーをテストする

クラスターのフェールオーバーをテストします。 **[フェールオーバー クラスター マネージャー]** で、クラスターを右クリックし、 **[その他の操作]**  >  **[コア クラスター リソースの移動]**  >  **[ノードの選択]** の順に選択した後、クラスターの他のノードを選択します。 コア クラスター リソースをクラスターのすべてのノードに移動してから、プライマリ ノードに戻します。 クラスターを各ノードに正常に移動できる場合は、SQL Server をインストールする準備ができています。  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="コア リソースを他のノードに移動して、クラスター フェールオーバーをテストする":::


## <a name="create-sql-server-fci"></a>SQL Server FCI を作成する

フェールオーバー クラスターを構成したら、SQL Server FCI を作成できます。

1. RDP を使用して最初の仮想マシンに接続します。

1. **[フェールオーバー クラスター マネージャー]** で、すべてのコア クラスター リソースが最初の仮想マシン上にあることを確認します。 必要に応じて、すべてのリソースをこの仮想マシンに移動します。

1. インストール メディアを探します。 仮想マシンでいずれかの Azure Marketplace イメージが使用されている場合、メディアは `C:\SQLServer_<version number>_Full` にあります。 

1. **[Setup]\(セットアップ\)** を選択します。

1. **[SQL Server インストール センター]** で、 **[インストール]** を選択します。

1. **[SQL Server フェールオーバー クラスターの新規インストール]** を選択し、ウィザードの指示に従って SQL Server FCI をインストールします。

1. **[クラスター ネットワークの構成]** ページで指定する IP は、SQL Server VM を 1 つのサブネットにデプロイしたのか、それとも複数のサブネットにデプロイしたのかによって異なります。 

   1. **単一サブネット環境** の場合、[Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) に追加する IP アドレスを指定します。
   1. **複数サブネット環境** の場合、[フェールオーバー クラスター インスタンスのネットワーク名の IP アドレス](failover-cluster-instance-prepare-vm.md#assign-secondary-ip-addresses)として以前に指定した、_最初の_ SQL Server VM のサブネット内のセカンダリ IP アドレスを指定します。

   :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/sql-install-cluster-network-secondary-ip-vm-1.png" alt-text="フェールオーバー クラスター インスタンスのネットワーク名の IP アドレスとして以前に指定した、最初の SQL Server VM のサブネット内のセカンダリ IP アドレスを指定する":::

1. **[データベース エンジンの構成]** で、データ ディレクトリは Premium ファイル共有に存在している必要があります。 共有の完全パスを `\\storageaccountname.file.core.windows.net\filesharename\foldername` の形式で入力します。 ファイル サーバーをデータ ディレクトリとして指定したことを通知する警告が表示されます。 この警告は想定されています。 ファイル共有を永続化したときに RDP 経由で VM にアクセスするために使用したユーザー アカウントが、潜在的なエラーを回避するために SQL Server サービスで使用されているのと確実に同じアカウントであるようにします。

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/use-file-share-as-data-directories.png" alt-text="ファイル共有を SQL データ ディレクトリとして使用する":::

1. ウィザードの手順を完了すると、セットアップにより、SQL Server FCI が最初のノードにインストールされます。

1. 最初のノードで FCI のインストールが成功したら、RDP を使用して 2 番目のノードに接続します。

1. **[SQL Server インストール センター]** を開き、 **[インストール]** を選択します。

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
   -LicenseType ???? -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>接続の構成

複数のサブネットに SQL Server VM をデプロイした場合は、この手順をスキップしてください。 1 つのサブネットに SQL Server VM をデプロイした場合は、トラフィックを FCI にルーティングするための追加コンポーネントを構成する必要があります。 フェールオーバー クラスター インスタンスに対して、仮想ネットワーク名 (VNN) と Azure Load Balancer、または分散ネットワーク名を構成できます。 [この 2 つの違いを確認](hadr-windows-server-failover-cluster-overview.md#virtual-network-name-vnn)してから、フェールオーバー クラスター インスタンスに対して[分散ネットワーク名](failover-cluster-instance-distributed-network-name-dnn-configure.md)または[仮想ネットワーク名と Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) をデプロイします。  

## <a name="limitations"></a>制限事項

- Microsoft 分散トランザクション コーディネーター (MSDTC) は、Windows Server 2016 以前ではサポートされていません。 
- Filestream は、Premium ファイル共有のフェールオーバー クラスターではサポートされていません。 Filestream を使用するには、[記憶域スペース ダイレクト](failover-cluster-instance-storage-spaces-direct-manually-configure.md)または [Azure 共有ディスク](failover-cluster-instance-azure-shared-disks-manually-configure.md)を代わりに使用して、クラスターをデプロイします。
- [軽量管理モード](sql-server-iaas-agent-extension-automate-management.md#management-modes)での SQL IaaS Agent 拡張機能への登録のみがサポートされています。 
- [スパース ファイルの制限のため Azure Files では](/rest/api/storageservices/features-not-supported-by-the-azure-file-service)、現在、データベース スナップショットはサポートされていません。
- データベース スナップショットはサポートされていないため、ユーザー データベースの CHECKDB は CHECKDB WITH TABLOCK に戻ります。 TABLOCK では実行されるチェックが制限されます。DBCC CHECKCATALOG はデータベースに対して実行されず、Service Broker データは検証されません。
- MASTER および MSDB データベースの CHECKDB はサポートされていません。 
- インメモリ OLTP 機能を使用するデータベースは、Premium ファイル共有を使用してデプロイされたフェールオーバー クラスター インスタンスではサポートされていません。 ビジネスでインメモリ OLTP が必要な場合は、代わりに [Azure 共有ディスク](failover-cluster-instance-azure-shared-disks-manually-configure.md)または[記憶域スペース ダイレクト](failover-cluster-instance-storage-spaces-direct-manually-configure.md)を使用して FCI をデプロイすることを検討してください。

## <a name="next-steps"></a>次のステップ

Premium ファイル共有がお客様に適した FCI 記憶域ソリューションでない場合は、代わりに [Azure 共有ディスク](failover-cluster-instance-azure-shared-disks-manually-configure.md)または[記憶域スペース ダイレクト](failover-cluster-instance-storage-spaces-direct-manually-configure.md)を使用して FCI を作成することを検討してください。 

詳細については、以下をご覧ください。

- [Windows Server フェールオーバー クラスターと Azure VM 上の SQL Server](hadr-windows-server-failover-cluster-overview.md)
- [Azure VM 上の SQL Server を使用したフェールオーバー クラスター インスタンス](failover-cluster-instance-overview.md)
- [フェールオーバー クラスター インスタンスの概要](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
- [Azure VM 上の SQL Server に対する HADR 設定](hadr-cluster-best-practices.md)

