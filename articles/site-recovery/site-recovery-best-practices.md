<properties
	pageTitle="Site Recovery のデプロイの準備 | Microsoft Azure"
	description="この記事では、Azure Site Recovery を使用してレプリケーションをデプロイする準備を整える方法について説明します。"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="03/08/2016"
	ms.author="raynew"/>

# Azure Site Recovery のデプロイの準備

Azure Site Recovery サービスは、仮想マシンと物理サーバーのレプリケーション、フェールオーバー、復旧を調整してビジネス継続性と障害復旧 (BCDR) 戦略に貢献します。コンピューターを Azure に、またはオンプレミスのセカンダリ データ センターにレプリケートできます。簡単な概要については、「[Azure Site Recovery とは](site-recovery-overview.md)」を参照してください。

## 概要

Azure Site Recovery は、VMware VM、Hyper-V VM、および物理サーバーの Azure またはセカンダリ データセンターに対するレプリケーションをサポートしています。この記事では、各レプリケーション シナリオに対する Azure Site Recovery デプロイメントの準備方法について説明します。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で投稿してください。

## Hyper-V レプリケーションのデプロイメント要件

次の表は、Hyper-V を (VMM ありまたはなしで) Azure とセカンダリ サイトにレプリケートするための一般的なデプロイメントの要件をまとめたものです。各レプリケーション シナリオの一般的な要件を理解し、比較するために利用してください。また、詳細なデプロイメントの前提条件へのリンクも紹介します。

