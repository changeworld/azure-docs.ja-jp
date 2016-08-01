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
	ms.date="07/06/2016"
	ms.author="raynew"/>

# Azure Site Recovery のデプロイの準備

Azure Site Recovery サービスでサポートされている各レプリケーション シナリオのデプロイ要件の概要については、この記事をご覧ください。各シナリオの一般要件をお読みになった後には、各デプロイの具体的なデプロイ詳細へのリンクをご利用ください。

この記事に関するコメントや質問は、この記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。

## 概要

組織には、予定されたダウンタイムおよび予定外のダウンタイム時にアプリ、ワークロード、およびデータの実行と利用可能な状態を維持し、できるだけ早く通常の動作状態に復旧させる方法を決定する BCDR の戦略が必要です。BCDR 戦略は、災害発生時にビジネス データを安全かつ回復可能な状態に維持し、ワークロードが継続的に利用可能な状態に保たれるようなものである必要があります。

Site Recovery とは、クラウド (Azure) またはセカンダリ データセンターへのオンプレミスの物理サーバーおよび仮想マシンのレプリケーションを統制することで BCDR 戦略を支援する Azure サービスです。プライマリ ロケーションで障害が発生した場合は、セカンダリ ロケーションにフェールオーバーしてアプリとワークロードの可用性を維持します。プライマリの場所が通常の動作に戻ると、その場所にフェールバックします。詳細については、「[Site Recovery とは](site-recovery-overview.md)」をご覧ください。

## デプロイメント モデルの選択

