<properties 
	pageTitle="Azure Site Recovery: よく寄せられる質問 | Microsoft Azure" 
	description="この記事では、Azure Site Recovery に関してよく寄せられる質問について説明します。" 
	services="site-recovery" 
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags 
	ms.service="get-started-article"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na" 
	ms.workload="storage-backup-recovery"
	ms.date="07/12/2016" 
	ms.author="raynew"/>


# Azure Site Recovery: よく寄せられる質問 (FAQ)
## この記事の内容

この記事には、Azure Site Recovery に関してよく寄せられる質問が含まれます。この記事の内容について質問がある場合は、[Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)に投稿してください。


## 全般

###Site Recovery は何をするものですか。

Site Recovery は、オンプレミスの仮想マシンと物理サーバーから Azure またはセカンダリ データセンターへのレプリケーションを調整および自動化することで、ビジネス継続性と障害復旧 (BCDR) 戦略に貢献します。[詳細情報](site-recovery-overview.md)。


### Site Recovery が保護できるものは何ですか。

- **Hyper-V 仮想マシン**: Site Recovery は、Hyper-V 仮想マシンで実行されているすべてのワークロードを保護できます。
- **物理サーバー**: Site Recovery は、Windows または Linux を実行する物理サーバーを保護できます。
- **VMware 仮想マシン**: Site Recovery は、VMware VM で実行されているすべてのワークロードを保護できます。

### Site Recovery では Azure Resource Manager モデルがサポートされますか。 

Site Recovery は、Azure クラシック ポータルだけでなく、Resource Manager をサポートする Azure ポータルでも使用できます。ほとんどのデプロイ シナリオについて、Azure ポータル の Site Recovery では合理的なデプロイ操作が実現し、VM と物理サーバーを、従来のストレージまたは Resource Manager ストレージにレプリケートできます。サポートされているデプロイを次に示します。

- [Azure ポータルで VMware VM または物理サーバーを Azure にレプリケートする](site-recovery-vmware-to-azure.md)
- [Azure ポータルで VMM クラウドの Hyper-V VM を Azure にレプリケートする](site-recovery-vmm-to-azure.md)
- [Azure ポータルで Hyper-V VM (VMM なし) を Azure にレプリケートする](site-recovery-hyper-v-site-to-azure.md)
- [Azure ポータルで VMM クラウドの Hyper-V VM をセカンダリ サイトにレプリケートする](site-recovery-vmm-to-vmm.md)


### Site Recovery でレプリケーションを調整するには、Hyper-V で何が必要ですか。 

Hyper-V ホスト サーバーに必要なものは、デプロイ シナリオによって異なります。以下の Hyper-V に関する前提条件を参照してください。

