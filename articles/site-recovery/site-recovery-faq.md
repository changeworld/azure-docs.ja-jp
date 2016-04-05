<properties 
	pageTitle="Site Recovery: よく寄せられる質問 | Microsoft Azure" 
	description="この記事では、Azure Site Recovery に関してよく寄せられる質問にお答えします。"
	services="site-recovery" 
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags 
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na" 
	ms.workload="storage-backup-recovery"
	ms.date="03/27/2016"
	ms.author="raynew"/>


# Azure Site Recovery: よく寄せられる質問 (FAQ)

この記事では、Azure Site Recovery サービスに関してよく寄せられる質問に回答しています。

この記事を読んだ後にまだ質問がある場合は、この記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)で投稿できます。

## 全般

### Site Recovery は何をするものですか。

Site Recovery は、オンプレミスの仮想マシンと物理サーバーから Azure またはセカンダリ データセンターへのレプリケーションを調整および自動化することで、ビジネス継続性と障害復旧 (BCDR) 戦略に貢献します。[詳細情報](site-recovery-overview.md)。

### Site Recovery がレプリケートできるものは何ですか。

- **Hyper-V 仮想マシン**: Site Recovery は、サポート対象の Hyper-V VM で実行されているすべてのワークロードをレプリケートできます。 
- **物理サーバー**: Site Recovery は、サポート対象の Windows または Linux の物理サーバーで実行されているワークロードをレプリケートできます。
- ** 仮想マシン**: Site Recovery は、サポート対象の VMware VM で実行されているすべてのワークロードをレプリケートできます。


### Hyper-V には何が必要ですか。

Hyper-V ホスト サーバーに必要なものは、デプロイ シナリオによって異なります。以下の Hyper-V に関する前提条件を参照してください。

