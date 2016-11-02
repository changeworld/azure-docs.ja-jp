<properties
    pageTitle="Site Recovery のしくみ | Microsoft Azure"
    description="この記事では、Site Recovery アーキテクチャの概要を説明します"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="cfreeman"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/13/2016"
    ms.author="raynew"/>


# <a name="how-does-azure-site-recovery-work?"></a>Azure Site Recovery のしくみ

この記事では、Azure Site Recovery サービスのアーキテクチャと、それを機能させるためのコンポーネントについて説明します。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で投稿してください。


## <a name="overview"></a>Overview

予定されたダウンタイムおよび予定外のダウンタイム時にワークロードおよびデータの実行と可用性を維持し、できるだけ早く通常の動作状態に復旧させる、ビジネス継続性および障害復旧 (BCDR) の戦略が必要です。 

Site Recovery は、BCDR 戦略に貢献する Azure サービスです。 Site Recovery では、クラウド (Azure) またはセカンダリ データセンターへのオンプレミスの物理サーバーと仮想マシンのレプリケーションを調整します。 プライマリ ロケーションで障害が発生した場合は、セカンダリ ロケーションにフェールオーバーしてアプリとワークロードの可用性を維持します。 プライマリの場所が通常の動作に戻ると、その場所にフェールバックします。 「[Site Recovery とは](site-recovery-overview.md)」で概要を確認してください。

## <a name="site-recovery-in-the-azure-portal"></a>Azure ポータルの Site Recovery