- [Hyper-V VM を (VMM なしで) Azure にレプリケートする](site-recovery-hyper-v-site-to-azure.md#before-you-start)
- [Hyper-V VM を (VMM を使って) Azure にレプリケートする](site-recovery-vmm-to-azure.md#before-you-start)
- [Hyper-V VM をセカンダリ データセンターにレプリケートする](site-recovery-vmm-to-vmm.md#before-you-start)

- セカンダリ データセンターにレプリケートする場合は、「[Hyper-V VM 用のサポートされているゲスト オペレーティング システム](https://technet.microsoft.com/library/mt126277.aspx)」をご覧ください。
- Azure にレプリケーションする場合、Site Recovery は [Azure でサポートされている](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)すべてのゲスト オペレーティング システムをサポートします。

### クライアント オペレーティング システムで Hyper-V が実行されているときに VM を保護できますか。

いいえ。VM は、サポートされている Windows Server マシンで実行されている Hyper-V ホスト サーバーに配置されている必要があります。クライアント コンピューターを保護する必要がある場合は、物理マシンとして [Azure](site-recovery-vmware-to-azure.md)、または[セカンダリ データセンター](site-recovery-vmware-to-vmware.md)にレプリケートできます。


### Site Recovery で保護できるワークロードは何ですか。

Site Recovery を使用すると、サポートされている VM または物理サーバーで実行されているほとんどのワークロードを保護できます。また、アプリケーションに対応したレプリケーションもサポートしているため、アプリをインテリジェントな状態に復元できます。Site Recovery は、SharePoint、Exchange、Dynamics、SQL Server、Active Directory などの Microsoft アプリケーションと統合し、Oracle、SAP、IBM、Red Hat などの主要なベンダーと緊密に連携します。ワークロードの保護の[詳細](site-recovery-workload.md)を参照してください。


### Hyper-V ホストを VMM クラウドに配置する必要がありますか。 

セカンダリ データセンターにレプリケートする場合は、VMM クラウドの Hyper-V ホスト サーバーに Hyper-V VM を配置する必要があります。Azure にレプリケートする場合は、VMM クラウド内にあるかどうかに関係なく、Hyper-V ホスト サーバー上の VM をレプリケートできます。[詳細については、こちらを参照してください。](site-recovery-hyper-v-site-to-azure.md)

### VMM サーバーが 1 つしかない場合、VMM で Site Recovery をデプロイできますか。 

はい。VMM クラウド内の Hyper-V サーバー上にある VM を Azure にレプリケートすることも、同じサーバー上の VMM クラウド間でレプリケートすることもできます。オンプレミス間のレプリケーションには、プライマリ サイトとセカンダリ サイトの両方に VMM サーバーを配置することをお勧めします。[詳細については、こちらを参照してください。](site-recovery-single-vmm.md)

### どの物理サーバーを保護できますか。

Windows および Linux を実行している物理サーバーを、Azure またはセカンダリ サイトにレプリケートできます。オペレーティング システムの要件については、[こちら](site-recovery-vmware-to-azure.md#protected-machine-prerequisites)をご覧ください。物理サーバーを Azure にレプリケートする場合も、セカンダリ サイトにレプリケートする場合も、同じ要件が適用されます。

オンプレミスのサーバーがダウンした場合には、物理サーバーは Azure では VM として実行されることに注意してください。オンプレミスの物理サーバーへのフェールバックは現在サポートされていませんが、Hyper-V または VMware 上で実行されている仮想マシンにフェールバックすることはできます。


### どの VMware VM を保護できますか。

VMware VM を保護するには、vSphere ハイパーバイザーに加え、VMware ツールが実行されている仮想マシンが必要です。また、VMware vCenter サーバーでハイパーバイザーを管理することをお勧めします。Azure またはセカンダリ サイトへの VMware サーバーと VM のレプリケートに関する要件については、[こちら](site-recovery-vmware-to-azure.md#protected-machine-prerequisites)をご覧ください。

### Site Recovery を使用してブランチ オフィスの障害復旧を管理できますか。

はい。ブランチ オフィスでのレプリケーションとフェールオーバーを調整するために Site Recovery を使用すると、1 か所ですべてのブランチ オフィスのワークロードの統一されたオーケストレーションとビューが得られます。ブランチ オフィスに出向くことなく、フェールオーバーを容易に実行し、本社からすべてのブランチの障害復旧を管理できます。

## セキュリティ

### Site Recovery サービスにレプリケーション データが送信されますか。

いいえ。Site Recovery は、レプリケートされたデータをインターセプトすることも、仮想マシンまたは物理サーバーでの実行内容に関するどのような情報を持つこともありません。レプリケーション データは、オンプレミスの Hyper-V ホスト、VMware ハイパーバイザー、物理サーバーと Azure Storage またはセカンダリ サーバーとの間でやり取りされます。Site Recovery には、これらのデータをインターセプトする能力はありません。レプリケーションとフェールオーバーを調整するために必要なメタデータのみが、Site Recovery サービスに送信されます。

Site Recovery は ISO 27001:2013、27018、HIPAA、DPA の認証を受けており、SOC2 および FedRAMP JAB の評価が進行中です。


### コンプライアンスのため、オンプレミス環境からのメタデータであっても、同じ地理的リージョン内に維持される必要があります。Site Recovery は役立ちますか。

はい。リージョンに Site Recovery コンテナーを作成すると、レプリケーションとフェールオーバーを有効にし、調整するために必要なすべてのメタデータが、そのリージョンの地理的境界内に維持されます。

### Site Recovery はレプリケーションを暗号化しますか。

オンプレミスのサイト間で仮想マシンと物理サーバーをレプリケートする場合には、転送中の暗号化がサポートされます。Azure に仮想マシンと物理サーバーをレプリケートする場合には、転送中の暗号化と (Azure での) 保管データの暗号化の両方がサポートされます。


## レプリケーション


### 仮想マシンを Azure にレプリケートするための前提条件はありますか。

Azure にレプリケートする仮想マシンは、[Azure 要件](site-recovery-best-practices.md#virtual-machines)に適合している必要があります。

### Hyper-V 第 2 世代仮想マシンを Azure にレプリケートできますか。

はい。Site Recovery は、フェールオーバー中に第 2 世代から第 1 世代への変換を行います。フェールバック時に、マシンは第 2 世代に変換し直されます。詳細については、[こちら](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)を参照してください。

### Azure にレプリケートした場合、Azure VM の支払いはどうなりますか。 

通常のレプリケーション中に、データが geo 冗長 Azure Storage にレプリケートされるため、Azure IaaS 仮想マシンの料金は何も支払う必要はありません (すばらしいメリット)。Azure へのフェールオーバーを実行すると、Site Recovery により、Azure IaaS 仮想マシンが自動的に作成されます。その後、Azure で消費したコンピューティング リソースについて、料金が請求されます。


### ASR ワークフローを自動化するために利用できる SDK は提供されていますか。

はい。Rest API、PowerShell、Azure SDK のいずれかを使用して、Site Recovery ワークフローを自動化することができます。PowerShell を使用した Site Recovery のデプロイについて、現在サポートされているシナリオは次のとおりです。

- [VMM クラウドの Hyper-V VM を Azure PowerShell クラシックにレプリケートする](site-recovery-deploy-with-powershell.md)
- [VMM クラウドの Hyper-V VM を Azure PowerShell Resource Manager にレプリケートする](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [VMM なしの Hyper-V VM を Azure PowerShell クラシックにレプリケートする](site-recovery-hyper-v-site-to-azure-classic.md)
- [VMM なしの Hyper-V VM を Azure PowerShell Resource Manager にレプリケートする](site-recovery-deploy-with-powershell-resource-manager.md)


### Azure にレプリケートする場合、どの種類のストレージ アカウントが必要ですか。

- **Azure クラシック ポータル**: Azure クラシック ポータルで Site Recovery をデプロイする場合は、[Standard geo 冗長ストレージ アカウント](../storage/storage-redundancy.md#geo-redundant-storage)が必要です。Premium Storage は現在サポートされていません。アカウントは、Site Recovery コンテナーと同じリージョンにある必要があります。

- **Azure ポータル**: Azure ポータルで Site Recovery をデプロイする場合は、LRS または GRS ストレージ アカウントが必要です。地域的障害が発生した場合やプライマリ リージョンが復旧できない場合にデータの復元性を確保できるように、GRS をお勧めします。アカウントは、Recovery Services コンテナーと同じリージョンにある必要があります。Premium ストレージは現在、VMware VM または物理サーバーをレプリケートする場合にのみサポートされています。

### どのくらいの頻度でデータをレプリケートできますか。

- **Hyper-V:** Hyper-V VM は 30 秒、5 分、または 15 分ごとにレプリケートできます。SAN レプリケーションを設定した場合、レプリケーションは同期されます。
- **VMware と物理サーバー:** レプリケーションの頻度はここでは関係ありません。レプリケーションは継続的です。

### 既存の復旧サイトから第 3 のサイトにレプリケーションを拡張することができますか。

拡張またはチェーン レプリケーションはサポートされていません。[フィードバック フォーラム](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication)でこの機能を要求してください。


### Azure に初めてレプリケートする際に、オフライン レプリケーションを行うことができますか。 

これはサポートされていません。[フィードバック フォーラム](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)でこの機能を要求してください。


### レプリケーションから特定のディスクを除外することはできますか。

これは、Azure ポータルを使用して Azure に [VMware VM と物理サーバーをレプリケート](site-recovery-vmware-to-azure.md#exclude-disks-from-replication)する場合にサポートされます。


### ダイナミック ディスクを持つ仮想マシンをレプリケートできますか。

ダイナミック ディスクは Hyper-V 仮想マシンのレプリケート時にサポートされますが、[Azure ポータル](site-recovery-vmware-to-azure-classic.md)または[デプロイが強化された Azure クラシック ポータル](site-recovery-vmware-to-azure.md)を使用している場合は、VMware VM および物理マシンのレプリケート時にもサポートされます。OS ディスクはベーシック ディスクである必要があることに注意してください。

### Hyper-V レプリケーション トラフィックに割り当てられた帯域幅を調整できますか。

はい。帯域幅調整の詳細については、デプロイに関する記事をご覧ください。

- [VMware VM と物理サーバーをレプリケートするためのキャパシティ プランニング](site-recovery-vmware-to-azure.md#step-5-capacity-planning)
- [VMM クラウドで Hyper-V VM をレプリケートするためのキャパシティ プランニング](site-recovery-vmm-to-azure.md#step-5-capacity-planning)
- [VMM なしの Hyper-V VM をレプリケートするためのキャパシティ プランニング](site-recovery-hyper-v-site-to-azure.md#step-5-capacity-planning)

## フェールオーバー


### Azure にフェールオーバーする場合、フェールオーバー後に Azure の仮想マシンにどうしたらアクセスできますか。 

Azure VM には、セキュリティで保護されたインターネット接続、サイト間 VPN、または Azure ExpressRoute 経由でアクセスできます。接続するにはさまざまこと準備する必要があります。詳細については、次をご覧ください。

- [VMware VM または物理サーバーのフェールオーバー後に Azure VM に接続する](hsite-recovery-vmware-to-azure.md#step-7-test-the-deployment)
- [VMM クラウドでの Hyper-V VM のフェールオーバー後に Azure Vm に接続する](site-recovery-vmm-to-azure.md#step-7-test-your-deployment)
- [VMM なしの Hyper-V VM のフェールオーバー後に Azure Vm に接続する](site-recovery-hyper-v-site-to-azure.md#step-7-test-the-deployment)


### Azure にフェールオーバーする場合、Azure はどのようにデータの回復力を確認しますか。

Azure は復元するように設計されています。Site Recovery は、必要に応じて、Azure SLA に従ってセカンダリ Azure データセンターにフェールオーバーする機能を既に備えています。これが発生した場合には、お客様のメタデータとコンテナーは、お客様が選択したコンテナーと同じリージョン内に保持されます。

### 2 つのデータ センター間でレプリケートしている場合、プライマリ データセンターに予期しない障害が発生するとどうなりますか。

セカンダリ サイトから計画外のフェールオーバーをトリガーすることができます。Site Recovery は、フェールオーバーを実行するためにプライマリ サイトからの接続を必要としません。

### フェールオーバーは自動で行われますか。

フェールオーバーは自動では行われません。ポータルで 1 回クリックするだけでフェールオーバーを開始できます。または [Site Recovery PowerShell](https://msdn.microsoft.com/library/dn850420.aspx) を使用してフェールオーバーをトリガーすることもできます。Site Recovery ポータルではフェールバックも簡単な操作で行えます。

自動化するには、オンプレミスの Orchestrator または Operations Manager を使用すると、仮想マシンのエラーを監視し、SDK を使用してフェールオーバーをトリガーできます。

- 復旧計画については、[こちら](site-recovery-create-recovery-plans.md)を参照してください。
- フェールオーバーについては、[こちら](site-recovery-failover.md)をご覧ください。
- VMware VM と物理サーバーへのフェールバックについては、[こちら](site-recovery-failback-azure-to-vmware.md)を参照してください


## サービス プロバイダー


### 当社はサービス プロバイダーですが、Site Recovery は、専用または共有のインフラストラクチャ モデルで動作しますか。
はい。Site Recovery は、専用および共有のインフラストラクチャ モデルの両方をサポートしています。

### サービス プロバイダーの場合、テナントの ID は Site Recovery サービスと共有されますか。
いいえ。テナント ID は匿名のままです。テナントは Site Recovery ポータルにアクセスする必要はありません。ポータルを操作するのは、サービス プロバイダーの管理者だけです。


### テナントのアプリケーション データは Azure に送信されますか。
サービス プロバイダーが所有するサイト間でレプリケートするときに、アプリケーション データが Azure に送信されることはありません。データは転送中に暗号化され、サービス プロバイダーのサイト間で直接レプリケートされます。

Azure にレプリケートする場合、アプリケーション データは Site Recovery サービスではなく、Azure ストレージに送信されます。データは、転送中に暗号化され、暗号化された状態のまま Azure に残ります。


### テナントに Azure サービスの請求書が届きますか。

いいえ。Azure の請求関係は、サービス プロバイダーとの直接的な関係です。サービス プロバイダーは、そのテナントに対して固有の請求書を生成する必要があります。

### Azure にレプリケートする場合、仮想マシンを Azure で常時実行する必要がありますか。

いいえ。データのレプリケート先は、ご使用のサブスクリプションの Azure ストレージ アカウントです。テスト フェールオーバー (DR の訓練) または実際のフェールオーバーを実行すると、Site Recovery は自動的に仮想マシンをお客様のサブスクリプションで作成します。

### Azure にレプリケートする際に、テナント レベルでの隔離は保証されますか。

はい。

### 現在、どのプラットフォームがサポートされていますか。

Azure パック、クラウド プラットフォーム システム、および System Center ベース (Hyper 2012 以降) のデプロイメントがサポートされています。Azure Pack と Site Recovery の統合の詳細については、[こちら](https://technet.microsoft.com/library/dn850370.aspx)をご覧ください。

### 単一の Azure パックおよび単一の VMM サーバーのデプロイメントもサポートされますか。

はい。Hyper-V 仮想マシンを Azure にレプリケートできます。または、サービス プロバイダー サイト間でレプリケートできます。サービス プロバイダー サイト間でレプリケートする場合は、Azure Runbook 統合が使用できないことに注意してください。


## 次のステップ

- [Azure Site Recovery の概要](site-recovery-overview.md)を読む
- [Site Recovery アーキテクチャ](site-recovery-components.md)の詳細

 

<!---HONumber=AcomDC_0720_2016-->