Azure には、リソースの作成と操作に関して、Azure Resource Manager モデルと従来のサービス管理モデルの 2 種類の[デプロイメント モデル](../resource-manager-deployment-model.md)があります。また、ポータルも 2 つあります。クラシック デプロイメント モデルをサポートする [Azure クラシック ポータル](https://manage.windowsazure.com/)と、両方のデプロイメント モデルをサポートする [Azure ポータル](https://ms.portal.azure.com/)です。

このクラシック ポータルと Azure ポータルの両方で Site Recovery を使用することができます。Azure クラシック ポータルでは、従来のサービス管理モデルで Site Recovery をサポートできます。Azure ポータルでは、従来のモデルまたは Resource Manager デプロイメントをサポートできます。Azure ポータルのデプロイの詳細については、[こちら](site-recovery-overview.md#site-recovery-in-the-azure-portal)をご覧ください。

デプロイメント モデルを選択するときは、次の点に注意してください。

- できる限り [Azure ポータル](https://portal.azure.com)および Resource Manager モデルを使用することをお勧めします。
- Azure ポータルでは、Site Recovery のよりシンプルで直感的な作業開始エクスペリエンスを利用できます。
- Azure ポータルを使用すると、コンピューターを、Azure の従来のストレージと Resource Manager ストレージの両方にレプリケートできます。さらに、Azure ポータルでは、LRS または GRS ストレージ アカウントを使用することもできます。
- Azure ポータルでは、1 つの場所から BCDR サービスを設定および管理できるように、Site Recovery サービスと Backup サービスが 1 つの Recovery Services コンテナーに結合されています。
- Azure サブスクリプションがクラウド ソリューション プロバイダー (CSP) プログラムを使用してプロビジョニングされたユーザーは、Azure ポータルで Site Recovery 操作を管理できるようになりました。
- Azure ポータルで VMware VM または物理マシンを Azure にレプリケートすると、Premium ストレージのサポート、レプリケーションから特定のディスクを除外する機能など、数多くの新機能が利用できるようになります。


## デプロイメント シナリオを選択する

**デプロイ** | **詳細** | **Azure ポータル** | **クラシック ポータル** | **PowerShell**
---|---|---|---|---
**VMware VM を Azureに** | VMware VM を Azure ストレージにレプリケートします | Azure ポータルでは、VM が従来のストレージまたは Resource Manager ストレージにフェールオーバーできます<br/><br/>[Azure ポータル](site-recovery-vmware-to-azure.md)のデプロイメントにより、合理的なデプロイ操作が実現し、数多くの機能の利点がもたらされます。 | Azure クラシック ポータルでは、[拡張モデル](site-recovery-vmware-to-azure-classic.md)でデプロイし、従来の Azure ストレージにフェールオーバーできます。<br/><br/> 新しいデプロイメントでは使用してはならないレガシ モデルもあります。 | PowerShell は現在サポートされていません。
**Hyper-V VM を Azure に** | Hyper-V VM を Azure ストレージに。System Center VMM クラウドで管理されている、または VMM がない Hyper-V ホストに VM を配置できます。 | Azure ポータルでは、従来のストレージまたは Resource Manager ストレージに VM がフェールオーバーできます。<br/><br/> Azure ポータルでは、合理的なデプロイ操作が実現します。VMM クラウドにおける Hyper-V VM のレプリケートの詳細については、[こちら](site-recovery-vmm-to-azure.md)をご覧ください。Hyper-V VM (VMM なし) のレプリケートの詳細については、[こちら](site-recovery-hyper-v-site-to-azure.md)をご覧ください。| 従来の Azure ポータルで、VM を従来の Azure ストレージにフェールオーバーできます | PowerShell デプロイメントはサポートされています。
**物理サーバーを Azure に** | Windows/Linux の物理サーバーを Azure ストレージにレプリケートします | Azure ポータルでは、従来のストレージまたは Resource Manager ストレージにサーバーがフェールオーバーできます。<br/><br/> [Azure ポータル](site-recovery-vmware-to-azure.md)のデプロイメントにより、合理的なデプロイ操作が実現し、数多くの機能の利点がもたらされます。 | Azure クラシック ポータルでは、[拡張モデル](site-recovery-vmware-to-azure-classic.md)でデプロイし、従来の Azure ストレージにフェールオーバーできます。<br/><br/> 新しいデプロイメントでは使用してはならないレガシ モデルもあります。 | PowerShell デプロイメントは現在サポートされていません。
"VMware VM/物理サーバーをセカンダリ サイトに" | VMware VM または Windows/Linux の物理サーバーをセカンダリ サイトにレプリケートします。詳細については、InMage Scout ユーザー ガイドを[ダウンロード](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf)してください。 | Azure ポータルでは使用できません | クラシック ポータルでは、Site Recovery コンテナーから InMage Scout をダウンロードします。 | PowerShell デプロイメントは現在サポートされていません
**Hyper-V VM をセカンダリ サイトに** | VMM クラウドの Hyper-V VM をセカンダリ クラウドにレプリケートします | [Azure ポータル](site-recovery-vmm-to-vmm.md) で、Hyper-V レプリカのみを使用して、VMM クラウドの Hyper-V VM をセカンダリ サイトにレプリケートできます。SAN は現在サポートされていません。 | Azure クラシック ポータル で、[Hyper-V レプリカ](site-recovery-vmm-to-vmm-classic.md)または [SAN レプリケーション](site-recovery-vmm-san.md)を使用して、VMM クラウドの Hyper-V VM をセカンダリ サイトにレプリケートできます | PowerShell デプロイメントはサポートされています



## デプロイに必要なものを確認する

### Azure へのレプリケート

**要件** | **詳細** 
---|---
**Azure アカウント** | [Microsoft Azure](http://azure.microsoft.com/) のアカウントが必要です。<br/><br/> アカウントがなくても、[無料試用版](https://azure.microsoft.com/pricing/free-trial/)を使用できます。Site Recovery の価格の詳細については、[こちら](https://azure.microsoft.com/pricing/details/site-recovery/)をご覧ください。
**Azure Storage** | レプリケートされたデータは Azure Storage に格納され、フェールオーバーが発生すると Azure VM が作成されます。Azure にレプリケートするには、[Azure ストレージ アカウント](../storage/storage-introduction.md)が必要です。<br/><br/>クラシック ポータルで Site Recovery をデプロイする場合は、1 つ以上の [Standard GRS ストレージ アカウント](..storage/storage-redundancy.md#geo-redundant-storage)が必要です。<br/><br/> Azure ポータルでデプロイする場合は、GRS または LRS ストレージを使用できます。<br/><br/> Azure ポータルで VMware VM または物理サーバーをレプリケートする場合は、Premium ストレージがサポートされています。Premium ストレージ アカウントを使用している場合は、オンプレミス データの継続的な変更をキャプチャするレプリケーション ログを保存するために、Standard ストレージ アカウントも必要になります。[Premium ストレージ](../storage/storage-premium-storage.md)は、IO を集中的に行うワークロードをホストするために、常に高い IO パフォーマンスと低遅延時間を必要とする仮想マシンに通常使用されます。<br/><br/> Premium アカウントを使用してレプリケートされたデータを保存する場合は、オンプレミスのデータの継続的な変更をキャプチャするレプリケーション ログを保存するために、Standard ストレージ アカウントも必要になります。
**Azure ネットワーク** | Azure にレプリケートするには、フェールオーバー後、Azure VM が作成されたときに、その Azure VM が接続する Azure ネットワークが必要です。<br/><br/> クラシック ポータルでデプロイする場合は、従来のネットワークを使用します。Azure ポータルでデプロイする場合は、従来のネットワークまたは Resource Manager ネットワークを使用できます。<br/><br/> ネットワークは、コンテナーと同じリージョンにある必要があります。
**ネットワーク マッピング (VMM から Azure)** | VMM から Azure にレプリケートする場合、フェールオーバー後、[ネットワーク マッピング](site-recovery-network-mapping.md)により、Azure VM が正しいネットワークに確実に接続されます。<br/><br/> ネットワーク マッピングを設定するには、VMM ポータルで VM ネットワークを構成する必要があります。
**オンプレミスの** | **VMware VM**: Site Recovery コンポーネントが実行されているオンプレミスのコンピューター、VMware vSphere ホスト/vCenter サーバー、およびレプリケートする VM が必要です。[詳細](site-recovery-vmware-to-azure.md#configuration-server-prerequisites)。<br/><br/> **物理サーバー**: 物理サーバーをレプリケートする場合は、Site Recovery コンポーネントが実行されているオンプレミスのコンピューター、およびレプリケートする物理サーバーが必要です。詳細については、[こちら](site-recovery-vmware-to-azure.md#configuration-server-prerequisites)を参照してください。Azure へのフェールオーバー後に[フェールバック](site-recovery-failback-azure-to-vmware.md)を行う場合は、そのための VMware インフラストラクチャが必要になります。<br/><br/> **Hyper-V VM**: VMM クラウドで Hyper-V VM をレプリケートする場合、VMM サーバーと、保護する VM が配置されている Hyper-V ホストが必要です。[詳細](site-recovery-vmm-to-azure.md#on-premises-prerequisites)。<br/><br/> VMM なしの Hyper-V VM をレプリケートする場合は、VM が配置されている Hyper-V ホストが必要です。詳細については、[こちら](site-recovery-hyper-v-site-to-azure.md#on-premises-prerequisites)を参照してください。
**保護されたマシン** | Azure にレプリケートする保護されたマシンは、以下で説明する [Azure の前提条件](#azure-virtual-machine-requirements)に従う必要があります。


### セカンダリ サイトへのレプリケート

**要件** | **詳細** 
---|---
**Azure アカウント** | [Microsoft Azure](http://azure.microsoft.com/) のアカウントが必要です。<br/><br/> アカウントがなくても、[無料試用版](https://azure.microsoft.com/pricing/free-trial/)を使用できます。Site Recovery の価格の詳細については、[こちら](https://azure.microsoft.com/pricing/details/site-recovery/)をご覧ください。
**オンプレミスの** | **VMware VM**: レプリケーション データをセカンダリ サイトに送信するには、プライマリ サイトに、そのデータをキャッシュ、圧縮、および暗号化するためのプロセス サーバーが必要です。セカンダリ サイトでは、構成サーバーをインストールして、デプロイメントとマスター ターゲット サーバーを管理および監視する必要があります。また、管理を容易にする vContinuum サーバーもお勧めします。さらに、1 台以上の VMware vSphere ホストと、必要に応じて vCenter サーバーが必要です。[詳細](site-recovery-vmware-to-vmware.md)<br/><br/> **VMM クラウドの Hyper-V VM**: VMM サーバーと、レプリケートする VM が格納されている Hyper-V ホストを設定する必要があります。詳細については、[こちら](site-recovery-vmm-to-vmm.md#on-premises-prerequisites)を参照してください。
**ネットワーク マッピング (VMM から VMM)** | VMM から VMM にレプリケートする場合、[ネットワーク マッピング](site-recovery-network-mapping.md)により、フェールオーバー後にレプリカ VM が適切なネットワークに確実に接続され、Hyper-V ホストに最適に配置されます。ネットワーク マッピングを設定するには、VMM サーバーで VM ネットワークを構成する必要があります。
**ストレージ マッピング** | VMM から VMM にレプリケートする場合は、必要に応じて[ストレージ マッピング](site-recovery-storage-mapping.md)を設定して、レプリケートされた VM のストレージ ターゲットを指定できます。ストレージ マッピングは、Hyper-V レプリカと SAN レプリケーションの両方に対して設定できます。<br/><br/> ストレージ マッピングは現在 Azure ポータルでサポートされていません。


## URL アクセスを確認する

サーバーから次の URL にアクセスできるようにします。

**URL** | **VMM から VMM** | **VMM から Azure** | **Hyper-V から Azure** | **VMware から Azure**
---|---|---|---|---
*.accesscontrol.windows.net | 許可 | 許可 | 許可 | 許可
*.backup.windowsazure.com | 必要なし | 許可 | 許可 | 許可
*.hypervrecoverymanager.windowsazure.com | 許可 | 許可 | 許可 | 許可
*.store.core.windows.net | 許可 | 許可 | 許可 | 許可
*.blob.core.windows.net | 必要なし | 許可 | 許可 | 許可
https://www.msftncsi.com/ncsi.txt | 許可 | 許可 | 許可 | 許可
https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi | 必要なし | 必要なし | 必要なし | 許可

## Azure 仮想マシンの要件

Site Recovery をデプロイすると、Azure でサポートされた任意のオペレーティング システムを実行している仮想マシンまたは物理サーバーをレプリケートできます。これには、Windows と Linux のほとんどのバージョンが含まれます。保護するオンプレミスの仮想マシンが Azure 要件を満たしていることを確認する必要があります。


**機能** | **要件** | **詳細**
---|---|---
HYPER-V ホスト | Windows Server 2012 R2 が実行されている必要があります。 | オペレーティング システムがサポートされていない場合は、前提条件の確認は失敗します。
VMware ハイパーバイザー | サポートされるオペレーティング システム | [要件の確認](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)
ゲスト オペレーティング システム | Hyper-V から Azure へのレプリケーション: Site Recovery では、[Azure でサポートされている](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)すべてのオペレーティング システムがサポートされます。<br/><br/> VMware と物理サーバーのレプリケーション: Windows と Linux の[前提条件](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)を確認します | サポートされていない場合、前提条件の確認は失敗します。
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
- **データ ディスクのサイズ**: Azure にレプリケートする場合、仮想マシンに最大で 32 個のデータ ディスクを備えることができます。それぞれのディスクのサイズは最大 1 TB です。これにより、最大 32 TB の仮想マシンのレプリケートとフェールオーバーを効率的に行うことができます。
- **復旧計画の制限**: Site Recovery は、何千もの仮想マシンを処理するようにスケールアップできます。復旧計画は、ひとまとまりでフェールオーバーする必要があるアプリケーション向けのモデルとして設計されているため、復旧計画で用いるマシン数は 50 個までに制限されています。
- **Azure サービスの制限**: すべての Azure サブスクリプションには、コアやクラウド サービスなどに一連の既定の制限が設定されています。サブスクリプション内のリソースの可用性については、テスト フェールオーバーを実行して検証することをお勧めします。これらの制限は、Azure サポートを使用して変更することができます。
- **キャパシティ プランニング**: Site Recovery の[キャパシティ プランニング](site-recovery-capacity-planner.md)のページをご覧ください。
- **レプリケーションの帯域幅**: レプリケーションの帯域幅が不足する場合は、次の点に注意してください。
	- **ExpressRoute**: Site Recovery は、Azure ExpressRoute や、Riverbed などの WAN オプティマイザーと連携します。ExpressRoute の詳細については、[こちら](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx)を参照してください。
	- **レプリケーション トラフィック**: Site Recovery は、VHD 全体ではなく、データ ブロックのみを使用してスマート初期レプリケーションを実行します。レプリケーション進行中にレプリケートされるのは、変更箇所のみです。
	- **ネットワーク トラフィック**: 送信先 IP アドレスとポートに基づくポリシーを用いて [Windows QoS](https://technet.microsoft.com/library/hh967468.aspx) を設定することで、レプリケーションに使用されるネットワーク トラフィックを制御できます。さらに、Azure Backup エージェントを使用して、Azure Site Recovery にレプリケートする場合には、そのエージェントの調整を構成できます。詳細については、[こちら](https://support.microsoft.com/kb/3056159)を参照してください。
- **RTO**: Site Recovery を使用した場合に予期できる回復時刻の目標 (RTO) を測定するには、テスト フェールオーバーを実行し、Site Recovery ジョブを表示して、操作を完了するためにかかる時間を分析することをお勧めします。Azure にフェールオーバーする場合、最適な RTO を実現するには、Site Recovery を Azure Automation と復旧計画に統合して、すべての手動操作を自動化することをお勧めします。
- **RPO**: Azure にレプリケートするときには、Site Recovery は、ほぼ同期の回復ポイントの目標 (RPO) をサポートします。これは、データセンターと Azure の間に十分な帯域幅があることを前提としています。


##サービス URL
サーバーからこれらの URL にアクセスできるようにします。


**URL** | **VMM から VMM** | **VMM から Azure** | **Hyper-V サイトから Azure** | **VMware から Azure**
---|---|---|---|---
 *.accesscontrol.windows.net | アクセス権が必要 | アクセス権が必要 | アクセス権が必要 | アクセス権が必要
 *.backup.windowsazure.com | | アクセス権が必要 | アクセス権が必要 | アクセス権が必要
 *.hypervrecoverymanager.windowsazure.com | アクセス権が必要 | アクセス権が必要 | アクセス権が必要 | アクセス権が必要
 *.store.core.windows.net | アクセス権が必要 | アクセス権が必要 | アクセス権が必要 | アクセス権が必要
 *.blob.core.windows.net | | アクセス権が必要 | アクセス権が必要 | アクセス権が必要
 https://www.msftncsi.com/ncsi.txt | アクセス権が必要 | アクセス権が必要 | アクセス権が必要 | アクセス権が必要
 https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi | | | | アクセス権が必要


## 次のステップ

一般的なデプロイメントの要件を理解し、比較したら、詳細な前提条件を読み、各シナリオのデプロイを開始します。

- [VMWare 仮想マシンを Azure にレプリケート](site-recovery-vmware-to-azure-classic.md)
- [物理サーバーを Azure にレプリケート](site-recovery-vmware-to-azure-classic.md)
- [VMM クラウドの Hyper-V サーバーを Azure にレプリケート](site-recovery-vmm-to-azure.md)
- [Hyper-V 仮想マシン (VMM を不使用) を Azure にレプリケート](site-recovery-hyper-v-site-to-azure.md)
- [Hyper-V VM をセカンダリ サイトにレプリケート](site-recovery-vmm-to-vmm.md)
- [Hyper-V VM をセカンダリ サイトにレプリケート (SAN を使用)](site-recovery-vmm-san.md)
- [Hyper-V VM をレプリケート (単一の VMM サーバーを使用)](site-recovery-single-vmm.md)

<!---HONumber=AcomDC_0720_2016-->