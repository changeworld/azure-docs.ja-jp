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
	ms.date="02/14/2016"
	ms.author="raynew"/>


# Azure Site Recovery: よく寄せられる質問 (FAQ)
## この記事の内容

この記事には、Site Recovery に関してよく寄せられる質問が含まれます。この FAQ の内容について質問がある場合は、[Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)に投稿してください。


## サポート

###Site Recovery は何をするものですか。

Site Recovery は、オンプレミスの仮想マシンと物理サーバーから Azure またはセカンダリ データセンターへのレプリケーションを調整および自動化することで、ビジネス継続性と障害復旧 (BCDR) 戦略に貢献します。[詳細情報](site-recovery-overview.md)。


### Site Recovery が保護できるものは何ですか。

- **Hyper-V 仮想マシン**: Site Recovery は、Hyper-V 仮想マシンで実行されているすべてのワークロードを保護できます。
- **物理サーバー**: Site Recovery は、Windows または Linux を実行する物理サーバーを保護できます。
- **VMware 仮想マシン**: Site Recovery は、VMware VM で実行されているすべてのワークロードを保護できます。


###保護できる Hyper-V VM は何ですか。

これは、デプロイメント シナリオによって異なります。

Hyper-V ホスト サーバーの前提条件を以下で確認します。

- [Hyper-V VM を (VMM なしで) Azure にレプリケートする](site-recovery-hyper-v-site-to-azure.md#before-you-start)
- [Hyper-V VM を (VMM を使って) Azure にレプリケートする](site-recovery-vmm-to-azure.md#before-you-start)
- [Hyper-V VM をセカンダリ データセンターにレプリケートする](site-recovery-vmm-to-vmm.md#before-you-start)

ゲスト オペレーティング システムについて:

- セカンダリ データセンターにレプリケートする場合は、「[Hyper-V VM のサポートされるゲスト オペレーティング システム](https://technet.microsoft.com/library/mt126277.aspx)」で、Hyper-V ホスト サーバーで実行されている VM のサポートされているゲスト オペレーティング システムの一覧を参照してください。
- Azure にレプリケーションする場合、Site Recovery は [Azure でサポートされている](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)すべてのゲスト オペレーティング システムをサポートします。

Site Recovery は、サポートされている VM で実行されているすべてのワークロードを保護できます。

### クライアント オペレーティング システムで Hyper-V が実行されているときに VM を保護できますか。

いいえ、それはサポートされていません。この問題を回避策するには、コンピューターを物理マシンとして [Azure](site-recovery-vmware-to-azure-classic.md) または[セカンダリ データセンター](site-recovery-vmware-to-vmware.md)にレプリケートする必要があります。


### Site Recovery で保護できるワークロードは何ですか。

Site Recovery を使用すると、仮想マシンまたは物理サーバーで実行されているほとんどのワークロードを保護できます。Site Recovery は、アプリケーションに対応した DR をデプロイするのに役立ちます。これは、SharePoint、Exchange、Dynamics、SQL Server、Active Directory などの Microsoft アプリケーションと統合して、Oracle、SAP、IBM、Red Hat などの主要なベンダーと緊密に連動します。障害復旧ソリューションは、特定のアプリケーションごとにカスタマイズできます。ワークロードの保護の[詳細](site-recovery-workload.md)を参照してください。


### Hyper-V 仮想マシンを保護するには、System Center VMM サーバーが必ず必要ですか。

いいえ。VMM クラウドに配置された Hyper-V VM をレプリケートできるだけでなく、VMM がデプロイされていない環境内の Hyper-V VM もレプリケートすることができます。[詳細情報](site-recovery-hyper-v-site-to-azure.md)。セカンダリ データセンターにレプリケートする場合は、VMM クラウドで Hyper-V ホスト サーバーを管理する必要があります。

### VMM サーバーが 1 つしかない場合、VMM で Site Recovery をデプロイできますか。

はい。クラウド内の Hyper-V VM を VMM サーバー上で Azure にレプリケートすることも、同じサーバー上の VMM クラウド間でレプリケートすることもできます。オンプレミス間のレプリケーションには、プライマリ サイトとセカンダリ サイトに VMM サーバーを配置することを強くお勧めします。[詳細については、こちらを参照してください。](site-recovery-single-vmm.md)

### どの物理サーバーを保護できますか。

Windows および Linux を実行している物理サーバーを保護することができます。オペレーティング システムの要件については、[こちら](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)を参照してください。物理サーバーを Azure にレプリケートする場合も、セカンダリ サイトにレプリケートする場合も、同じ制限が適用されます。

オンプレミスのサーバーがダウンした場合には、物理サーバーは Azure では VM として実行されることに注意してください。オンプレミスの物理サーバーへのフェールバックは、現在サポートされていません。VMware VM にフェールバックする必要があります。

### どの VMware VM を保護できますか。

このシナリオでは VMware vCenter サーバー、vSphere ハイパーバイザー、および VMware ツールが実行されている仮想マシンが必要です。正確な要件については、[こちら](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)を参照してください。物理サーバーを Azure にレプリケートする場合も、セカンダリ サイトにレプリケートする場合も、同じ制限が適用されます。

### 仮想マシンを Azure にレプリケートするための前提条件はありますか。

Azure にレプリケートする仮想マシンは、[Azure 要件](site-recovery-best-practices.md#azure-virtual-machine-requirements)に適合している必要があります。

### Hyper-V 第 2 世代仮想マシンを Azure にレプリケートできますか。

はい。Site Recovery は、フェールオーバー中に第 2 世代から第 1 世代への変換を行います。フェールバック時に、マシンは第 2 世代に変換し直されます。詳細については、[こちら](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)を参照してください。

### Azure にレプリケートした場合、Azure VM の支払いはどうなりますか。

通常のレプリケーション中に、データが地理冗長 Azure Storage にレプリケートされるため、Azure IaaS 仮想マシンの料金は何も支払う必要はありません (すばらしいメリット)。Azure へのフェールオーバーを実行すると、Site Recovery により、Azure IaaS 仮想マシンが自動的に作成されます。その後、Azure で消費したコンピューティング リソースについて、料金が請求されます。

### Site Recovery を使用してブランチ オフィスの障害復旧を管理できますか。

はい。ブランチ オフィスでのレプリケーションとフェールオーバーを調整するために Site Recovery を使用すると、1 か所ですべてのブランチ オフィスのワークロードの統一されたオーケストレーションとビューが得られます。ブランチ オフィスに出向くことなく、フェールオーバーを容易に実行し、本社からすべてのブランチの障害復旧を管理できます。

### Site Recovery ワークフローを自動化するために利用できる SDK は提供されていますか。

はい。Rest API、PowerShell、Azure SDK のいずれかを使用して、Site Recovery ワークフローを自動化することができます。詳細については、「[PowerShell と Azure リソース マネージャーを使った Site Recovery のデプロイ](site-recovery-deploy-with-powershell-resource-manager.md)」を参照してください。


## セキュリティとコンプライアンス

### Site Recovery サービスにデータが送信されますか。

いいえ。Site Recovery がお客様のアプリケーション データをインターセプトしたり、仮想マシンまたは物理サーバーで実行されていることに関するいかなる情報も持つことはありません。

レプリケーション データは、Hyper-V ホスト間、VMware ハイパーバイザー間、プライマリ データセンターとセカンダリ データセンター内の物理サーバー間、あるいはデータセンターと Azure ストレージ間でやり取りされます。Site Recovery には、これらのデータをインターセプトする能力はありません。レプリケーションとフェールオーバーを調整するために必要なメタデータのみが、Site Recovery サービスに送信されます。

Site Recovery は ISO 27001:2005 の認証を受けており、HIPAA、DPA、および FedRAMP JAB の評価が進行中です。

### コンプライアンスのため、オンプレミス環境からのメタデータであっても、同じ地理的リージョン内に維持される必要があります。Site Recovery は役立ちますか。

はい。リージョンに Site Recovery コンテナーを作成すると、レプリケーションとフェールオーバーを有効にし、調整するために必要なすべてのメタデータが、そのリージョンの地理的境界内に維持されます。

### Site Recovery はレプリケーションを暗号化しますか。
オンプレミスのサイト間で仮想マシンと物理サーバーをレプリケートする場合には、転送中の暗号化がサポートされます。オンプレミスのサイトと Azure 間で仮想マシンと物理サーバーをレプリケートする場合には、転送中の暗号化と Azure での暗号化の両方がサポートされます。




## レプリケーションとフェールオーバー

### Azure にレプリケートする場合、どの種類のストレージ アカウントが必要ですか。

[標準の地理冗長ストレージ](../storage/storage-introduction.md#replication-for-durability-and-high-availability)を持つストレージ アカウントが必要になります。Premium Storage は現在サポートされていません。

### どのくらいの頻度でデータをレプリケートできますか。
- **Hyper-V:** Windows Server 2012 R2 が稼働している Hyper-V VM は 30 秒、5 分、または 15 分ごとにレプリケートできます。SAN レプリケーションを設定した場合は、レプリケーションと同期します。
- **VMware と物理サーバー:** レプリケーションの頻度はここでは関係ありません。レプリケーションは継続的に行われます。

### 既存の復旧サイトから第 3 のサイトにレプリケーションを拡張することができますか。
拡張またはチェーン レプリケーションはサポートされていません。「[フィードバック フォーラム](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication/)」でこの機能に関するフィードバックを送信してください。


### Azure に初めてレプリケートする際に、オフライン レプリケーションを行うことができますか。

これはサポートされていません。「[フィードバック フォーラム](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from/)」でこの機能に関するフィードバックを送信してください。


### レプリケーションから特定のディスクを除外することはできますか。

これはサポートされていません。「[フィードバック フォーラム](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6418801-exclude-disks-from-replication/)」でこの機能に関するフィードバックを送信してください。

### ダイナミック ディスクを持つ仮想マシンをレプリケートできますか。

ダイナミック ディスクは Hyper-V 仮想マシンのレプリケート時にサポートされますが、VMware 仮想マシンまたは物理サーバーをレプリケートする際にはサポートされません。「[フィードバック フォーラム](https://feedback.azure.com/forums/256299-site-recovery/)」でこの機能に関するフィードバックを送信してください。

### Azure にフェールオーバーする場合、フェールオーバー後に Azure の仮想マシンにどうしたらアクセスできますか。

Azure VM には、セキュリティで保護されたインターネット接続を経由して、またはある場合には、サイト間 VPN (または Azure ExpressRoute) 経由でアクセスできます。VPN 接続を経由した通信は、VM が配置されている Azure ネットワーク上の内部ポートにマップされます。インターネットを経由した通信は、VM の Azure クラウド サービスのパブリック エンドポイントにマップされます。

### Azure にフェールオーバーする場合、Azure はどのようにデータの回復力を確認しますか。

Azure はサービスを復元するように設計されています。Site Recovery は、必要に応じて、Azure SLA に従ってセカンダリ Azure データセンターにフェールオーバーする機能を既に備えています。これが発生した場合には、お客様のメタデータとコンテナーは、お客様が選択したコンテナーと同じリージョン内に保持されます。

### 2 つのデータ センター間でレプリケートしている場合、プライマリ データセンターに予期しない障害が発生するとどうなりますか。

セカンダリ サイトから計画外のフェールオーバーをトリガーすることができます。Site Recovery は、フェールオーバーを実行するためにプライマリ サイトからの接続を必要としません。


### フェールオーバーは自動で行われますか。

フェールオーバーは自動では行われません。ポータルで 1 回クリックするだけでフェールオーバーを開始できます。または [Site Recovery PowerShell コマンドレット](https://msdn.microsoft.com/library/dn850420.aspx)を使用してフェールオーバーをトリガーすることもできます。Site Recovery ポータルではフェールバックも簡単な操作で行えます。自動化するには、オンプレミスの Orchestrator または Operations Manager を使用すると、仮想マシンのエラーを監視し、SDK を使用してフェールオーバーをトリガーできます。

### Hyper-V VM をレプリケートする場合、Hyper-V レプリケーション トラフィックに割り当てられた帯域幅を調整できますか。
- Hyper-V VM を 2 つのオンプレミス サイト間でレプリケートする場合、Windows QoS を使用できます。サンプル スクリプトを次に示します。

    	New-NetQosPolicy -Name ASRReplication -IPDstPortMatchCondition 8084 -ThrottleRate (2048*1024)
    	gpupdate.exe /force

- Hyper-V VM を Azure にレプリケートする場合は、次のサンプル PowerShell コマンドレットを使用して調整を構成できます。

    	Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)



## サービス プロバイダーのデプロイメント

### Site Recovery は、専用または共有のインフラストラクチャ モデルで動作しますか。
はい。Site Recovery は、専用および共有のインフラストラクチャ モデルの両方をサポートしています。

### テナントの ID は Site Recovery サービスと共有されますか。
いいえ。実際には、テナントは Site Recovery ポータルにアクセスする必要はありません。サービス プロバイダーの管理者だけが、Site Recovery ポータルでアクションを実行します。


### テナントのアプリケーション データは Azure に送信されますか。
サービス プロバイダーが所有するサイト間でレプリケートするときに、アプリケーション データが Azure に送信されることはありません。データは転送中に暗号化され、サービス プロバイダーのサイト間で直接レプリケートされます。

Azure にレプリケートする場合、アプリケーション データは Site Recovery サービスではなく、Azure ストレージに送信されます。データは、転送中に暗号化され、暗号化された状態のまま Azure に残ります。

### テナントに Azure サービスの請求書が届きますか。

いいえ。Azure の請求関係は、サービス プロバイダーとの直接的な関係です。サービス プロバイダーは、そのテナントに対して固有の請求書を生成する必要があります。

### Azure にレプリケートする場合、仮想マシンを Azure で常時実行する必要がありますか。

いいえ。データは、地理冗長 Azure ストレージ アカウントにお客様のサブスクリプションでレプリケートされます。テスト フェールオーバー (DR の訓練) または実際のフェールオーバーを実行すると、Site Recovery は自動的に仮想マシンをお客様のサブスクリプションで作成します。

### Azure にレプリケートする際に、テナント レベルでの隔離は保証されますか。

はい。

### 現在、どのプラットフォームがサポートされていますか。

Azure パック、クラウド プラットフォーム システム、および System Center ベース (Hyper 2012 以降) のデプロイメントがサポートされています。Azure パックと Site Recovery の統合の詳細については、「[仮想マシンの保護を構成する](https://technet.microsoft.com/library/dn850370.aspx)」を参照してください。

### 単一の Azure パックおよび単一の VMM サーバーのデプロイメントもサポートされますか。

はい。Hyper-V 仮想マシンと Azure のレプリケート、またはサービス プロバイダー サイト間のレプリケートができます。サービス プロバイダー サイト間でレプリケートする場合は、Azure Runbook 統合が使用できないことに注意してください。


## 次のステップ

- [Azure Site Recovery の概要](site-recovery-overview.md)を読む
- [Site Recovery アーキテクチャ](site-recovery-components.md)の詳細  

<!---HONumber=AcomDC_0218_2016-->