Azure には、リソースの作成と操作に関して、Azure Resource Manager モデルとクラシック サービス管理モデルという 2 種類の[デプロイメント モデル](../resource-manager-deployment-model.md)があります。 また、ポータルも 2 つあります。クラシック デプロイメント モデルをサポートする [Azure クラシック ポータル](https://manage.windowsazure.com/)と、両方のデプロイメント モデルをサポートする [Azure Portal](https://portal.azure.com) です。

- このクラシック ポータルと Azure ポータルの両方で Site Recovery を使用することができます。
- Azure クラシック ポータルでは、クラシック サービス管理モデルで Site Recovery をサポートできます。
- Azure Portal では、クラシック モデルまたは Resource Manager デプロイメント モデルをサポートできます。 

この記事の情報は、クラシック ポータルと Azure ポータルの両方のデプロイに当てはまります。 次の表は、違いをまとめたものです。

**レプリケート** | **Azure ポータル** | **クラシック ポータル**
--- | --- | ---
**Azure への VMware VM レプリケーション** | 簡略化されたデプロイ プロセス。<br/><br/> VM をクラシックまたは Resource Manager ベースのストレージにフェールオーバー。<br/><br/> クラシックまたは Resource Manager ベースのストレージにレプリケート。<br/><br/> フェールオーバー後は、Azure VM の接続にクラシックまたは Resource Manager ネットワークを使用。<br/><br/> LRS または GRS ストレージを使用。 | クラシック ストレージのみにフェールオーバー。<br/><br/> フェールオーバー後は、VM の接続にクラシック ネットワークのみを使用。<br/><br/> GRS ストレージを使用。
**Azure への Hyper-V VM レプリケーション (VMM なし)** | 簡略化されたデプロイ プロセス。<br/><br/> VM をクラシックまたは Resource Manager ベースのストレージにフェールオーバー。<br/><br/> クラシックまたは Resource Manager ベースのストレージにレプリケート。<br/><br/> フェールオーバー後は、Azure VM の接続にクラシックまたは Resource Manager ネットワークを使用。
**Azure への Hyper-V VM レプリケーション (VMM あり)** | 簡略化されたデプロイ プロセス。<br/><br/> VM をクラシックまたは Resource Manager ベースのストレージにフェールオーバー。<br/><br/> クラシックまたは Resource Manager ベースのストレージにレプリケート。<br/><br/> フェールオーバー後は、Azure VM の接続にクラシックまたは Resource Manager ネットワークを使用。<br/><br/> ネットワーク マッピングの設定が必要。 | クラシック ストレージのみにフェールオーバー。<br/><br/> フェールオーバー後は、VM の接続にクラシック ネットワークのみを使用。
**セカンダリ サイトへの Hyper-V VM レプリケーション (VMM あり)** | 簡略化されたデプロイ プロセス。<br/><br/> VM をクラシックまたは Resource Manager ベースのストレージにフェールオーバー。<br/><br/> クラシックまたは Resource Manager ベースのストレージにレプリケート。<br/><br/> フェールオーバー後は、Azure VM の接続にクラシックまたは Resource Manager ネットワークを使用。<br/><br/> ネットワーク マッピングの設定が必要。 | クラシック ストレージのみにフェールオーバー。<br/><br/> フェールオーバー後は、VM の接続にクラシック ネットワークのみを使用。<br/><br/> ストレージ マッピングの設定が可能。 <br/><br/> SAN レプリケーションは未サポート。



## <a name="deployment-scenarios"></a>デプロイメント シナリオ

Site Recovery は、次のようなさまざまなシナリオで、レプリケーションを調整するためにデプロイできます。

- **VMware VM をレプリケートする**: オンプレミスの VMware 仮想マシンを Azure Storage またはセカンダリ データセンターにレプリケートできます。
- **物理マシンをレプリケートする**: Windows または Linux を実行している物理マシンを Azure Storage またはセカンダリ データセンターにレプリケートできます。 物理マシンをレプリケートするためのプロセスは、VMware VM をレプリケートするためのプロセスとほとんど同じです。
- **Hyper-V VM をレプリケートする**: Hyper-V ホストが System Center VMM クラウドで管理されている場合、VM を Azure またはセカンダリ データセンターにレプリケートできます。 ホストが VMM で管理されていない場合は、Azure にしかレプリケートできません。 VMM で管理されていない Hyper-V VM を Azure Storage にレプリケートできます。
- **VM を移行する**: Site Recovery を使用して、リージョン間で [Azure IaaS VM を移行](site-recovery-migrate-azure-to-azure.md)したり、Azure IaaS VM に [AWS Windows インスタンスを移行](site-recovery-migrate-aws-to-azure.md)したりできます。 完全レプリケーションは、現在サポートされていません。 移行のためのレプリケーション (フェールオーバー) はできますが、フェールバックはできません。 

Site Recovery は、これらの VM と物理サーバーで実行されているほとんどのアプリをレプリケートできます。 サポートされるアプリの概要については、「 [Azure Site Recovery で保護できるワークロード](site-recovery-workload.md)


## <a name="replicate-vmware-virtual-machines-to-azure"></a>VMWare 仮想マシンを Azure にレプリケートします。

![拡張](./media/site-recovery-components/arch-enhanced.png)

**コンポーネント** | **詳細**
--- | ---
**Azure** | **アカウント**: Azure アカウントが必要です。<br/><br/> **ストレージ**: レプリケートしたデータを格納するには Azure ストレージ アカウントが必要です。 クラシック アカウントか Resource Manager ストレージ アカウントを使用できます。 Azure ポータルでデプロイする場合は、LRS と GRS のどちらのアカウントでも使用できます。 レプリケートされたデータは Azure Storage に格納され、フェールオーバーが発生すると、Azure VM はスピンアップされます。<br/><br/> **ネットワーク**: Azure VM がフェールオーバーで作成された場合に接続する Azure 仮想ネットワークが必要です。 Azure Portal では、ネットワークをクラシック サービス マネージャー モデルでも Resource Manager モデルでも作成できます。
**オンプレミスの構成サーバー** |  構成サーバーなどの Site Recovery コンポーネントを実行するオンプレミスの Windows Server 2012 R2 マシンが必要です。<br/><br/> 高可用性の VMware VM でなければなりません。<br/><br/> サーバーのコンポーネントは次のとおりです。<br/><br/> **構成サーバー**: オンプレミス環境と Azure との間の通信を調整し、データのレプリケーションと復旧を管理します。<br/><br/> **プロセス サーバー**: レプリケーション ゲートウェイとして機能します。 プロセス サーバーは保護されたソース マシンからレプリケーション データを受信し、キャッシュ、圧縮、および暗号化を使用して最適化してから、データを Azure Storage に送信します。 また、保護されたマシンへのモビリティ サービスのプッシュ インストールを処理し、VMware VM の自動検出を実行します。 デプロイの拡大に合わせて、増大するレプリケーション トラフィックの処理を実行する独立した専用プロセス サーバーを追加できます。<br/><br/> **マスター ターゲット サーバー**: Azure からのフェールバック中にレプリケーション データを処理します。
**オンプレミスの仮想化サーバー** | 1 つ以上の vSphere ホスト。 ホストの管理には vCenter サーバーもお勧めします。
**レプリケートする VM** | Azure にレプリケートする各 VMware VM に、モビリティ サービス コンポーネントがインストールされている必要があります。 このサービスによって、マシン上のデータの書き込みがキャプチャされ、プロセス サーバーに転送されます。 モビリティ サービス コンポーネントは、手動でインストールするか、マシンのレプリケーションを有効にするときにプロセス サーバーによって自動でプッシュしてインストールすることができます。

- [詳細情報](site-recovery-vmware-to-azure.md#azure-prerequisites) をご覧ください。
- [詳細情報](site-recovery-failback-azure-to-vmware.md) をご覧ください。


## <a name="replicate-physical-servers-to-azure"></a>物理サーバーを Azure にレプリケート

![拡張](./media/site-recovery-components/arch-enhanced.png)

**コンポーネント** | **詳細**
--- | ---
**Azure** | **アカウント**: Azure アカウントが必要です。<br/><br/> **ストレージ**: レプリケートしたデータを格納するには Azure ストレージ アカウントが必要です。 クラシック アカウントか Resource Manager ストレージ アカウントを使用できます。 Azure ポータルでデプロイする場合は、LRS と GRS のどちらのアカウントでも使用できます。 レプリケートされたデータは Azure Storage に格納され、フェールオーバーが発生すると、Azure VM はスピンアップされます。<br/><br/> **ネットワーク**: Azure VM がフェールオーバーで作成された場合に接続する Azure 仮想ネットワークが必要です。 Azure Portal では、ネットワークをクラシック サービス マネージャー モデルでも Resource Manager モデルでも作成できます。
**オンプレミスの構成サーバー** |  構成サーバーなどの Site Recovery コンポーネントを実行するオンプレミスの Windows Server 2012 R2 マシンが必要です。<br/><br/> サーバーは、仮想サーバーでも物理サーバーでもかまいません。<br/><br/> サーバーのコンポーネントは次のとおりです。<br/><br/> **構成サーバー**: オンプレミス環境と Azure との間の通信を調整し、データのレプリケーションと復旧を管理します。<br/><br/> **プロセス サーバー**: レプリケーション ゲートウェイとして機能します。 プロセス サーバーは保護されたソース マシンからレプリケーション データを受信し、キャッシュ、圧縮、および暗号化を使用して最適化してから、データを Azure Storage に送信します。 また、保護されたマシンへのモビリティ サービスのプッシュ インストールを処理し、VMware VM の自動検出を実行します。 デプロイメントの拡大に合わせて、増大するレプリケーション トラフィックの処理を実行する独立した専用プロセス サーバーを追加できます。<br/><br/> **マスター ターゲット サーバー**: Azure からのフェールバック中にレプリケーション データを処理します。
**レプリケートするマシン** | Azure にレプリケートする各物理 Windows/Linux に、モビリティ サービス コンポーネントがインストールされている必要があります。 このサービスによって、マシン上のデータの書き込みがキャプチャされ、プロセス サーバーに転送されます。 モビリティ サービス コンポーネントは、手動でインストールするか、マシンのレプリケーションを有効にするときにプロセス サーバーによって自動でプッシュしてインストールすることができます。
**フェールバック** | 物理マシン間のフェールバックはサポートされていません。 つまり、物理サーバーを Azure にフェールオーバーした後でフェールバックする場合は、VMware VM にフェールバックする必要があります。 物理サーバーにはフェールバックできません。 フェールバック先の Azure VM が必要になります。構成サーバーを VMware VM としてデプロイしていない場合は、別のマスター ターゲット サーバーを VMware VM としてセットアップする必要があります。 これは、マスター ターゲット サーバーが VMware ストレージとのやり取りと接続を通じてディスクを VMware VM に復元する必要があるために必要です。


- [詳細情報](site-recovery-vmware-to-azure.md#azure-prerequisites) をご覧ください。
- [詳細情報](site-recovery-failback-azure-to-vmware.md) をご覧ください。


## <a name="replicate-hyper-v-vms-not-managed-by-vmm-to-azure"></a>Azure への VMM で管理されていない Hyper-V VM のレプリケート

![Hyper-V サイトから Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

**コンポーネント** | **詳細**
--- | ---
**Azure** | **アカウント**: Azure アカウントが必要です。<br/><br/> **ストレージ**: レプリケートしたデータを格納するには Azure ストレージ アカウントが必要です。 クラシック アカウントか Resource Manager ストレージ アカウントを使用できます。 アカウントは GRS である必要があります。 レプリケートされたデータは Azure Storage に格納され、フェールオーバーが発生すると、Azure VM はスピンアップされます。<br/><br/> **ネットワーク**: Azure VM がフェールオーバーで作成された場合に接続する Azure 仮想ネットワークが必要です。
**Hyper-V ホスト/VM** | 1 つ以上の VM を実行している 1 つ以上の Hyper-V ホスト。<br/><br/> デプロイ中に、Site Recovery Provider と Recovery Services エージェントが各ホストにインストールされます。

- [詳細情報](site-recovery-hyper-v-site-to-azure.md#azure-prerequisites) をご覧ください。
- [詳細情報](site-recovery-hyper-v-site-to-azure-classic.md#azure-prerequisites) をご覧ください。



## <a name="replicate-hyper-v-vms-managed-by-vmm-to-azure"></a>Azure への VMM で管理されている Hyper-V VM のレプリケート


![VMM から Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

**コンポーネント** | **詳細**
--- | ---
**Azure** | **アカウント**: Azure アカウントが必要です。<br/><br/> **ストレージ**: レプリケートしたデータを格納するには Azure ストレージ アカウントが必要です。 クラシック アカウントか Resource Manager ストレージ アカウントを使用できます。 アカウントは GRS である必要があります。 レプリケートされたデータは Azure Storage に格納され、フェールオーバーが発生すると、Azure VM はスピンアップされます。<br/><br/> **ネットワーク**: Azure VM がフェールオーバーで作成された場合に接続する Azure 仮想ネットワークが必要です。
**VMM サーバー** | 1 つ以上のオンプレミス VMM サーバーと、1 つ以上のプライベート クラウドが必要です。 デプロイ中に、Site Recovery Provider が各サーバーにインストールされます。
**Hyper-V ホスト/VM** | 1 つ以上の VM を実行している 1 つ以上の Hyper-V ホスト。<br/><br/> デプロイ中に、Recovery Services エージェントが各ホストにインストールされます。


- [詳細情報](site-recovery-vmm-to-azure.md#azure-requirements) をご覧ください。
- [詳細情報](site-recovery-vmm-to-azure-classic.md#before-you-start) をご覧ください。




## <a name="replicate-vmware-virtual-machines-or-physical-server-to-a-secondary-site"></a>セカンダリ サイトへの VMware 仮想マシンまたは物理サーバーのレプリケート

![VMware から VMware](./media/site-recovery-components/vmware-to-vmware.png)


**コンポーネント** | **詳細**
--- | ---
**Azure** | **アカウント**: InMage Scout を使用してこのシナリオをデプロイします。 InMage Scout を入手するには、Azure アカウントが必要です。<br/><br/> Site Recovery コンテナーを作成した後で、InMage Scout をダウンロードし、最新の更新プログラムをインストールして、デプロイをセットアップします。
**プライマリ サイト** | **プロセス サーバー**: キャッシュ、圧縮、およびデータの最適化を処理できるように、プライマリ サイトでプロセス サーバーのコンポーネントをセットアップします。 プロセス サーバーでは、保護するマシンへの統合エージェントのプッシュ インストールも処理します。<br/><br/> **VMware ESX/ESXi と vCenter**: VMware EXS/ESXi ハイパーバイザーと、オプションとして VMware vCenter サーバーが必要です。<br/><br/> **VM** | デプロイ中に、保護するマシンに統合エージェントがインストールされます。 エージェントはすべてのコンポーネント間の通信プロバイダーとして機能します。
**セカンダリ サイト** | **構成サーバー**: 構成サーバーは最初にインストールするコンポーネントです。管理 Web サイトまたは vContinuum コンソールのどちらかを使用してデプロイを管理、構成、および監視できるようにセカンダリ サイトにインストールします。 デプロイメントに含まれる構成サーバーが 1 つのみなので、Windows Server 2012 R2 を実行するマシンにインストールする必要があります。<br/><br/> **vContinuum サーバー (セカンダリ サイト)**: 構成サーバーと同じ場所にインストールされます。 保護対象の環境を管理および監視するためのコンソールを提供します。 既定のインストールでは、vContinuum サーバーが 1 つ目のマスター ターゲット サーバーとなり、統合エージェントがインストールされます。<br/><br/> **マスター ターゲット サーバー**: マスター ターゲット サーバーは、レプリケートされたデータを保持します。 プロセス サーバーからデータを受信して、セカンダリ サイトにレプリカ マシンを作成し、データのリテンション期間ポイントを保持します。 必要のあるマスター ターゲット サーバーの数は、保護するマシンの数によって異なります。 プライマリ サイトにフェールバックする場合は、そこにもマスター ターゲット サーバーが必要です。


VMware VM または物理サーバーをセカンダリ サイトにレプリケートするには、Azure Site Recovery サブスクリプションに含まれている InMage Scout をダウンロードします。 Azure Portal または Azure クラシック ポータルからダウンロードできます。

各サイトでコンポーネント サーバー (構成、プロセス、マスター ターゲット) をセットアップし、レプリケートするマシンに統合エージェントをインストールします。 初期レプリケーションの後、各マシン上のエージェントによって、差分レプリケーションの変更がプロセス サーバーに送信されます。 プロセス サーバーは、このデータを最適化して、セカンダリ サイト上のマスター ターゲット サーバーに転送します。 構成サーバーでは、レプリケーション プロセスを管理します。


## <a name="replicate-hyper-v-vms-managed-by-vmm-to-a-secondary-site"></a>セカンダリ サイトへの VMM で管理されている Hyper-V VM のレプリケート

![オンプレミス間](./media/site-recovery-components/arch-onprem-onprem.png)

**コンポーネント** | **詳細**
--- | ---
**Azure** | **アカウント**: Azure アカウントが必要です。
**VMM サーバー** | プライマリ サイトに 1 つの VMM サーバーを配置し、セカンダリ サイトにもう 1 つの VMM サーバーを配置することをお勧めします。 各サーバーには、1 つ以上のプライベート クラウドが必要です。<br/><br/> デプロイ中に、Azure Site Recovery Provider を VMM サーバーにインストールします。
**Hyper-V ホスト/VM** | プライマリおよびセカンダリ サイトの VMM クラウドで実行されている 1 つ以上の Hyper-V ホスト<br/><br/> 各ホストには、レプリケートする VM が 1 つ以上あります。<br/><br/>. デプロイ中に、Recovery Services エージェントが各ホストにインストールされます。

- [詳細情報](site-recovery-vmm-to-vmm.md#azure-prerequisites) をご覧ください。
- [詳細情報](site-recovery-vmm-to-vmm-classic.md#before-you-start) をご覧ください。


## <a name="replicate-hyper-v-vms-managed-by-vmm-to-a-secondary-site-using-san-replication"></a>SAN レプリケーションを使用した、セカンダリ サイトへの VMM で管理されている Hyper-V VM のレプリケート

![SAN レプリケーション](./media/site-recovery-components/arch-onprem-onprem-san.png)

VMM クラウドで管理されている Hyper-V VM を、クラシック ポータルで SAN レプリケーションを使用してセカンダリ サイトにレプリケートできます。 このシナリオは現在、Azure Portal ではサポートされていません。

**コンポーネント** | **詳細**
--- | ---
**Azure** | **アカウント**: Azure アカウントが必要です。
**VMM サーバー** | プライマリ サイトに 1 つの VMM サーバーを配置し、セカンダリ サイトにもう 1 つの VMM サーバーを配置することをお勧めします。 各サーバーには、1 つ以上のプライベート クラウドが必要です。<br/><br/> デプロイ中に、Azure Site Recovery Provider を VMM サーバーにインストールします。
**SAN** | プライマリ VMM サーバーで管理されている、サポートされている SAN アレイ。<br/><br/> SAN は、セカンダリ サイトの別の SAN アレイとネットワーク インフラストラクチャを共有する必要があります。
**Hyper-V ホスト/VM** | プライマリおよびセカンダリ サイトの VMM クラウドで実行されている 1 つ以上の Hyper-V ホスト<br/><br/> 各ホストには、レプリケートする VM が 1 つ以上あります。<br/><br/>. デプロイ中に、Recovery Services エージェントが各ホストにインストールされます。

このシナリオでは、Site Recovery をデプロイする際に Azure Site Recovery Provider を VMM サーバーにインストールします。 Provider は、インターネット経由で Site Recovery サービスを使用してレプリケーションを調整および統制します。 プライマリ ストレージ アレイとセカンダリ ストレージ アレイとの間で同期 SAN レプリケーションによって、データがレプリケートされます。

[詳細情報](site-recovery-vmm-san.md#before-you-start) をご覧ください。

## <a name="hyper-v-protection-lifecycle"></a>Hyper-V 保護のライフ サイクル

次のワークフローでは、Hyper-V 仮想マシンを保護、レプリケート、およびフェールオーバーするプロセスを示します。

1. **保護の有効化**: Site Recovery コンテナーをセットアップし、VMM クラウドまたは Hyper-V サイトのレプリケーション設定を構成し、VM の保護を有効にします。 **保護の有効化**と呼ばれるジョブは、開始されると、**[ジョブ]** タブで監視することができます。 このジョブは、まずマシンが前提条件を満たしていることを確認します。次に、構成された設定を使用して Azure へのレプリケーションをセットアップする [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) メソッドを呼び出します。 **保護の有効化**ジョブは、[StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) メソッドも呼び出して、完全な VM レプリケーションを初期化します。
2. **初期レプリケーション**: 仮想マシンのスナップショットが取得され、仮想ハード ディスクが、Azure またはセカンダリ データセンターにすべてコピーされるまで、1 つずつレプリケートされます。 この所要時間は、VM サイズ、ネットワーク帯域幅、および初期レプリケーション方法によって異なります。 初期レプリケーションの進行中にディスクの変更が発生した場合、Hyper-V レプリカ レプリケーション トラッカーはそれらの変更を Hyper-V レプリケーション ログ (.hrl) として追跡します。ログは、ディスクと同じフォルダーに配置されます。 各ディスクには関連付けられた .hrl ファイルが存在し、これらはセカンダリ ストレージに送信されます。 初期レプリケーションの進行中は、スナップショットおよびログ ファイルによってディスク リソースが消費されるので注意してください。 初期レプリケーションが終了すると、VM のスナップショットは削除され、ログ内の差分ディスク変更が同期およびマージされます。
3. **保護の最終処理**: 初期レプリケーションが終了すると、**保護の最終処理**ジョブによって、ネットワークなどのレプリケーション後の設定が構成され、仮想マシンが保護されます。 Azure にレプリケートする場合は、仮想マシンの設定を微調整して、いつでもフェールオーバーできるようにしておくことが必要な場合があります。 この時点で、テスト フェールオーバーを実行して、すべてが想定通りに動作しているかを確認できます。
4. **レプリケーション**: 初期レプリケーション後は、レプリケーションの設定に従って差分の同期が開始されます。
    - **レプリケーションの失敗**: 差分レプリケーションに失敗した場合、完全なレプリケーションが帯域幅または時間の観点からコスト高になるときは、再同期が実行されます。 たとえば、.hrl ファイルがディスク サイズの 50% に達した場合、VM には再同期のマークが付けられます。 再同期では、ソースとターゲットの仮想マシンのチェックサムを計算して差分のみを送信することで、送信されるデータの量が最小限に抑えられます。 再同期が完了すると、差分レプリケーションが再開されます。 既定では再同期は業務時間外に自動的に実行するようにスケジュールされますが、手動で仮想マシンを再同期することもできます。
    - **レプリケーション エラー**: レプリケーション エラーが発生した場合に備えて、組み込み再試行があります。 認証エラーや承認エラーなど回復できないエラーである場合、またはレプリカ マシンが無効な状態にある場合、再試行は行われません。 ネットワーク エラーなどの回復可能なエラーの場合、またはディスクの空き領域やメモリが少ない場合は、再試行が実行されます。回数を重ねるごとに再試行間隔は長くなります (1、2、4、8、10 分と長くなり、その後は 30 分ごとに行われる)。
4. **予定されたフェールオーバーまたは予定外のフェールオーバー**: 必要に応じて、予定されたフェールオーバーまたは予定外のフェールオーバーを実行できます。 予定されたフェールオーバーを実行する場合、データが決して失われないように、ソース側の VM はシャット ダウンされます。 レプリカ VM は、作成後、コミット保留中の状態になります。 SAN を使用してレプリケートする場合 (自動的にコミットされる) を除き、レプリカ VM をコミットしてフェールオーバーを完了させる必要があります。 プライマリ サイトが起動され稼働状態になると、フェールバックが実行される可能性があります。 Azure にレプリケートした場合、レプリケーションの反転は自動的に行われます。 それ以外の場合は、レプリケーションの反転を手動で開始してください。


![ワークフロー](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## <a name="next-steps"></a>次のステップ

[デプロイメントの準備をする](site-recovery-best-practices.md)



<!--HONumber=Oct16_HO2-->


