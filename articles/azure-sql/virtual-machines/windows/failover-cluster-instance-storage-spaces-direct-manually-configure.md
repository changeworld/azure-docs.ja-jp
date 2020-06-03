---
title: SQL Server FCI - Azure Virtual Machines | Microsoft Docs
description: この記事では、Azure Virtual Machines で SQL Server フェールオーバー クラスター インスタンスを作成する方法について説明します。
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/11/2018
ms.author: mikeray
ms.openlocfilehash: 75c25454451b733870f8a674b292cd131454f4d2
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "84032313"
---
# <a name="configure-a-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>Azure Virtual Machines で SQL Server フェールオーバー クラスター インスタンスを構成します。
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

この記事では、Azure Resource Manager モデルを使用して、Azure Virtual Machines で SQL Server フェールオーバー クラスター インスタンス (FCI) を作成する方法について説明します。 このソリューションでは、ストレージ (データ ディスク) を Windows クラスター内のノード (Azure VM) 間で同期させるためのソフトウェア ベースの仮想 SAN として、[Windows Server 2016 Datacenter Edition 記憶域スペース ダイレクト](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)を使用します。 記憶域スペース ダイレクトは Windows Server 2016 で新しく追加されました。

次の図は、Azure Virtual Machines での完全なソリューションを示しています。

![完全なソリューション](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/00-sql-fci-s2d-complete-solution.png)

この図は次のことを示しています。

- Windows Server フェールオーバー クラスター内の 2 つの Azure 仮想マシン。 仮想マシンがフェールオーバー クラスター内にある場合、仮想マシンは*クラスター ノード*または*ノード*とも呼ばれます。
- 各仮想マシンには、2 つ以上のデータ ディスクがあります。
- 記憶域スペース ダイレクトは、データ ディスク上のデータを同期し、同期されたストレージを記憶域プールとして提供します。
- 記憶域プールは、クラスターの共有ボリューム (CSV) をフェールオーバー クラスターに提供します。
- SQL Server FCI のクラスター ロールは、CSV をデータ ドライブ用に使用します。
- SQL Server FCI の IP アドレスを保持するための Azure ロード バランサー。
- Azure 可用性セットにより、すべてのリソースが保持されます。

>[!NOTE]
>この図では、すべての Azure リソースが同じリソース グループに含まれています。

記憶域スペース ダイレクトの詳細については、[Windows Server 2016 Datacenter Edition 記憶域スペース ダイレクト](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)に関する記事を参照してください。

記憶域スペース ダイレクトでは、コンバージド型とハイパー コンバージド型の 2 種類のアーキテクチャがサポートされています。 このドキュメントでのアーキテクチャは、ハイパー コンバージド型です。 ハイパー コンバージド インフラストラクチャでは、クラスター化されたアプリケーションをホストしている同じサーバーにストレージが配置されます。 このアーキテクチャでは、ストレージは各 SQL Server FCI ノード上にあります。

## <a name="licensing-and-pricing"></a>ライセンスと価格

Azure Virtual Machines では、従量課金制 (PAYG) またはライセンス持ち込み (BYOL) VM イメージを使用して SQL Server の使用許可を与えることができます。 選択するイメージの種類が課金方法に影響を与えます。

従量課金制ライセンスでは、Azure Virtual Machines 上の SQL Server のフェールオーバー クラスター インスタンス (FCI) により、FCI のすべてのノード (パッシブ ノードを含む) に対する課金が発生します。 詳細については、「[SQL Server Enterprise Virtual Machines の料金](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/)」を参照してください。

ソフトウェア アシュアランスの Enterprise Agreement を締結している場合、アクティブ ノードごとに 1 つの無料のパッシブ FCI ノードを使用することができます。 Azure でこの利点を利用するには、BYOL VM イメージを使用した後、FCI のアクティブ ノードとパッシブ ノードの両方で同じライセンスを使用します。 詳細については、[Enterprise Agreement](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx) に関するページを参照してください。