**Azure にレプリケート (VMM を使用)** | **Azure にレプリケート (VMM を使用しない)** | **セカンダリ サイトにレプリケート (VMM を使用)**
---|---|---
**VMM**: System Center 2012 R2 で実行されている、少なくとも 1 つの VMM サーバー。VMM サーバーには、1 つ以上の VMM ホスト グループを含むクラウドが少なくとも 1 つある必要があります。<br/><br/> **Hyper-V**: Windows Server 2012 R2 以降を実行している、オンプレミスのデータ センター内の 1 つ以上の Hyper-V ホスト サーバー。Hyper-V サーバーは、VM クラウド内のホスト グループに属している必要があります。<br/><br/> **仮想マシン**: ソース Hyper-V サーバーの少なくとも 1 つの VM に必要です。Azure にレプリケートする VM は [Azure の仮想マシンの前提条件](#azure-virtual-machine-requirements)を満たす必要があります。<br/><br/> **Azure アカウント**: [Azure](https://azure.microsoft.com/) アカウントとサブスクリプションが必要です。<br/><br/> **Azure Storage**: レプリケートしたデータを格納するには [Azure ストレージ アカウント](../storage/storage-redundancy.md#geo-redundant-storage)が必要になります。レプリケートされたデータは Azure Storage に格納され、フェールオーバーが発生すると、Azure VM はスピンアップされます。<br/><br/> **ネットワーク マッピング**: ネットワーク マッピングをセットアップすると、属している復旧計画の種類にかかわらず、同じ Azure ネットワーク上でフェールオーバーするすべての仮想マシンが相互に通信できるようになります。ターゲット Azure ネットワーク上にネットワーク ゲートウェイが設定されている場合、仮想マシンはオンプレミスの仮想マシンにも接続できます。ネットワーク マッピングを設定しない場合は、同じ復旧計画に属する、フェールオーバーするマシンのみが接続できます。<br/><br/> **プロバイダー/エージェント**: デプロイメント時には、VMM サーバーに Azure Site Recovery プロバイダーをインストールし、Hyper-V ホスト サーバーに Azure Recovery Services エージェントをインストールします。プロバイダーは、Azure Site Recovery と通信します。エージェントは、ソースとターゲットの Hyper-V サーバー間のデータ レプリケーションを処理します。VM には何もインストールしません。<br/><br/> **インターネット接続**: VMM サーバーと Hyper-V ホストから。<br/><br/> **プロバイダー接続**: プロバイダーから Site Recovery に対してプロキシ経由で接続する場合、プロキシから Site Recovery の URL にアクセスできる必要があります。<br/><br/> [詳細なデプロイメントの前提条件](site-recovery-vmm-to-azure.md#before-you-start) | **Hyper-V**: Windows Server 2012 R2 以降を実行している、ソースおよびターゲット サイト内の少なくとも 1 つの Hyper-V サーバー。<br/><br/> **仮想マシン**: ソース Hyper-V サーバーの少なくとも 1 つの VM。VM を Azure にレプリケートする場合、[Azure 仮想マシンの前提条件](#azure-virtual-machine-requirements)に準拠する必要があります。<br/><br/>**Azure アカウント**: [Azure](https://azure.microsoft.com/) アカウントとサブスクリプションが必要です。<br/><br/> **Azure Storage**: レプリケートしたデータを格納するには [Azure ストレージ アカウント](../storage/storage-redundancy.md#geo-redundant-storage)が必要になります。<br/><br/> **プロバイダー/エージェント**: デプロイメント時には、Hyper-V ホスト サーバーまたはクラスターに Azure Site Recovery プロバイダーをインストールと Azure Recovery Services エージェントの両方をインストールします。VM には何もインストールしません。<br/><br/> **インターネット接続**: Hyper-V ホストから。<br/><br/> **プロバイダー接続**: プロバイダーからプロキシ経由で接続する場合、プロキシから Site Recovery の URL にアクセスできる必要があります。<br/><br/> [詳細なデプロイメントの前提条件](site-recovery-hyper-v-site-to-azure.md#before-you-start#before-you-start) | **VMM**: ソース VMM サーバーには、1 つ以上の VMM ホスト グループを含むクラウドが少なくとも 1 つある必要があります。クラウドには、Hyper-V 機能プロファイルが設定されている必要があります。<br/><br/>**Hyper-V**: 最新の更新プログラムがインストールされた Windows Server 2012 以降を実行する、ソースおよびターゲット サイトの 1 つ以上の Hyper-V サーバー。Hyper-V サーバーは、VMM クラウド内のホスト グループに属している必要があります。<br/><br/> **仮想マシン**: ソース VMM クラウド内の少なくとも 1 つの VM。<br/><br/>**ネットワーク マッピング**: ネットワーク マッピングをセットアップすると、フェールオーバー後に仮想マシンが適切なネットワークに確実に接続され、レプリカ仮想マシンがターゲット Hyper-V ホスト サーバーに最適に配置されます。ネットワーク マッピングを構成しない場合、フェールオーバー後に、レプリケートされたマシンは、どの VM ネットワークにも接続されません。<br/><br/> **ストレージ マッピング**: 仮想マシンがフェールオーバー後に最適な状態で確実にストレージに接続されるように、ストレージ マッピング オプションを設定できます (既定で、レプリカ VM はターゲット Hyper-V サーバーに指定された場所に格納されます)。<br/><br/> **SAN レプリケーション**: 2 つのオンプレミス VMM サイト間で SAN レプリケーションを使用してレプリケートする場合は、既存の SAN 環境を使用することができます。[サポートされる SAN 配列](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx)を参照してください。<br/><br/> **プロバイダー/エージェント**: デプロイメント時に、Azure Site Recovery Provider を VMM サーバーにインストールして、Azure Site Recovery と通信できるようにします。レプリケーションは、LAN/VPN で Hyper-V ソースとターゲット サーバー間に発生します。<br/><br/> **インターネット接続**: VMM サーバーにのみ。<br/><br/> **プロバイダー接続**: プロバイダーからプロキシ経由で接続する場合、Site Recovery の URL にアクセスできる必要があります。<br/><br/> [詳細なデプロイメントの前提条件](site-recovery-vmm-to-vmm.md#before-you-start)


## VMware VM と物理サーバーをレプリケートするためのデプロイメント要件

次の表は、VMware VM と Windows/Linux 物理サーバーを Azure とセカンダリ サイトにレプリケートするための要件をまとめたものです。

>[AZURE.NOTE] VMware VM と物理サーバーを Azure にレプリケートするには、[拡張](site-recovery-vmware-to-azure-classic.md)デプロイメント モデルを使用するか、古いデプロイメントで使用されていた[レガシ](site-recovery-vmware-to-azure-classic-legacy.md) モデルを使用します。次の表には、各モデルのデプロイメント要件が含まれています。

**Azure にレプリケート (拡張)** | **Azure にレプリケート (レガシ)** | **セカンダリ サイトへのレプリケート**
---|---|---
**オンプレミス管理サーバー**: オンプレミス サイトに、管理サーバーとして動作する専用サーバーが必要です。すべての Site Recovery コンポーネントはこのサーバーにインストールされます。<br/><br/> **追加のプロセス サーバー**: プロセス サーバーは既定で管理サーバーにインストールされますが、必要に応じて追加のオンプレミス プロセス サーバーをインストールして、デプロイメントをスケールすることができます。<br/><br/> **VMware vCenter/ESXi**: VMware VM をレプリケートする場合 (または物理サーバーをフェールバックする場合)、VM がデプロイされている vSphere ESX/ESXi が必要です。ESXi ホストの管理には、vCenter サーバーが推奨されます。</br><br/> **フェールバック**: 物理サーバーをレプリケートする場合でも、Azure からフェールバックする VMware 環境が必要です。さらに、Azure VM としてプロセス サーバーも設定する必要があります。また、大量のトラフィック ボリュームフェールバックする場合、必要に応じて追加のオンプレミス マスター ターゲット サーバーを設定します。[詳細](site-recovery-failback-azure-to-vmware-classic.md)<br/><br/>**Azure アカウント**: [Azure](https://azure.microsoft.com/) アカウントとサブスクリプションが必要です。<br/><br/> **Azure Storage**: レプリケートしたデータを格納するには [Azure ストレージ アカウント](../storage/storage-redundancy.md#geo-redundant-storage)が必要になります。レプリケートされたデータは Azure Storage に格納され、フェールオーバーが発生すると、Azure VM はスピンアップされます。<br/><br/> **Azure 仮想ネットワーク**: フェールオーバーが発生した場合に Azure VM が接続する Azure 仮想ネットワークが必要です。フェールバック後にフェールバックするには、Azure ネットワークからオンプレミス サイトへの VPN 接続 (または Azure ExpressRoute) を設定する必要があります。<br/><br/> **保護されたマシン**: 少なくとも 1 つの VMware 仮想マシンまたは物理 Windows/Linux サーバー。デプロイメント時に、レプリケートする各マシンにモビリティ サービスをインストールします。<br/><br/> **接続**: 管理サーバーからプロキシ経由で Site Recovery に接続する場合、プロキシ サーバーが特定の URL に接続できる必要があります。<br/><br/> [詳細なデプロイメントの前提条件](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)。 | **プライマリ サイト**: プロセス サーバーを設定する必要があります。<br/><br/> **フェールバック**: 物理サーバーをレプリケートする場合でも、Azure からフェールバックする VMware 環境が必要です。オンプレミス サイトの場合、vContinuum サーバーとマスター ターゲット サーバーを設定する必要があります。Azure で、プロセス サーバーを設定する必要があります。[詳細](site-recovery-failback-azure-to-vmware-classic-legacy.md)<br/><br/>**Azure アカウント**: [Azure](https://azure.microsoft.com/) アカウントとサブスクリプションが必要です。<br/><br/> **Azure Storage**: レプリケートしたデータを格納するには [Azure ストレージ アカウント](../storage/storage-redundancy.md#geo-redundant-storage)が必要になります。レプリケートされたデータは Azure Storage に格納され、フェールオーバーが発生すると、Azure VM はスピンアップされます。<br/><br/> **Azure インフラストラクチャ VM**: Azure VM として構成サーバーとマスター ターゲット サーバーを設定する必要があります。<br/><br/> **Azure 仮想ネットワーク**: 構成サーバーとターゲット マスター サーバーのデプロイ先となる Azure 仮想ネットワークが必要になります。Azure VM は、フェールオーバー後にこのネットワークに接続されます。<br/><br/> **保護されたマシン**: 少なくとも 1 つの VMware 仮想マシンまたは物理 Windows/Linux サーバー。デプロイメント時に、レプリケートする各マシンにモビリティ サービスをインストールします。<br/><br/> **接続**: 管理サーバーからプロキシ経由で Site Recovery に接続する場合、プロキシ サーバーが特定の URL に接続できる必要があります。<br/><br/> [詳細なデプロイメントの前提条件](site-recovery-vmware-to-azure-classic-legacy.md#before-you-start)。 | **プライマリ サイト**: 専用の Windows サーバー (物理または VMware 仮想マシン)。<br/><br/> **セカンダリ サイト**: 専用の構成サーバーとマスター ターゲット サーバー。<br/><br/> **保護されたマシン**: 少なくとも 1 つの VMware 仮想マシンまたは物理 Windows/Linux サーバー。デプロイメント時には、各マシンに統合エージェントがインストールされます。





## Azure 仮想マシンの要件

Site Recovery をデプロイすると、Azure でサポートされた任意のオペレーティング システムを実行している仮想マシンまたは物理サーバーをレプリケートできます。これには、Windows と Linux のほとんどのバージョンが含まれます。保護するオンプレミスの仮想マシンが Azure 要件を満たしていることを確認する必要があります。


**機能** | **サポート** | **詳細**
---|---|---
Hyper-V ホスト オペレーティング システム | Windows Server 2012 R2 | サポートされていない場合、前提条件の確認は失敗します。
VMware ハイパーバイザー オペレーティング システム | サポートされたオペレーティング システムの実行 | [詳細](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)
ゲスト オペレーティング システム | Hyper-V から Azure へのレプリケーションの場合、Site Recovery では、[Azure でサポートされた](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)すべてのオペレーティング システムがサポートされます。<br/><br/> VMware と物理サーバーのレプリケーションの場合、Windows と Linux の[前提条件](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)を確認します。 | サポートされていない場合、前提条件の確認は失敗します。
ゲスト オペレーティング システムのアーキテクチャ | 64 ビット | サポートされていない場合、前提条件の確認は失敗します。
オペレーティング システムのディスク サイズ | 最大 1023 GB | サポートされていない場合、前提条件の確認は失敗します。
オペレーティング システムのディスク数 | 1 | サポートされていない場合、前提条件の確認は失敗します。
データ ディスク数 | 16 以下 (最大値は、作成される仮想マシンのサイズの関数であり、16 = XL で求められます。) | サポートされていない場合、前提条件の確認は失敗します。
データ ディスク VHD のサイズ | 最大 1023 GB | サポートされていない場合、前提条件の確認は失敗します。
ネットワーク アダプター | 複数のアダプターがサポートされます。 |
静的 IP アドレス | サポートされています | プライマリ仮想マシンが静的 IP アドレスを使用している場合、Azure で作成される仮想マシンに静的 IP アドレスを指定できます。Hyper-V で実行されている Linux 仮想マシンの場合、静的 IP アドレスはサポートされません。 
iSCSI ディスク | サポートされていません | サポートされていない場合、前提条件の確認は失敗します。
共有 VHD | サポートされていません | サポートされていない場合、前提条件の確認は失敗します。
FC ディスク | サポートされていません | サポートされていない場合、前提条件の確認は失敗します。
ハード ディスク フォーマット| VHD <br/><br/> VHDX | VHDX は現在、Azure でサポートされていませんが、Azure にフェールオーバーするとき、Site Recovery が VHDX を VHD に自動的に変換します。オンプレミスにフェールバックした場合、仮想マシンは引き続き VHDX 形式を使用します。
仮想マシン名| 1 ～ 63 文字で指定します。名前に使用できるのは、英文字、数字、およびハイフンのみです。名前の先頭と末尾は、英文字または数字である必要があります。 | Site Recovery で仮想マシンのプロパティの値を更新します。
仮想マシンのタイプ | <p>第 1 世代</p> <p>第 2 世代 - Windows</p> | OS ディスク タイプがベーシック ディスクの第 2 世代仮想マシンがサポートされています。この OS ディスクは、ディスク フォーマットが VHDX で、1 つまたは 2 つのデータ ボリュームを含み、300 GB 未満であることが必要です。Linux の第 2 世代仮想マシンはサポートされていません。[詳細についてはこちら](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)



## デプロイの最適化

次のヒントは、デプロイの最適化およびスケーリングの際に役立ちます。

- **オペレーティング システムのボリューム サイズ**: 仮想マシンを Azure にレプリケートする場合、オペレーティング システムのボリュームは、1 TB より小さくする必要があります。これよりもボリュームが大きい場合は、デプロイを開始する前に、手動で別のディスクに移動することができます。
- **データ ディスクのサイズ**: Azure にレプリケートする場合、仮想マシンには、各サイズが最大 1 TB の、最大で 32 個のデータ ディスクを備えることができます。これにより、最大 32 TB の仮想マシンのレプリケートとフェールオーバーを効率的に行うことができます。
- **復旧計画の制限**: Site Recovery は、何千もの仮想マシンを処理するようにスケールアップできます。復旧計画は、ひとまとまりでフェールオーバーする必要があるアプリケーション向けのモデルとして設計されているため、復旧計画で用いるマシン数は 50 個までに制限されています。
- **Azure サービスの制限**: すべての Azure サブスクリプションには、コアやクラウド サービスなどに一連の既定の制限が設定されています。サブスクリプション内のリソースの可用性については、テスト フェールオーバーを実行して検証することをお勧めします。これらの制限は、Azure サポートを使用して変更することができます。
- **容量計画**: Site Recovery の[容量計画](site-recovery-capacity-planner.md)のページを参照してください。
- **レプリケーションの帯域幅**: レプリケーションの帯域幅が不足する場合は、次の点に注意してください。
	- **ExpressRoute**: Site Recovery は、Azure ExpressRoute や、Riverbed などの WAN オプティマイザーと連携します。ExpressRoute の詳細については、[こちら](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx)を参照してください。
	- **レプリケーション トラフィック**: Site Recovery は、VHD 全体ではなく、データ ブロックのみを使用してスマート初期レプリケーションを実行します。レプリケーション進行中にレプリケートされるのは、変更箇所のみです。
	- **ネットワーク トラフィック**: 送信先 IP アドレスとポートに基づくポリシーを用いて [Windows QoS](https://technet.microsoft.com/library/hh967468.aspx) を設定することで、レプリケーションに使用されるネットワーク トラフィックを制御できます。さらに、Azure Backup エージェントを使用して、Azure Site Recovery にレプリケートする場合には、そのエージェントの調整を構成することができます。詳細については、[こちら](https://support.microsoft.com/kb/3056159)を参照してください。
- **RTO**: Site Recovery を使用した場合に予期できる回復時刻の目標 (RTO) を測定する場合は、テスト フェールオーバーを実行し、Site Recovery ジョブを表示して、操作を完了するためにかかる時間を分析することをお勧めします。Azure にフェールオーバーする場合、最適な RTO を実現するには、Site Recovery を Azure Automation と復旧計画に統合して、すべての手動操作を自動化することをお勧めします。
- **RPO**: Azure にレプリケートするときには、Site Recovery は、ほぼ同期の回復ポイントの目標 (RPO) をサポートします。これは、データセンターと Azure の間に十分な帯域幅があることを前提としています。





## 次のステップ

一般的なデプロイメントの要件を理解し、比較したら、詳細な前提条件を読み、各シナリオのデプロイを開始します。

- [VMWare 仮想マシンを Azure にレプリケート](site-recovery-vmware-to-azure-classic.md)
- [物理サーバーを Azure にレプリケート](site-recovery-vmware-to-azure-classic.md)
- [VMM クラウドの Hyper-V サーバーを Azure にレプリケート](site-recovery-vmm-to-azure.md)
- [Hyper-V 仮想マシン (VMM を不使用) を Azure にレプリケート](site-recovery-hyper-v-site-to-azure.md)
- [Hyper-V VM をセカンダリ サイトにレプリケート](site-recovery-vmm-to-vmm.md)
- [Hyper-V VM をセカンダリ サイトにレプリケート (SAN を使用)](site-recovery-vmm-san.md)
- [Hyper-V VM をレプリケート (単一の VMM サーバーを使用)](site-recovery-single-vmm.md)

<!---HONumber=AcomDC_0309_2016-->