- [Hyper-V VM を (VMM なしで) Azure にレプリケートする](site-recovery-hyper-v-site-to-azure.md#before-you-start)
- [Hyper-V VM を (VMM を使って) Azure にレプリケートする](site-recovery-vmm-to-azure.md#before-you-start)
- [Hyper-V VM をセカンダリ データセンターにレプリケートする](site-recovery-vmm-to-vmm.md#before-you-start)

Hyper-V ホスト サーバー上で実行されるゲストの要件は次のとおりです。

- セカンダリ サイトにレプリケートする場合は、「[HYPER-V 仮想マシンのサポート対象のゲスト オペレーティング システム](https://technet.microsoft.com/library/mt126277.aspx)」を参照してください。
- Azure にレプリケーションする場合、Site Recovery は [Azure でサポートされている](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)ものと同じゲスト オペレーティング システムをサポートします。


### クライアント オペレーティング システムで Hyper-V が実行されている場合、VM を保護できますか。

いいえ。VM は、サポートされている Windows Server マシンで実行されている Hyper-V ホスト サーバーに配置されている必要があります。クライアント コンピューターを保護する必要がある場合は、物理マシンとして [Azure](site-recovery-vmware-to-azure-classic.md) または[セカンダリ データセンター](site-recovery-vmware-to-vmware.md)にレプリケートできます。


### Site Recovery で保護できるワークロードは何ですか。

Site Recovery では、サポートされている VM または物理サーバーで実行されているすべてのワークロードをレプリケートできます。また、アプリケーションに対応したレプリケーションもサポートしているため、アプリをインテリジェントな状態に復元できます。Site Recovery は、SharePoint、Exchange、Dynamics、SQL Server、Active Directory などの Microsoft アプリケーションと統合し、Oracle、SAP、IBM、Red Hat などの主要なベンダーと緊密に連携します。ワークロードの保護の[詳細](site-recovery-workload.md)を参照してください。


### Hyper-V ホストを Site Recovery でレプリケートするには、System Center VMM クラウドに配置する必要がありますか。 

セカンダリ データセンターにレプリケートする場合は、VMM クラウドの Hyper-V ホスト サーバーに Hyper-V VM を配置する必要があります。Azure にレプリケートする場合は、VMM クラウド内にあるかどうかに関係なく、Hyper-V ホスト サーバー上の VM をレプリケートできます。[詳細情報](site-recovery-hyper-v-site-to-azure.md)。

### VMM サーバーが 1 つしかない場合、VMM で Site Recovery をデプロイできますか。 

はい。VMM クラウド内の Hyper-V サーバー上にある VM を Azure にレプリケートすることも、同じサーバー上の VMM クラウド間でレプリケートすることもできます。オンプレミス間のレプリケーションには、プライマリ サイトとセカンダリ サイトの両方に VMM サーバーを配置することをお勧めします。[詳細については、こちらを参照してください。](site-recovery-single-vmm.md)

### どの物理サーバーを保護できますか。

サポートされている Windows/Linux オペレーティング システムを実行している物理サーバーを保護することができます。Azure またはセカンダリ サイトのレプリケーションに関するオペレーティング システムの要件については、[こちら](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)を参照してください。Azure Storage にレプリケートされた物理サーバーは、フェールオーバーが発生すると VM として実行されることに注意してください。現時点では、Azure からオンプレミスのサイトにフェールバックした場合、物理サーバーへのフェールバックはサポートされません。VMware 上で動作している仮想マシンへのフェールバックにのみ対応しています。

### どの VMware VM を保護できますか。

VMware VM を保護するには、vSphere ハイパーバイザーに加え、VMware ツールが実行されている仮想マシンが必要です。また、VMware vCenter サーバーでハイパーバイザーを管理することをお勧めします。Azure またはセカンダリ サイトへの VMware サーバーと VM のレプリケートに関する要件については、[こちら](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)を参照してください。

### Site Recovery を使用してブランチ オフィスの障害復旧を管理できますか。

はい。Site Recovery を使用してブランチ オフィスでのレプリケーションとフェールオーバーを調整すると、ブランチ オフィスのすべてのワークロードを一元的に調整して表示できます。ブランチ オフィスに出向くことなく、フェールオーバーを容易に実行し、本社からすべてのブランチの障害復旧を管理できます。

## セキュリティ


### Site Recovery サービスにレプリケーション データが送信されますか。

いいえ。Site Recovery は、レプリケートされたデータをインターセプトすることも、仮想マシンまたは物理サーバーでの実行内容に関するどのような情報を持つこともありません。

レプリケーション データは、オンプレミスの Hyper-V ホスト、VMware ハイパーバイザー、物理サーバーと Azure Storage またはセカンダリ サーバーとの間でやり取りされます。Site Recovery には、これらのデータをインターセプトする能力はありません。レプリケーションとフェールオーバーを調整するために必要なメタデータのみが、Site Recovery サービスに送信されます。

Site Recovery は ISO 27001:2005 の認証を受けており、HIPAA、DPA、および FedRAMP JAB の評価が進行中です。

### コンプライアンスのため、オンプレミス環境からのメタデータであっても、同じ地理的リージョン内に維持される必要があります。Site Recovery は対応していますか。

はい。リージョンに Site Recovery コンテナーを作成すると、レプリケーションとフェールオーバーを有効にして調整するために必要なすべてのメタデータが、そのリージョンの地理的境界内に維持されます。

### Site Recovery はレプリケーションを暗号化しますか。
オンプレミスのサイト間で仮想マシンと物理サーバーをレプリケートする場合には、転送中の暗号化がサポートされます。Azure に仮想マシンと物理サーバーをレプリケートする場合には、転送中の暗号化と (Azure での) 保管データの暗号化の両方がサポートされます。



## レプリケーション

### 仮想マシンを Azure にレプリケートするための前提条件はありますか。

Azure にレプリケートする仮想マシンは、[Azure 要件](site-recovery-best-practices.md#azure-virtual-machine-requirements)に適合している必要があります。

### Hyper-V 第 2 世代仮想マシンを Azure にレプリケートできますか。

はい。Site Recovery は、フェールオーバー中に VM を第 2 世代から第 1 世代に変換します。VM は、フェールバック時に第 2 世代に変換し直されます。詳細については、[こちら](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)を参照してください。

### Azure にレプリケートした場合、Azure VM の支払いはどうなりますか。 

通常のレプリケーション中に、データが geo 冗長 Azure Storage にレプリケートされるため、Azure IaaS 仮想マシンの料金を支払う必要がありません。Azure へのフェールオーバーを実行すると、Site Recovery により、Azure IaaS 仮想マシンが自動的に作成されます。その後、Azure で消費したコンピューティング リソースについて、料金が請求されます。

### Site Recovery ワークフローを自動化するために利用できる SDK は提供されていますか。

はい。Rest API、PowerShell、Azure SDK のいずれかを使用して、Site Recovery ワークフローを自動化することができます。詳細については、「[PowerShell と Azure Resource Manager を使った Azure Site Recovery](site-recovery-deploy-with-powershell-resource-manager.md)」を参照してください。

### Azure にレプリケートする場合、どの種類のストレージ アカウントが必要ですか。

[標準の地理冗長ストレージ](../storage/storage-introduction.md#replication-for-durability-and-high-availability)を持つストレージ アカウントが必要になります。Premium Storage は現在サポートされていません。

### どのくらいの頻度でデータをレプリケートできますか。

- **Hyper-V:** Windows Server 2012 R2 が稼働している Hyper-V VM は 30 秒、5 分、または 15 分ごとにレプリケートできます。SAN レプリケーションを設定した場合、レプリケーションは同期されます。
- **VMware と物理サーバー:** レプリケーションの頻度はここでは関係ありません。レプリケーションは継続的に行われます。 

### 既存の復旧サイトから別の復旧サイトにレプリケーションを拡張できますか。

拡張またはチェーン レプリケーションはサポートされていません。この機能に関するフィードバックを[こちら](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication/)で送信してください。


### Azure に初めてレプリケートする際に、オフライン レプリケーションを行うことができますか。 

これはサポートされていません。この機能に関するフィードバックを[こちら](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from/)で送信してください。

### レプリケーションから特定のディスクを除外することはできますか。

これはサポートされていません。この機能に関するフィードバックを[こちら](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6418801-exclude-disks-from-replication/)で送信してください。

### ダイナミック ディスクを持つ仮想マシンをレプリケートできますか。

ダイナミック ディスクは Hyper-V 仮想マシンのレプリケート時にサポートされますが、[強化されたデプロイ](site-recovery-vmware-to-azure-classic.md)を使用している場合は、VMware VM および物理マシンのレプリケート時にもサポートされます。OS ディスクはベーシック ディスクである必要があることに注意してください。

### Hyper-V レプリケーション トラフィックに割り当てられた帯域幅を調整できますか。
- Hyper-V VM を 2 つのオンプレミス サイト間でレプリケートする場合、Windows QoS を使用できます。サンプル スクリプトを次に示します。 

    	New-NetQosPolicy -Name ASRReplication -IPDstPortMatchCondition 8084 -ThrottleRate (2048*1024)
    	gpupdate.exe /force

- Hyper-V VM を Azure にレプリケートする場合は、次のサンプル PowerShell コマンドレットを使用して調整を構成できます。

    	Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)

- Hyper-V トラフィックのスロットルについては、[こちら](https://support.microsoft.com/ja-JP/kb/3056159)を参照してください。
- Azure への VMware レプリケーションのスロットルについては、[こちら](site-recovery-vmware-to-azure-classic.md#capacity-planning)を参照してください。


## フェールオーバー

### Azure へのフェールオーバー後に Azure 仮想マシンにアクセスするにはどうすればよいですか。 

Azure VM には、セキュリティで保護されたインターネット接続、サイト間 VPN、または Azure ExpressRoute 経由でアクセスできます。VPN 接続を経由した通信は、VM が配置されている Azure ネットワーク上の内部ポートにマップされます。インターネットを経由した通信は、VM の Azure クラウド サービスのパブリック エンドポイントにマップされます。

### Azure へのフェールオーバーでは、Azure はどのようにデータの回復力を確認しますか。

Azure は復元するように設計されています。Site Recovery は、必要に応じて、Azure SLA に従ってセカンダリ Azure データセンターにフェールオーバーする機能を既に備えています。これが発生した場合には、お客様のメタデータとコンテナーは、お客様が選択したコンテナーと同じリージョン内に保持されます。

### 2 つのサイト間でレプリケートしている場合、プライマリ サイトに予期しない障害が発生するとどうなりますか。

セカンダリ サイトから計画外のフェールオーバーをトリガーすることができます。Site Recovery は、フェールオーバーを実行するためにプライマリ サイトからの接続を必要としません。

### フェールオーバーは自動で行われますか。

フェールオーバーは自動では行われません。ポータルで 1 回クリックするだけでフェールオーバーを開始できます。または [Site Recovery PowerShell コマンドレット](https://msdn.microsoft.com/library/dn850420.aspx)を使用してフェールオーバーをトリガーすることもできます。Site Recovery ポータルでは、フェールバックは一連の操作で実行されます。

フェールオーバーを自動化する場合は、オンプレミスの Orchestrator または Operations Manager を使用すると、仮想マシンのエラーを監視し、SDK を使用してフェールオーバーをトリガーできます。


##
### 当社はサービス プロバイダーですが、Site Recovery は、専用または共有のインフラストラクチャ モデルで動作しますか。
はい。Site Recovery は、専用および共有のインフラストラクチャ モデルの両方をサポートしています。

### サービス プロバイダーの場合、テナントの ID は Site Recovery サービスと共有されますか。
いいえ。テナントは Site Recovery ポータルにアクセスする必要はありません。ポータルを操作するのは、サービス プロバイダーの管理者だけです。


### テナントのアプリケーション データは Azure に送信されますか。

サービス プロバイダーが所有するサイト間でレプリケートするときに、アプリケーション データが Azure に送信されることはありません。データは転送中に暗号化され、サービス プロバイダーのサイト間で直接レプリケートされます。

Azure にレプリケートする場合、アプリケーション データは Site Recovery サービスではなく、Azure ストレージに送信されます。データは、転送中に暗号化され、暗号化された状態のまま Azure に残ります。

### テナントに Azure サービスの請求書が届きますか。

いいえ。Azure の請求関係は、サービス プロバイダーとの直接的な関係です。サービス プロバイダーは、そのテナントに対して固有の請求書を生成する必要があります。

### Azure にレプリケートする場合、仮想マシンを Azure で常時実行する必要がありますか。

いいえ。データは、お客様のサブスクリプションの geo 冗長 Azure ストレージ アカウントにレプリケートされます。テスト フェールオーバー (DR の訓練) または実際のフェールオーバーを実行すると、Site Recovery は自動的に仮想マシンをお客様のサブスクリプションで作成します。

### Azure にレプリケートする際に、テナント レベルでの隔離は保証されますか。

はい。

### 現在、どのプラットフォームがサポートされていますか。

Azure Pack、クラウド プラットフォーム システム、および System Center (2012 以降) のデプロイがサポートされています。Azure Pack の統合の詳細については、「[仮想マシンの保護を構成する](https://technet.microsoft.com/library/dn850370.aspx)」を参照してください。

### 単一の Azure パックおよび単一の VMM サーバーのデプロイメントもサポートされますか。

はい。Hyper-V 仮想マシンと Azure のレプリケート、またはサービス プロバイダー サイト間のレプリケートができます。サービス プロバイダー サイト間でレプリケートする場合は、Azure Runbook 統合が使用できないことに注意してください。


## 次のステップ

- [Azure Site Recovery の概要](site-recovery-overview.md)を読む
- [Site Recovery アーキテクチャ](site-recovery-components.md)の詳細  

<!---HONumber=AcomDC_0330_2016-->