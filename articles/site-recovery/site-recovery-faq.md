<properties 
	pageTitle="Azure Site Recovery: よく寄せられる質問" 
	description="この記事では、Azure Site Recovery の使用に関してよく寄せられる質問について説明します。" 
	services="site-recovery" 
	documentationCenter=""
	authors="csilauraa"
	manager="jwhit"
	editor="tysonn"/>

<tags 
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na" 
	ms.workload="storage-backup-recovery"
	ms.date="08/26/2015" 
	ms.author="lauraa"/>


# Azure Site Recovery: よく寄せられる質問
## この記事の内容

この記事には、Azure Site Recovery に関してよく寄せられる質問が含まれます。Site Recovery と関連するデプロイ シナリオの概要については、「[Site Recovery Overview (Site Recovery Overview 概要)](site-recovery-overview.md)」を参照してください。

この記事を読んだ後に他の質問がある場合は、[Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)に投稿できます。


## 全般
### ASR はセキュリティで保護されていますか。 どのようなデータが Azure に送信されますか。

はい、ASR はセキュリティで保護されています。ASR はアプリケーション データをインターセプトしません。また、仮想マシン内で何が動作しているかという情報も持ちません。Hyper-V ホストまたは仮想マシンのいずれからも、インターネット接続は必要ありません。

レプリケーションは自社のエンタープライズ サイトとサービス プロバイダー サイトの間で行われるので、いずれのアプリケーション データも Azure に送信されません。フェールオーバーを調整するために必要な VM 名またはクラウド名などのメタデータのみが Azure に送信されます。ASR には、アプリケーション データをインターセプトする能力はありません。そのようなデータはオンプレミスに常に維持されます。

ASR は ISO 27001:2005 の認証を受けており、HIPAA、DPA、および FedRAMP JAB の評価が進行中です。

### コンプライアンス要件として、オンプレミス環境からのメタデータであっても、同じ地理的リージョン内に維持される必要があります。ASR はその要件を満たすことができますか。

はい。選択したリージョンに Site Recovery コンテナーを作成すると、障害復旧設定を有効にし、調整するために必要なすべてのメタデータは、そのリージョンの地理的境界内に維持されます。

### ASR ワークフローを自動化するために利用できる SDK は提供されていますか。