SQL Server on Azure Virtual Machines の従量課金制ライセンスと BYOL ライセンスを比較するには、「[SQL VM の概要](sql-server-on-azure-vm-iaas-what-is-overview.md#get-started-with-sql-vms)」に関するページを参照してください。

SQL Server のライセンスに関する完全な情報については、[価格](https://www.microsoft.com/sql-server/sql-server-2017-pricing)に関するページを参照してください。

### <a name="example-azure-template"></a>Azure テンプレートの例

このソリューション全体を、Azure でテンプレートから作成できます。 テンプレートの例は、GitHub の[Azure クイック スタート テンプレート](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad)から使用できます。 この例は、特定のワークロード向けに設計およびテストされたものではありません。 このテンプレートを実行すれば、ドメインに接続された記憶域スペース ダイレクト ストレージで SQL Server FCI を作成できます。 このテンプレートは、評価のうえ、目的に応じた変更が可能です。

## <a name="before-you-begin"></a>開始する前に

作業を開始する前に、いくつかのことを把握し、準備しておく必要があります。

### <a name="what-to-know"></a>必要な知識
次のテクノロジについて、運用上の理解が必要です。

- [Windows クラスター テクノロジ](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server フェールオーバー クラスター インスタンス](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

留意すべき 1 つの点として、Azure IaaS VM ゲスト フェールオーバー クラスターでは、サーバー (クラスター ノード) ごとに 1 つの NIC、および 1 つのサブネットを使用することをお勧めします。 Azure ネットワークは物理的な冗長性を備えているので、Azure IaaS VM ゲスト クラスターで NIC とサブネットを追加する必要はありません。 クラスター検証レポートには、ノードは 1 つのネットワーク上でのみ到達可能であることを警告するメッセージが表示されます。 Azure IaaS VM ゲスト フェールオーバー クラスターでは、この警告を無視できます。

さらに、次のテクノロジの概要について理解しておくことが必要です。

- [Windows Server 2016 の記憶域スペース ダイレクトを使用したハイパー コンバージド ソリューション](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Azure リソース グループ](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> 現時点では、Azure 仮想マシン上の SQL Server フェールオーバー クラスター インスタンスは、[SQL Server IaaS Agent 拡張機能](sql-server-iaas-agent-extension-automate-management.md)の[軽量](sql-vm-resource-provider-register.md#management-modes)管理モードでのみサポートされています。 完全拡張機能モードから軽量に変更するには、対応する VM の **SQL 仮想マシン** リソースを削除し、それらを軽量モードの SQL VM リソース プロバイダーに登録します。 Azure portal を使用して **SQL 仮想マシン** リソースを削除するときに、**正しい仮想マシンの横のチェックボックスをオフにします**。 完全拡張機能では、自動バックアップ、パッチ適用、高度なポータル管理などの機能がサポートされます。 エージェントを軽量管理モードで再インストールすると、これらの機能は SQL VM で動作しなくなります。

### <a name="what-to-have"></a>必要なもの

この記事の手順を完了するには、次のものが必要です。

- Microsoft Azure サブスクリプションが必要です。
- Azure Virtual Machines 上の Windows ドメイン。
- Azure の仮想マシンと Active Directory の両方にオブジェクトを作成するためのアクセス許可を持つアカウント。
- 次のコンポーネント用の十分な IP アドレス空間を持つ Azure 仮想ネットワークとサブネット。
   - 両方の仮想マシン。
   - フェールオーバー クラスターの IP アドレス。
   - 各 FCI の IP アドレス。
- Azure ネットワーク上で構成された、ドメイン コントローラーを指す DNS。

これらの前提条件が整ったら、フェールオーバー クラスターの構築を開始できます。 最初の手順で、仮想マシンを作成します。

## <a name="step-1-create-the-virtual-machines"></a>手順 1:仮想マシンの作成

1. サブスクリプションで [Azure portal](https://portal.azure.com) にサインインします。

1. [Azure 可用性セットを作成します](../../../virtual-machines/linux/tutorial-availability-sets.md)。

   可用性セットを利用すると、障害ドメインと更新ドメインの仮想マシンがグループ化されます。 これにより、アプリケーションがネットワーク スイッチ、サーバーのラックの電源装置など、単一障害点の影響を受けないようにすることができます。

   仮想マシンのリソース グループを作成していない場合、これは Azure 可用性セットの作成時に行います。 可用性セットの作成に Azure Portal を使用する場合は、次の手順を実行します。

   1. Azure portal で、 **[リソースの作成]** を選択して Azure Marketplace を開きます。 "**可用性セット**" を検索します。
   1. **[可用性セット]** を選択します。
   1. **［作成］** を選択します
   1. **[可用性セットの作成]** で、次の値を指定します。
      - **Name**:可用性セットの名前。
      - **サブスクリプション**:Azure サブスクリプション。
      - **[リソース グループ]** :既存のグループを使用する場合は、 **[既存のものを選択]** をクリックし、リストからグループを選択します。 または、 **[新規作成]** を選択し、グループの名前を入力します。
      - **[場所]** :仮想マシンを作成する予定の場所を設定します。
      - **[障害ドメイン]** : 既定値 (**3**) を使用します。
      - **[更新ドメイン]** : 既定値 (**5**) を使用します。
   1. **[作成]** を選択して可用性セットを作成します。

1. 可用性セット内に仮想マシンを作成します。

   Azure 可用性セット内に 2 つの SQL Server 仮想マシンをプロビジョニングします。 手順については、「[Azure Portal での SQL Server 仮想マシンのプロビジョニング](create-sql-vm-portal.md)」を参照してください。

   両方の仮想マシンを配置します。

   - 可用性セットと同じ Azure リソース グループ内。
   - ドメイン コントローラーと同じネットワーク内。
   - 両方の仮想マシン、およびこのクラスター上で最終的に使用するすべての FCI 用に十分な IP アドレス空間を持つサブネット内。
   - Azure 可用性セット内。

      >[!IMPORTANT]
      >仮想マシンを作成した後に可用性セットを設定または変更することはできません。

   Azure Marketplace からイメージを選択します。 Windows Server と SQL Server、または Windows Server だけを含む Azure Marketplace イメージを使用できます。 詳細については、「[Azure Virtual Machines における SQL Server の概要](sql-server-on-azure-vm-iaas-what-is-overview.md)」に関する記事を参照してください。

   Azure ギャラリーの SQL Server の公式イメージには、インストール済みの SQL Server インスタンス、SQL Server のインストール ソフトウェア、必要なキーが含まれます。

   SQL Server ライセンスのご希望の課金方法に基づき、適切なイメージを選択します。

   - **従量課金制ライセンス**。 これらのイメージの 1 秒あたりのコストには、SQL Server ライセンスが含まれます。
      - **SQL Server 2016 Enterprise on Windows Server 2016 Datacenter**
      - **SQL Server 2016 Standard on Windows Server 2016 Datacenter**
      - **SQL Server 2016 Developer on Windows Server 2016 Datacenter**

   - **ライセンス持ち込み (BYOL)**

      - **(BYOL) SQL Server 2016 Enterprise on Windows Server 2016 Datacenter**
      - **(BYOL) SQL Server 2016 Standard on Windows Server 2016 Datacenter**

   >[!IMPORTANT]
   >仮想マシンを作成した後、事前にインストールされていたスタンドアロンの SQL Server インスタンスを削除します。 フェールオーバー クラスターと記憶域スペース ダイレクトを設定したら、プレインストールされた SQL Server メディアを使用して SQL Server FCI を作成します。

   または、オペレーティング システムだけを含む Azure Marketplace イメージを使用することができます。 フェールオーバー クラスターと記憶域スペース ダイレクトの設定後、**Windows Server 2016 Datacenter** イメージを選択し、SQL Server FCI をインストールします。 このイメージには、SQL Server インストール メディアは含まれません。 各サーバーに対して実行できる場所に、SQL Server インストール メディアを配置します。

1. Azure で仮想マシンが作成されたら、RDP で各仮想マシンに接続します。

   RDP を使用して最初に仮想マシンに接続する際、この PC をネットワーク上で検出可能にするかどうかを確認するメッセージが表示されます。 **[はい]** を選択します。

1. SQL Server ベースの仮想マシン イメージの 1 つを使用している場合は、SQL Server インスタンスを削除します。

   1. **[プログラムと機能]** で **[Microsoft SQL Server 2016 (64 ビット)]** を右クリックし、 **[アンインストールと変更]** を選択します。
   1. **[削除]** を選択します。
   1. 既定のインスタンスを選択します。
   1. **[データベース エンジン サービス]** のすべての機能を削除します。 **[共有機能]** は削除しないでください。 次のスクリーンショットのように表示されます。

      ![機能を選択する](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/03-remove-features.png)

   1. **[次へ]** 、 **[削除]** の順に選択します。

1. <a name="ports"></a>ファイアウォール ポートを開きます。

   各仮想マシンで、Windows ファイアウォールの次のポートを開きます。

   | 目的 | TCP ポート | Notes
   | ------ | ------ | ------
   | SQL Server | 1433 | SQL Server の既定のインスタンスの通常のポートです。 ギャラリーからイメージを使用した場合、このポートが自動的に開きます。
   | 正常性プローブ | 59999 | 開いている任意の TCP ポートです。 後の手順で、このポートを使用するようにロード バランサーの[正常性プローブ](#probe)とクラスターを構成します。  

1. 仮想マシンにストレージを追加します。 詳細については、[ストレージの追加](../../../virtual-machines/linux/disks-types.md)に関するページを参照してください。

   両方の仮想マシンに、少なくとも 2 つのデータ ディスクが必要です。

   NTFS でフォーマットされたディスクではなく、フォーマットされていないディスクを接続します。
      >[!NOTE]
      >NTFS でフォーマットされたディスクを接続する場合、ディスクの適格性チェックをしない記憶域スペース ダイレクトしか有効にできません。  

   最小で 2 つの Premium SSD を各 VM に接続します。 少なくとも P30 (1 TB) のディスクをお勧めします。

   [ホスト キャッシュ] を **[読み取り専用]** に設定します。

   運用環境で使用するストレージ容量はワークロードによって異なります。 この記事で説明されている値は、デモおよびテスト用です。

1. [既存のドメインに仮想マシンを追加します](availability-group-manually-configure-prerequisites-tutorial.md#joinDomain)。

仮想マシンを作成して構成したら、フェールオーバー クラスターを設定できます。

## <a name="step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct"></a>手順 2:記憶域スペース ダイレクトが有効な Windows Server フェールオーバー クラスターを構成する

次に、記憶域スペース ダイレクトが有効なフェールオーバー クラスターを構成します。 この手順では、以下のサブ手順を実行します。

1. Windows Server フェールオーバー クラスタリング機能を追加する。
1. クラスターを検証する。
1. フェールオーバー クラスターを作成する。
1. クラウド監視を作成する。
1. ストレージを追加する。

### <a name="add-windows-server-failover-clustering"></a>Windows Server フェールオーバー クラスタリングを追加する

1. ローカル管理者のメンバーであり、かつ Active Directory でオブジェクトを作成するアクセス許可を持つドメイン アカウントを使用して、RDP で最初の仮想マシンに接続する。 このアカウントを使用して、構成を進めます。

1. [各仮想マシンにフェールオーバー クラスタリングを追加します](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms)。

   UI からフェールオーバー クラスタリングをインストールするには、両方の仮想マシンで次の手順を実行します。
   1. **[サーバー マネージャー]** で、 **[管理]** 、 **[役割と機能の追加]** の順に選択します。
   1. **[役割と機能の追加ウィザード]** で、 **[機能の選択]** ページが表示されるまで **[次へ]** を選択します。
   1. **[機能の選択]** で **[フェールオーバー クラスタリング]** を選択します。 必要なすべての機能と管理ツールを含めます。 **[機能の追加]** を選択します。
   1. **[次へ]** を選択し、 **[完了]** を選択して、機能をインストールします。

   PowerShell を使用してフェールオーバー クラスタリングをインストールするには、いずれかの仮想マシン上の管理者 PowerShell セッションから次のスクリプトを実行します。

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

次の手順の詳しい参考資料については、「[Windows Server 2016 で記憶域スペース ダイレクトを使用するハイパーコンバージド ソリューション](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct)」の手順 3. の説明を参照してください。

### <a name="validate-the-cluster"></a>クラスターを検証する

UI または PowerShell を使用して、クラスターを検証します。

UI を使用してクラスターを検証するには、いずれかの仮想マシンから次の手順を実行します。

1. **[サーバー マネージャー]** で、 **[ツール]** を選択し、 **[フェールオーバー クラスター マネージャー]** を選択します。
1. **[フェールオーバー クラスター マネージャー]** で、 **[操作]** を選択し、 **[構成の検証]** を選択します。
1. **[次へ]** を選択します。
1. **[サーバーまたはクラスターの選択]** で、両方の仮想マシンの名前を入力します。
1. **[テスト オプション]** で、 **[選択するテストのみを実行する]** を選択します。 **[次へ]** を選択します。
1. 次に示すように、 **[テストの選択]** で、 **[ストレージ]** を除くすべてのテストを選択します。

   ![クラスター検証テストを選択する](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/10-validate-cluster-test.png)

1. **[次へ]** を選択します。
1. **[確認]** で、 **[次へ]** を選択します。

構成の検証ウィザードにより、検証テストが実行されます。

PowerShell を使用してクラスターを検証するには、いずれかの仮想マシンの管理者 PowerShell セッションから次のスクリプトを実行します。

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

クラスターの検証後、フェールオーバー クラスターを作成します。

### <a name="create-the-failover-cluster"></a>フェールオーバー クラスターを作成する

フェールオーバー クラスターを作成するには、以下が必要です。
- クラスター ノードになる仮想マシンの名前。
- フェールオーバー クラスターの名前。
- フェールオーバー クラスターの IP アドレス。 クラスター ノードと同じ Azure 仮想ネットワークおよびサブネットでは使用されていない IP アドレスを使用することができます。

#### <a name="windows-server-2008-through-windows-server-2016"></a>Windows server 2008 から Windows Server 2016

次の PowerShell スクリプトは、Windows Server 2008 から Windows Server 2016 用のフェールオーバー クラスターを作成します。 ノード名 (仮想マシン名) と、Azure VNET の使用可能な IP アドレスでスクリプトを更新してください。

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

次の PowerShell スクリプトは、Windows Server 2019 用のフェールオーバー クラスターを作成します。 詳細については、「[フェールオーバー クラスター:クラスター ネットワーク オブジェクト](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97)」に関するセクションを確認してください。 ノード名 (仮想マシン名) と、Azure VNET の使用可能な IP アドレスでスクリプトを更新してください。

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>クラウド監視を作成する

クラウド監視とは、Azure Storage Blob に格納されている、新しい種類のクラスター クォーラム監視です。 これにより、監視共有をホストする別個の VM が不要になります。

1. [フェールオーバー クラスターのクラウド監視を作成](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness)します。

1. BLOB コンテナーを作成します。

1. アクセス キーと、コンテナーの URL を保存します。

1. フェールオーバー クラスターのクォーラム監視を構成します。 「[ユーザー インターフェイスでクォーラム監視を構成する方法](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness)」に関するページを参照してください。

### <a name="add-storage"></a>ストレージを追加する

記憶域スペース ダイレクト用のディスクは空にする必要があります。 パーティションまたはその他のデータを含めることはできません。 ディスクを消去するには、[このガイドの手順](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct?redirectedfrom=MSDN#step-31-clean-drives)に従います。

1. [記憶域スペース ダイレクトを有効化します](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct)。

   次の PowerShell スクリプトを実行すると、記憶域スペース ダイレクトが有効化されます。  

   ```powershell
   Enable-ClusterS2D
   ```

   **[フェールオーバー クラスター マネージャー]** に、記憶域プールが表示されるようになります。

1. [ボリュームを作成します](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes)。

   記憶域スペース ダイレクトは、記憶域プールをユーザーが有効化した場合に、記憶域プールを自動的に作成します。 これでボリュームを作成する準備が整いました。 PowerShell コマンドレット `New-Volume` は、ボリュームの作成プロセスを自動化します。 このプロセスには、書式設定、クラスターへのボリュームの追加、およびクラスターの共有ボリューム (CSV) の作成が含まれます。 次の例では、800 ギガバイト (GB) の CSV を作成します。

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   このコマンドが完了すると、800 GB のボリュームがクラスター リソースとしてマウントされます。 ボリュームは `C:\ClusterStorage\Volume1\` にあります。

   このスクリーンショットは、記憶域スペース ダイレクトが有効なクラスターの共有ボリュームを示しています。

   ![クラスターの共有ボリューム](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>手順 3:クラスター フェールオーバーのテスト フェールオーバーを実行する

**フェールオーバー クラスター マネージャー**で、ストレージ リソースを他のクラスター ノードに移動できることを確認します。 **フェールオーバー クラスター マネージャー**を使用してフェールオーバー クラスターに接続し、ノード間でストレージを移動できれば、FCI を構成することができます。

## <a name="step-4-create-the-sql-server-fci"></a>手順 4:SQL Server FCI を作成する

フェールオーバー クラスターと、ストレージを含むすべてのクラスター コンポーネントを構成したら、SQL Server FCI を作成できます。

1. RDP を使用して最初の仮想マシンに接続します。

1. **フェールオーバー クラスター マネージャー**で、すべてのコア クラスター リソースが最初の仮想マシン上にあることを確認します。 必要に応じて、すべてのリソースをこの仮想マシンに移動します。

1. インストール メディアを探します。 仮想マシンでいずれかの Azure Marketplace イメージが使用されている場合、メディアは `C:\SQLServer_<version number>_Full` にあります。 **[Setup]\(セットアップ\)** を選択します。

1. **[SQL Server インストール センター]** で、 **[インストール]** を選択します。

1. **[SQL Server フェールオーバー クラスターの新規インストール]** を選択します。 ウィザードの指示に従って、SQL Server FCI をインストールします。

   FCI のデータ ディレクトリは、クラスター化されたストレージ上にある必要があります。 記憶域スペース ダイレクトでは、これは共有ディスクではなく、各サーバー上のボリュームのマウント ポイントです。 記憶域スペース ダイレクトは、両方のノード間でボリュームを同期します。 ボリュームは、クラスターの共有ボリュームとしてクラスターに表示されます。 データ ディレクトリとして CSV のマウント ポイントを使用します。

   ![データ ディレクトリ](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/20-data-dicrectories.png)

1. ウィザードの手順を完了すると、セットアップにより、SQL Server FCI が最初のノードにインストールされます。

1. セットアップで FCI が最初のノードにインストールされたら、RDP を使用して 2 番目のノードに接続します。

1. **[SQL Server インストール センター]** を開きます。 **[インストール]** を選択します。

1. **[SQL Server フェールオーバー クラスターにノードを追加]** を選択します。 ウィザードの指示に従って SQL Server をインストールし、このサーバーを FCI に追加します。

   >[!NOTE]
   >SQL Server を含む Azure Marketplace ギャラリー イメージを使用した場合、SQL Server のツールはイメージに含まれています。 これらのいずれかのイメージを使用しなかった場合、SQL Server のツールは別途インストールしてください。 「[SQL Server Management Studio (SSMS) のダウンロード](https://msdn.microsoft.com/library/mt238290.aspx)」を参照してください。

## <a name="step-5-create-the-azure-load-balancer"></a>手順 5:Azure ロード バランサーを作成する

Azure 仮想マシンでは、クラスターは、一度に 1 つのクラスター ノードに存在する必要がある IP アドレスを保持するためにロード バランサーを使用します。 このソリューションでは、ロード バランサーは SQL Server FCI の IP アドレスを保持します。

詳細については、「[Azure のロード バランサーの作成と構成](availability-group-manually-configure-tutorial.md#configure-internal-load-balancer)」に関する記事を参照してください。

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Azure Portal でロード バランサーを作成する

ロード バランサーを作成するには、次の手順を実行します。

1. Azure Portal で、仮想マシンが含まれているリソース グループに移動します。

1. **[追加]** を選択します。 Azure Marketplace で "**ロード バランサー**" を検索します。 **[ロード バランサー]** を選択します。

1. **［作成］** を選択します

1. 次の項目を入力して、ロード バランサーを構成します。

   - **サブスクリプション**:Azure サブスクリプション。
   - **[リソース グループ]** :仮想マシンが含まれているリソース グループ。
   - **Name**:ロード バランサーを識別する名前。
   - **[リージョン]** :仮想マシンが含まれている Azure の場所。
   - **[種類]** :パブリックまたはプライベート。 プライベート ロード バランサーには、同じ仮想ネットワーク内からアクセスできます。 プライベート ロード バランサーは、ほとんどの Azure アプリケーションで使用できます。 アプリケーションがインターネット経由で直接 SQL Server にアクセスする必要がある場合は、パブリック ロード バランサーを使用します。
   - **SKU**:Standard。
   - **仮想ネットワーク**:仮想マシンと同じネットワーク。
   - **[IP アドレスの割り当て]** :静的。 
   - **[プライベート IP アドレス]** :SQL Server FCI クラスターのネットワーク リソースに割り当てた IP アドレス。

 次のスクリーンショットは **[ロード バランサーの作成]** の UI を示しています。

   ![ロードバランサーを設定する](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>ロード バランサーのバックエンド プールを構成する

1. 仮想マシンが含まれている Azure リソース グループに戻り、新しいロード バランサーを探します。 リソース グループの表示を更新することが必要な場合もあります。 ロード バランサーを選択します。

1. **[バックエンド プール]** を選択し、 **[追加]** を選択します。

1. VM を含む可用性セットにバックエンド プールを関連付けます。

1. **[ターゲット ネットワーク IP 構成]** で、 **[仮想マシン]** を選択し、クラスター ノードとして参加する仮想マシンを選びます。 必ず、FCI をホストするすべての仮想マシンを含めます。

1. **[OK]** を選択して、バックエンド プールを作成します。

### <a name="configure-a-load-balancer-health-probe"></a>ロード バランサーの正常性プローブを構成する

1. [ロード バランサー] ブレードで **[正常性プローブ]** を選択します。

1. **[追加]** を選択します。

1. **[正常性プローブの追加]** ブレードで、<a name="probe"></a>正常性プローブのパラメーターを設定します。

   - **Name**:正常性プローブの名前。
   - **プロトコル**:TCP
   - **ポート**:[こちらの手順](#ports)で正常性プローブ用にファイアウォールで作成したポートに設定します。 この記事の例では、TCP ポート `59999` を使用します。
   - **間隔**: 5 秒
   - **[異常のしきい値]** : 連続エラー数 2。

1. **[OK]** を選択します。

### <a name="set-load-balancing-rules"></a>負荷分散規則を設定する

1. [ロード バランサー] ブレードで、 **[負荷分散規則]** を選択します。

1. **[追加]** を選択します。

1. 次のように負荷分散規則のパラメーターを設定します。

   - **Name**:負荷分散規則の名前。
   - **[フロントエンド IP アドレス]** : SQL Server FCI クラスターのネットワーク リソースの IP アドレス。
   - **ポート**:SQL Server FCI の TCP ポート。 既定のインスタンス ポートは 1433 です。
   - **[バックエンド ポート]** : **[フローティング IP (ダイレクト サーバー リターン)]** を有効にしたときの **[ポート]** の値と同じポートを使用します。
   - **[バックエンド プール]** : 先ほど構成したバックエンド プール名。
   - **[正常性プローブ]** : 先ほど構成した正常性プローブ。
   - **[セッション永続化]** : [なし] :
   - **[アイドル タイムアウト (分)]** : 4.
   - **[フローティング IP]\(ダイレクト サーバー リターン\)** : 有効。

1. **[OK]** を選択します。

## <a name="step-6-configure-the-cluster-for-the-probe"></a>手順 6:プローブのクラスターを構成する

PowerShell でクラスターのプローブ ポート パラメーターを設定します。

クラスターのプローブ ポート パラメーターを設定するには、使用環境の値を使用して、次のスクリプトで変数を更新します。 スクリプトから山かっこ (`<` および `>`) を削除します。

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

次の一覧では、更新する必要がある値について説明します。

   - `<Cluster Network Name>`:ネットワークの Windows Server フェールオーバー クラスターの名前。 **[フェールオーバー クラスター マネージャー]**  >  **[ネットワーク]** で、ネットワークを右クリックして **[プロパティ]** を選択します。 正しい値は **[全般]** タブの **[名前]** にあります。

   - `<SQL Server FCI IP Address Resource Name>`:SQL Server FCI IP アドレス リソースの名前。 **[フェールオーバー クラスター マネージャー]**  >  **[Roles]\(ロール\)** で、SQL Server FCI ロールの **[サーバー名]** の下にある IP アドレス リソースを右クリックして **[プロパティ]** を選択します。 正しい値は **[全般]** タブの **[名前]** にあります。 

   - `<ILBIP>`:ILB の IP アドレス。 このアドレスは、ILB のフロント エンド アドレスとして Azure Portal で構成されます。 これは、SQL Server FCI の IP アドレスでもあります。 **[フェールオーバー クラスター マネージャー]** の `<SQL Server FCI IP Address Resource Name>` がある同じプロパティ ページで見つけることができます。  

   - `<nnnnn>`:ロード バランサーの正常性プローブで構成したプローブ ポート。 未使用の TCP ポートが有効です。

>[!IMPORTANT]
>クラスター パラメーターのサブネット マスクは、TCP IP ブロードキャスト アドレス (`255.255.255.255`) である必要があります。

クラスターのプローブを設定したら、PowerShell ですべてのクラスター パラメーターを確認できます。 このスクリプトを実行します。

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>手順 7:FCI フェールオーバーをテストする

FCI のフェールオーバーをテストして、クラスターの機能を検証します。 次の手順を実行します。

1. SQL Server FCI クラスターのノードの 1 つに RDP を使用して接続します。

1. **フェールオーバー クラスター マネージャー**を開きます。 **[役割]** を選びます。 SQL Server FCI ロールを所有しているノードを確認します。

1. SQL Server FCI ロールを右クリックします。

1. **[移動]** を選択し、 **[最適なノード]** を選択します。

**フェールオーバー クラスター マネージャー**で、ロールとそのリソースがオフラインになったことが示されます。 リソースは移動し、もう一方のノードでオンラインになります。

### <a name="test-connectivity"></a>接続をテストする

接続をテストするには、同じ仮想ネットワーク内の別の仮想マシンにサインインします。 **SQL Server Management Studio** を開き、SQL Server FCI 名に接続します。

>[!NOTE]
>必要に応じて、[SQL Server Management Studio をダウンロード](https://msdn.microsoft.com/library/mt238290.aspx)できます。

## <a name="limitations"></a>制限事項

Azure Virtual Machines では、クラスター共有ボリューム (CSV) および [Standard Load Balancer](../../../load-balancer/load-balancer-standard-overview.md) 上のストレージを備えた Windows Server 2019 で、 Microsoft 分散トランザクション コーディネーター (MSDTC) をサポートします。

Azure Virtual Machines では、次の理由から、MSDTC は Windows Server 2016 以前ではサポートされていません。

- クラスター化された MSDTC リソースは、共有ストレージを使用するように構成することはできません。 Windows Server 2016 では、MSDTC リソースを作成した場合、ストレージが使用可能であっても、使用可能な共有ストレージは 1 つも表示されません。 この問題は、Windows Server 2019 で修正済みです。
- Basic Load Balance は、RPC ポートを処理しません。

## <a name="see-also"></a>関連項目

[リモート デスクトップ (Azure) を使用して記憶域スペース ダイレクトを設定する](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[記憶域スペース ダイレクトを使用するハイパーコンバージド ソリューション](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)

[記憶域スペース ダイレクトの概要](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[記憶域スペース ダイレクトの SQL Server サポート](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)