はい。ASR ワークフローは、Rest API、PowerShell、または Azure SDK を使用して自動化できます。詳細については、「[Introducing PowerShell support for Azure Site Recovery (Azure Site Recovery 用の PowerShell サポート概要)](http://azure.microsoft.com/blog/2014/11/05/introducing-powershell-support-for-azure-site-recovery/)」というタイトルのブログ投稿を参照してください。

### ASR ではレプリケーションが暗号化されますか。 
*Hyper-V および VMM の保護シナリオ*では、オンプレミスと Azure間、およびオンプレミス間のレプリケーションで、転送中の暗号化がサポートされます。Azure への *Hyper-V および VMM の保護*によって、暗号化された状態での保存がサポートされます。詳細については、[この記事](https://azure.microsoft.com/blog/2014/09/02/azure-site-recovery-privacy-security-part1/)を参照してください。

### レプリケーション/コピーの間隔を 15 分より長くすることはできますか。
* **Hyper-V および VMM のシナリオ**: いいえ。ホスト ベースのレプリケーションを使用した、Hyper-V 仮想マシンのレプリケーションを構成できるのは、30 秒、5 分、および 15 分のみです。
* **VMware/物理のシナリオ**: インゲスト ベースのレプリケーションには適用されません。テクノロジで継続的なデータ保護が使用されるためです。

### ASR を使用して、レプリケーションから特定のディスクを除外することができますですか。
これはサポートされていません。[Azure Site Recovery Feedback Forum - Exclude disk from replication (Azure Site Recovery フィードバック フォーラム - レプリケーションからディスクを除外する)](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6418801-exclude-disks-from-replication) を通じてフィードバックをお送りください。

### ダイナミック ディスク ベースの仮想マシンをレプリケートできますか。
*Hyper-V および VMM のシナリオ*では、ダイナミック ディスクをサポートしています。*VMware 仮想マシンまたは物理マシンのシナリオ*では、ダイナミック ディスクをサポートしていません。[Azure Site Recovery Feedback Forum (Azure Site Recovery フィードバック フォーラム)](http://feedback.azure.com/forums/256299-site-recovery) を通じてフィードバックをお送りください。

### サポートされているストレージ アカウント タイプは何ですか。
[標準的な地理冗長ストレージ](../storage/storage-redundancy.md#geo-redundant-storage)はサポートされています。[Premium Storage アカウント]((../storage/storage-premium-storage-preview-portal/) は、[VMware 仮想マシンまたは物理マシンのシナリオ](site-recovery-vmware-to-azure.md)でのみサポートされます。標準のローカル冗長ストレージのサポートは未対応です。[Support for locally redundant storage support (ローカル冗長ストレージをサポートするためのサポート)](http://feedback.azure.com/forums/256299-site-recovery/suggestions/7204469-local-redundant-type-azure-storage-support) を通じてフィードバックをお送りください。

### 既存の復旧サイトから第 3 のサイトにレプリケーションを拡張することができますか。
これはサポートされていません。[Azure Site Recovery Feedback Forum - Support for extending replication (Azure Site Recovery フィードバック フォーラム - レプリケーションの拡張のサポート)](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication) を通じてフィードバックをお送りください。

### オフライン メカニズムを使用して、初期ディスクを Azure にシードできますか。
これはサポートされていません。[Azure Site Recovery Feedback Forum - Support for offline replication (Azure Site Recovery フィードバック フォーラム - オフライン レプリケーションのサポート)](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from) を通じてフィードバックをお送りください。

### Hyper-v をソースとして使用する場合、レプリケーション トラフィックに割り当てられた帯域幅を調整できますか。
- レプリケーションを 2 つのオンプレミス サイト間で行っている場合は、Windows QoS を使用して調整を行うことができます。サンプル スクリプトを次に示します。 

    	New-NetQosPolicy -Name ASRReplication -IPDstPortMatchCondition 8084 -ThrottleRate (2048*1024)
    	gpupdate.exe /force

- レプリケーションを Azure に対して行っている場合は、次の PowerShell コマンドレットのサンプルを使用して構成することができます。

    	Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)


## バージョンのサポート

### Windows Server ホストおよびクラスターのサポートされるバージョンはどれですか。
Hyper-V サイト間のレプリケーションと保護を有効にするために Hyper-V レプリカを選択している場合は、Windows Server 2012 および Windows Server 2012 R2 を使用できます。


### Hyper-V ゲスト オペレーティング システムとしては、どのバージョンがサポートされますか。
サポートされるゲスト オペレーティング システムの最新のリストが、「[Virtual Machines とゲスト オペレーティング システムについて](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)」というタイトルのトピックで提供されています。

### Hyper-V がクライアント オペレーティング システムで実行されている場合、仮想マシンの保護を構成できますか。

クライアント オペレーティング システムで実行されている Hyper-V を構成して、Azure に仮想マシンをレプリケートすることはできません。Hyper-V レプリカは、サーバー オペレーティング システムでのみサポートされます。[この記事](site-recovery-vmware-to-azure.md)を参照して、Azure に物理クライアント コンピューターをレプリケートできます。

### ASR は、第 2 世代のマシンをサポートしていますか。

はい。ASR は、Hyper-V から Azure への、第 2 世代仮想マシンのレプリケーションをサポートしています。ASR は、フェールオーバー中に第 2 世代から第 1 世代への変換を行います。フェールバック時に、マシンは第 1 世代に変換し直されます。詳細については、[こちら](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)を参照してください。


## サービス プロバイダー サイト間でのデプロイ 

### このソリューションは、専用または共有のインフラストラクチャ モデルで動作しますか。
はい、ASR は専用または共有の両方のインフラストラクチャ モデルをサポートします。

### テナントの ID は Azure と共有されますか。
いいえ。実際には、テナントは ASR ポータルにアクセスする必要はありません。サービス プロバイダーの管理者だけが、Azure の ASR ポータルでアクションを実行します。

### テナントに Azure から請求書が届きますか。
いいえ。Microsoft Azure の請求関係は、サービス プロバイダーとの間で直接維持されます。サービス プロバイダーは、そのテナントに対して固有の請求書を生成する必要があります。

### テナントのアプリケーション データは Azure に送信されますか。
DR 用にサービス プロバイダー独自のサイトを使用する場合、アプリケーション データが Azure に送信されることはありません。データは、サービス プロバイダーのサイト間で直接暗号化およびレプリケートされます。Azure を DR サイトとして使用する場合、アプリケーション データは Azure に送信されます。保管されているデータおよび転送中のデータは、暗号化された状態に維持されます。また、VPN、ExpressRoute、またはパブリック インターネットを使用して、Azure との接続を確立することもできます。

### Azure を DR サイトとして使用する場合、仮想マシンを Azure で常時実行する必要がありますか。
いいえ、ASR はファーストクラスのパブリック クラウド DRaaS ソリューションとして設計されています。データは、地理冗長 Azure ストレージ アカウントに独自のサブスクリプションでレプリケートされます。DR の訓練または実際のフェールオーバーを実行すると、ASR は自動的に仮想マシンをお客様のサブスクリプションで作成します。

### Azure を DR サイトとして選択した場合、テナントレベルでの分離を確立できますか。
はい。

### 現在、どのプラットフォームがサポートされていますか。
Azure パック、クラウド プラットフォーム システム、および System Center ベースの Hyper 2012 以降のデプロイがサポートされます。ASR と Azure パックの統合の詳細については、「[仮想マシンの保護を構成する](https://technet.microsoft.com/library/dn850370.aspx)」を参照してください。

### 単一 Azure パックおよび単一 VMM サーバーのデプロイもサポートされますか。
はい、単一 SCVMM デプロイは、サービス プロバイダー サイト間の DR、およびサービス プロバイダー サイトと Azure との間の DR の 2 つのシナリオでサポートされます。

単一 Azure パック デプロイも、サービス プロバイダー サイト間の DR を有効にした場合にサポートされますが、ASR Runbook 統合は、このトポロジで利用できません。

## Hyper-V サイトと Azure の間でのデプロイ (VMM なし)

### ASR では、サイト間 VPN を有効にする必要がありますか。
いいえ、必須ではありません。ASR はパブリック インターネット経由でも同様に動作します。ただし、サイト間 VPN を構成している場合、フェールオーバーした仮想マシンに以前と同じ方法でアクセスできます。Azure を災害復旧サイトとして有効にする場合のネットワークに関する注意事項の詳細については、「[Networking Infrastructure Setup for Microsoft Azure as a Disaster Recovery Site (災害復旧サイトとしての Microsoft Azure 用のネットワーク インフラストラクチャの設定)](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/)」ブログを参照してください。

### ASR 保護の料金に追加料金が発生することがありますか。
安定した状態では、変更が地理冗長 Azure Storage にレプリケートされ、Azure IaaS 仮想マシンの料金は何も支払う必要はありません (すばらしいメリット)。Azure へのフェールオーバーを実行すると、ASR により、Azure IaaS 仮想マシンが自動的に作成されます。その後、Azure で消費したコンピューティング リソースについて、料金が請求されます。

### ブランチ オフィスに VMM がインストールされていません。ブランチ オフィスのワークロードの保護を Azure に対して有効にできますか。 これを行うために ASR を使用する主要なメリットは何ですか。
はい、Hyper-V ホストで動作する、SCVMM によって管理されていない仮想マシンを、ASR を使用して保護できます。

ASR を使用して、すべてのブランチ オフィスの障害復旧ニーズを管理すると、1 つの中央サイトからすべてのブランチ オフィスのワークロードを統一したビューで確認できます。1 回だけのクリックによるフェールオーバーと、ブランチ オフィスを訪問することなく本社からすべてのブランチ オフィスの障害復旧を管理することも実現できます。現在、ネットワーク マッピングおよび保管データの暗号化は、ブランチ オフィスの障害復旧用にはサポートされていません。

### ブランチ オフィスについて、保護できる仮想マシンの数に制限はありますか。
いいえ。サポートについては、エンタープライズ シナリオと同じです。

### 保護する仮想マシンにエージェントをインストールする必要はありますか。

仮想マシンにエージェントは必要ありません。保護する仮想マシンを実行している各 Hyper-V サーバーに、Site Recovery プロバイダーと Recovery Services エージェントをインストールする必要があります。これら 2 つのコンポーネントは、Site Recovery のデプロイ中にダウンロードできる、単一のプロバイダー ファイルを使用してインストールされます。

### Hyper-V サーバーで実行されているプロバイダーには、特別なファイアウォール設定が必要でしょうか。

特別な設定は不要です。サーバー上のプロバイダー コンポーネントは、HTTPS 接続によって Azure と通信し、特に指定がない限り、Hyper-V ホスト サーバー上の既定のインターネット設定を使用します。

### Hyper-V サーバーをドメイン メンバーにする必要はありますか。

いいえ、サーバーがドメインに属している必要はありません。

### Windows Server ホストおよびクラスターのサポートされるバージョンはどれですか。
Hyper-V サイトと Azure の間で ASR および Hyper-V レプリケーションを使用している場合は、Windows Server 2012 および Windows Server 2012 R2 を使用できます。

### ゲスト オペレーティング システムとしては、どのバージョンがサポートされますか。
サポートされるゲスト オペレーティング システムの最新のリストが、「[Virtual Machines とゲスト オペレーティング システムについて](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)」というタイトルの記事で提供されています。

## 2 つの VMM データセンター間でのデプロイ

### セカンダリ サイトにレプリケートされている仮想マシンを Azure にレプリケートできますか。 

いいえ、この種のチェーン レプリケーションはサポートされていません

### 2 つの VMM データ センター間の保護を構成するために証明書が必要ですか。

いいえ。ASR で VMM クラウド間の保護を構成するときに、認証の種類を指定します。実際に利用できる Kerberos 環境を構成している場合を除き、HTTPS を選択してください。Azure Site Recovery は、HTTPS 認証に使用する証明書を自動的に構成します手動で構成する必要はありません。Kerberos を選択した場合は、ホスト サーバーの相互認証に Kerberos チケットが使用されます。既定では、Hyper-V ホスト サーバーの Windows ファイアウォールで、ポート 8083 (Kerberos 用) と 8084 (認証用) が開きます。この設定は、Windows Server 2012 R2 で実行されている Hyper-V ホスト サーバーだけに関連することに注意してください。



## SAN を使用した 2 つの VMM データセンター間でのデプロイ

### SAN ベースのレプリケーションが設定されており、それを変更したくない場合はどうすればよいですか。
変更する必要はありません。ASR は、レプリケーションが既に設定されているシナリオをサポートします。この場合、仮想マシン中心の災害復旧オーケストレーション、災害復旧ネットワークの構成、アプリケーション対応の復旧計画、災害復旧の訓練、およびコンプライアントと監査に関するレポート要件に対して、ASR を活用できます。


### VMM は必要ですか。 両側で VMM が必要ですか。
はい。配列に固有の SMI-S プロバイダーを使用して、SAN 配列を VMM で管理する必要があります。

配列の種類に基づく、単一の VMM HA デプロイがサポートされます。ただし、推奨される構成は、個別の VMM サーバーを使用してサイトを管理することです。


### サポート対象のストレージ アレイは何ですか。

NetApp、EMC、および HP では、SMI-S プロバイダーを更新することで、Azure Site Recovery SAN レプリケーションのサポートに対応しました。詳細については、以下のリンクを参照してください。

- [NetApp Clustered Data ONTAP 8.2](http://community.netapp.com/t5/Technology/NetApp-Unveils-Support-for-Microsoft-Azure-SAN-Replication-with-SMI-S-and/ba-p/94483)
- [EMC VMAX シリーズ](https://thecoreblog.emc.com/high-end-storage/microsoft-azure-site-recovery-now-generally-available-vmax-srdf-integration-pack-ready-for-public-review/)    
- [HP 3PAR](http://h20195.www2.hp.com/V2/GetDocument.aspx?docname=4AA5-7068ENW&cc=us&lc=en)


### 自社のストレージ管理について不明な場合はどうすればよいですか。
ストレージ管理者によって設定された既存のレプリケーションも対処可能です。つまり、ストレージ管理者は、アレイについて何も変更する必要はありません。ただし、組織で SCVMM を通じてストレージ管理を自動化する場合は、ASR と VMM を使用してストレージをプロビジョニングすることもできます。

### 同期レプリケーションはサポートされますか。 ゲスト クラスターはサポートされますか。 共有ストレージはサポートされますか。
すべて、はいです。

### Net-net: 動作するようにするには、オンプレミスに何をインストールする必要がありますか。
(SAN) アレイベースのレプリケーションを使用して Hyper-V サイト間のレプリケーションと保護を有効にする場合、必要なことは、ASR DR プロバイダーを SCVMM サーバーにインストールすることだけです。これは、インターネット接続が必要な*唯一*のホストでもあることに注意してください。


また、アレイは、更新された SMI-S プロバイダーを使用して SCVMM によって検出される必要もあります。このプロバイダーは、お使いのストレージのベンダーにより提供されます。

## VMware と Azure の間でのデプロイ

### VMware VM のクローンを作成しています。クローンを作成した VM を Azure に置いて保護できますか。
保護されている VM のクローンは作成できません。クローンを作成した VM にモバイル サービスがインストールされていない限り、クローンを作成した VMware VM を Azure に置いて保護できます。重複エントリを回避するために、モバイル サービスをインストールする前に VM のクローンを作成できます。両方の VM の GUID が同じ場合、複製に影響が出るためです。

### プロセス サーバー VM のクローンを作成できますか。
いいえ。プロセス サーバーのクローンは作成できません。プロセス サーバーがデプロイされるとき、独自の ID が作成されます。クローンを作成すると、2 つのプロセス サーバーで GUID が同じになり、既存の複製に影響が出ます。


## 物理サーバーと Azure の間でのデプロイ

### オンプレミスの物理サーバーを Azure で保護できますか。

はい、ASR を使用して物理サーバーをレプリケートし、オンプレミスのサーバーがダウンしたときは、それを Azure 内の VM として実行することができます。オンプレミスの物理サーバーへのフェールバックは、現在サポートされていません。Hyper-V または VMware 上で実行されている仮想マシンへのフェールバックは可能です。


## フェールオーバー

### Azure がダウンしたらどうなりますか。 VMM からのフェールオーバーをトリガーできますか。

Azure はサービスを復元するように設計されていますが、最悪の事態に備えることもできます。ASR は、Azure SLA に従ってセカンダリ Azure データセンターにフェールオーバーする機能を既に備えています。この事態が発生した場合でも、お客様のメタデータと ASR コンテナーは、お客様がコンテナー用に選択したリージョンと同じ地理的リージョン内に維持されます。


### 災害時に、プライマリ データセンターの ISP も停止した場合はどうなりますか。 セカンダリ データセンターの ISP も停止した場合はどうなりますか。

ASR ポータルを使用することにより、1 回のクリックで計画されていないフェールオーバーを実行できます。フェールオーバーを実行するために、プライマリ データセンターから接続する必要はありません。

フェールオーバー後にセカンダリ データセンターで動作する必要のあるアプリケーションによっては、特定の形体のインターネット接続が必要になることがあります。ASR は、災害時にプライマリ サイトと ISP が影響を受けても、セカンダリ サイトの SCVMM サーバーが引き続き ASR に接続されていることを前提にしています。

### ASR はフェールバックをサポートしますか。
はい。Azure からオンプレミス サイトへのフェールバックは、ASR ポータルで 1 回クリックするだけで簡単に実行できます。

### 自動フェールオーバーはできますか。

自動フェールオーバーのオプションはありません。フェールオーバーをトリガーするには、ポータルからクリック 1 回で行う方法と、[Site Recovery PowerShell](https://msdn.microsoft.com/library/dn850420.aspx) を使用する方法があります。オンプレミスの Orchestrator または Operations Manager を使用すると、仮想マシンのエラーを監視し、SDK を使用してフェールオーバーをトリガーできます。これにより、フェールオーバーのプロセスを自動化できます。




## Monitoring

### セットアップおよび実行中のレプリケーションを監視するために使用できるソリューションはありますか。
注意を払う必要のあるすべての問題について、ASR サービスから直接電子メール通知を受け取れるようにサブスクライブすることができます。また、SCOM を使用することにより、実行中のレプリケーションを監視することもできます。

## 次のステップ

ASR のデプロイを開始する際は、次の記事を参照してください。

- [オンプレミスの VMM サイトと Azure 間の保護の設定](site-recovery-vmm-to-azure.md) 
- [Set up protection between an on-premises Hyper-V site and Azure (オンプレミスの Hyper-V サイトと Azure 間の保護の設定)](site-recovery-hyper-v-site-to-azure.md) 
- [Set up protection between two on-premises VMM sites (2 つのオンプレミスの VMM サイト間の保護の設定)](site-recovery-vmm-to-vmm.md) 
- [Set up protection between two on-premises VMM sites with SAN (SAN を使用した 2 つのオンプレミスの VMM サイト間の保護の設定)](site-recovery-vmm-san.md) 
- [単一の VMM サーバーを使用した保護の設定](site-recovery-single-vmm.md) 

 

<!---HONumber=Sept15_HO3-->