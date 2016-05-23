<properties
	pageTitle="SQL Server の高可用性と障害復旧 | Microsoft Azure"
	description="Azure Virtual Machines で実行されている SQL Server に対するさまざまな種類の HADR 戦略についての説明。"
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="MikeRayMSFT"
	manager="jhubbard"
	editor=""
	tags="azure-service-management"/>
<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="05/04/2016"
	ms.author="mikeray" />

# Azure 仮想マシンにおける SQL Server の高可用性と障害復旧

## 概要

Microsoft Azure 仮想マシン (VM) と SQL Server を使用すると、高可用性と障害復旧 (HADR) データベース ソリューションのコストを削減できます。ほとんどの SQL Server HADR ソリューションでは、Azure のみのソリューションとしても、ハイブリッドのソリューションとしても、Azure 仮想マシンがサポートされています。Azure のみのソリューションでは、HADR システム全体が Azure で実行されます。ハイブリッド構成では、ソリューションの一部が Azure で実行され、その他の部分が組織内のオンプレミスで実行されます。Azure 環境は柔軟性が高いので、SQL Server データベース システムの予算や HADR 要件に応じて、部分的に、または完全に Azure に移動できます。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## HADR ソリューションの必要性

サービス レベル アグリーメント (SLA) が必要とする HADR 機能をデータベース システムが備えていることを確認するのは、管理者の責任です。Azure には、クラウド サービスのサービス復旧、Virtual Machines の障害復旧検出などの高可用性メカニズムが用意されていますが、それだけで必要な SLA が満たされていることが保証されるわけではありません。これらのメカニズムは VM の高可用性を保護しますが、VM 内で実行される SQL Server の高可用性は保護されません。VM がオンラインで正常であっても、SQL Server インスタンスで障害が発生する可能性があります。さらに、Azure の高可用性メカニズムがあっても、ソフトウェアまたはハードウェアの障害からの回復やオペレーティング システムのアップグレードなどのイベントのために、VM のダウンタイムが生じます。

また、Azure の geo 冗長ストレージ (GRS) は、geo レプリケーションと呼ばれる機能によって実装されており、データベースにとって適切な障害復旧ソリューションにはならない場合があります。geo レプリケーションでは、データを非同期的に送信するため、障害が発生すると、最新の更新が失われる場合があります。geo レプリケーションの制限事項の詳細については、「[別々のディスクのデータとログ ファイルでサポートされていない geo レプリケーション](#geo-replication-support)」セクションを参照してください。

## HADR デプロイ アーキテクチャ

Azure でサポートされている SQL Server HADR テクノロジは、次のとおりです。

- [AlwaysOn 可用性グループ](https://technet.microsoft.com/library/hh510230.aspx)
- [データベース ミラーリング](https://technet.microsoft.com/library/ms189852.aspx)
- [ログ配布](https://technet.microsoft.com/library/ms187103.aspx)
- [Azure BLOB ストレージ サービスを使用したバックアップと復元](https://msdn.microsoft.com/library/jj919148.aspx)
- [AlwaysOn フェールオーバー クラスター インスタンス](https://technet.microsoft.com/library/ms189134.aspx)

高可用性と障害復旧の両方の機能を持つ SQL Server ソリューションを実装するために、テクノロジを組み合わせることができます。使用するテクノロジによっては、ハイブリッド デプロイで VPN トンネルと Azure Virtual Network が必要になる場合があります。以下の各セクションで、デプロイ アーキテクチャのいくつかの例を示します。

## Azure のみ: 高可用性ソリューション

Azure 内の SQL Server データベースの高可用性ソリューションを実現するには、AlwaysOn 可用性グループまたはデータベース ミラーリングを使用します。

|テクノロジ|サンプル アーキテクチャ|
|---|---|
|**AlwaysOn 可用性グループ**|すべての可用性レプリカが、同じリージョン内で、高可用性のために Azure VM で実行されます。Windows Server フェールオーバー クラスタリング (WSFC) には Active Directory ドメインが必要であるため、ドメイン コントローラー VM を構成する必要があります。<br/> ![AlwaysOn 可用性グループ](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_ha_always_on.gif)<br/>詳細については、「[Azure VM での AlwaysOn 可用性グループの構成 (GUI)](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md)」をご覧ください。|**データベース ミラーリング**|高可用性のために、プリンシパル、ミラー、および監視サーバーが同じ Azure データセンターで実行されます。ドメイン コント ローラーを使用してデプロイすることができます。<br/>![データベース ミラーリング](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_ha_dbmirroring1.gif)<br/>また、代わりにサーバー証明書を使用して、ドメイン コントローラーなしで同じデータベース ミラーリング構成をデプロイすることもできます。<br/>![データベース ミラーリング](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_ha_dbmirroring2.gif)|
|**AlwaysOn フェールオーバー クラスター インスタンス**|共有記憶域を必要とするフェールオーバー クラスター インスタンス (FCI) は、2 つの異なる方法で作成できます。<br/><br/>1.サードパーティのクラスタリング ソリューションによってサポートされるストレージを使用する Azure VM で実行される 2 ノード WSFC 上の FCI。SIOS DataKeeper を使用する具体的な例については、「[High availability for a file share using WSFC and 3rd party software SIOS Datakeeper (WSFC とサードパーティ ソフトウェア SIOS Datakeeper を使用するファイル共有の高可用性)](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/)」を参照してください。<br/><br/>2.ExpressRoute を介してリモート iSCSI ターゲット共有ブロック記憶域を使用する Azure VM で実行されている 2 ノード WSFC 上の FCI。たとえば、NetApp Private Storage (NPS) は ExpressRoute と Equinix を使用して iSCSI ターゲットを Azure VM に公開します。<br/><br/>サードパーティの共有記憶域とデータ レプリケーション ソリューションの場合は、フェールオーバーでのデータ アクセスに関する問題について、ベンダーに問い合わせてください。<br/><br/>[Azure File Storage](https://azure.microsoft.com/services/storage/files/) 上での FCI の使用は、このソリューションが Premium Storage を利用していないためにまだサポートされていないことに注意してください。近日中にサポートできるように作業中です。|

## Azure のみ: 障害復旧ソリューション

Azure 内の SQL Server データベースの障害復旧ソリューションを実現するには、AlwaysOn 可用性グループ、データベース ミラーリング、またはストレージ BLOB によるバックアップと復元を使用します。

|テクノロジ|サンプル アーキテクチャ|
|---|---|
|**AlwaysOn 可用性グループ**|可用性レプリカが、障害復旧のために、Azure VM の複数のデータセンターで実行されます。この複数のリージョンにわたるソリューションでは、完全なサイトの機能停止の場合にも保護できます。<br/> ![AlwaysOn 可用性グループ](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_alwayson.png)<br/>リージョン内で、すべてのレプリカが同じクラウド サービスおよび同じ VNet 内にある必要があります。各リージョンは個別の VNet を持つため、これらのソリューションには VNet と VNet 間の接続が必要です。★詳細については、「[Azure クラシック ポータルでのサイト間 VPN の構成](../vpn-gateway/vpn-gateway-site-to-site-create.md)」を参照してください。|
|**データベース ミラーリング**|プリンシパルとミラーとサーバーが、障害復旧のために、異なるデータセンターで実行されます。Active Directory ドメインは、複数のデータセンターにまたがって機能できないため、サーバー証明書を使用してデプロイする必要があります。<br/>![データベース ミラーリング](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_dbmirroring.gif)|
|**Azure BLOB ストレージ サービスを使用したバックアップと復元**|実稼働データベースが、障害復旧のために、別のデータセンターの BLOB ストレージに直接バックアップされます。<br/>![バックアップと復元](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_backup_restore.gif)<br/>詳細については、「[Azure Virtual Machines 内の SQL Server のバックアップと復元](virtual-machines-windows-sql-backup-recovery.md)」を参照してください。|

## ハイブリッド IT: 障害復旧ソリューション

ハイブリッド IT 環境内の SQL Server データベースの障害復旧ソリューションを実現するには、AlwaysOn 可用性グループ、データベース ミラーリング、ログ配布、および Azure Blob ストレージによるバックアップと復元を使用します。

|テクノロジ|サンプル アーキテクチャ|
|---|---|
|**AlwaysOn 可用性グループ**|クロスサイト障害復旧のために、いくつかの可用性レプリカが Azure VM で実行され、その他のレプリカがオンプレミスで実行されます。実稼働サイトは、オンプレミスに置くことも、Azure データセンターに置くこともできます。<br/>![AlwaysOn 可用性グループ](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_alwayson.gif)<br/>すべての可用性レプリカは同じ WSFC クラスターにある必要があるため、WSFC クラスターは両方のネットワークにまたがっている必要があります (マルチサブネット WSFC クラスター)。この構成には、Azure とオンプレミス ネットワーク間の VPN 接続が必要です。<br/><br/>データベースの障害復旧を成功させるには、障害復旧サイトにレプリカ ドメイン コントローラーもインストールする必要があります。<br/><br/>SSMS のレプリカの追加ウィザードを使用して、既存の AlwaysOn 可用性グループに Azure レプリカを追加できます。詳細については、Azure への AlwaysOn 可用性グループの拡張に関するチュートリアルをご覧ください。|
|**データベース ミラーリング**|サーバー証明書を使用するクロスサイト障害復旧のために、1 つのパートナーが Azure VM で実行され、もう 1 つがオンプレミスで実行されます。パートナーは、同じ Active Directory ドメイン内にある必要がなく、VPN 接続も必要ありません。<br/>![データベース ミラーリング](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_dbmirroring.gif)<br/>もう 1 つのデータベース ミラーリング シナリオとして、クロスサイト障害復旧のために、1 つのパートナーを Azure VM で実行し、もう 1 つを同じ Active Directory ドメイン内のオンプレミスで実行するという形式があります。[Azure Virtual Network とオンプレミス ネットワーク間の VPN 接続](../vpn-gateway/vpn-gateway-site-to-site-create.md)が必要です。<br/><br/>データベースの障害復旧を成功させるには、障害復旧サイトにレプリカ ドメイン コントローラーもインストールする必要があります。|
|**ログ配布**|クロスサイト障害復旧のために、1 つのサーバーが Azure VM で実行され、もう 1 つがオンプレミスで実行されます。ログ配布は Windows ファイル共有に依存しているため、Azure Virtual Network とオンプレミス ネットワーク間の VPN 接続が必要です。<br/>![ログ配布](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_log_shipping.gif)<br/>データベースの障害復旧を成功させるには、障害復旧サイトにレプリカ ドメイン コントローラーもインストールする必要があります。|
|**Azure BLOB ストレージ サービスを使用したバックアップと復元**|オンプレミスの実稼働データベースが、障害復旧のために、Azure BLOB ストレージに直接バックアップされます。<br/>![バックアップと復元](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_backup_restore.gif)<br/>。詳細については、「[Azure Virtual Machines 内の SQL Server のバックアップと復元](virtual-machines-windows-sql-backup-recovery.md)」を参照してください。|

## Azure での SQL Server HADR の重要な考慮事項

Azure の VM、ストレージ、およびネットワークには、オンプレミスの非仮想化 IT インフラストラクチャとは異なる動作特性があります。Azure での HADR SQL Server ソリューションの実装を成功させるには、これらの違いを理解し、それに対応したソリューションを設計する必要があります。

### 可用性セットでの高可用性ノード

Azure の可用性セットを使用すると、高可用性ノードを別個の障害ドメイン (FD) と更新ドメイン (UD) に配置できます。Azure VM を同じ可用性セットに配置するには、同じクラウド サービスにデプロイする必要があります。同じクラウド サービス上にあるノードのみが同じ可用性セットに属することができます。詳細については、「[Virtual Machines の可用性管理](virtual-machines-windows-manage-availability.md)」を参照してください。

### Azure ネットワークでの WSFC クラスターの動作

Azure での非 RFC 準拠 DHCP サービスのために、特定の WSFC クラスター構成の作成が失敗する場合があります。これは、クラスター ネットワーク名に、重複する IP アドレス (クラスター ノードのいずれかと同じ IP アドレスなど) が割り当てられているためです。これは、WSFC 機能に依存する AlwaysOn 可用性グループを実装するときに問題になります。

2 ノード クラスターを作成し、オンラインにするシナリオを考えてみましょう。

1. クラスターがオンラインになると、NODE1 がクラスター ネットワーク名のために動的に割り当てられた IP アドレスを要求します。

1. DHCP サービスは、要求が NODE1 自体から送られてきていると認識するため、DHCP サービスからは NODE1 自体の IP アドレス以外の IP アドレスは取得できません。

1. NODE1 とクラスター ネットワーク名の両方に重複するアドレスが割り当てられていることが Windows によって検出され、既定のクラスター グループはオンラインになれません。

1. 既定のクラスター グループは、NODE2 に移動されます。NODE1 の IP アドレスはクラスター IP アドレスとして処理され、既定のクラスター グループがオンラインになります。

1. NODE2 が NODE1 との接続を確立しようとするときに、NODE1 宛てのパケットは、NODE1 の IP アドレスが NODE2 の IP アドレスに解決されるため、NODE2 から送信されません。NODE2 が NODE1 との接続を確立できず、クォーラムを失って、クラスターがシャットダウンされます。

1. 一方、NODE1 は NODE2 にパケットを送信できますが、NODE2 は応答できません。NODE1 はクォーラムを失い、クラスターをシャットダウンします。

このシナリオは、クラスター ネットワーク名をオンラインにするために、クラスター ネットワーク名にリンク ローカル IP アドレス (169.254.1.1 など) のような未使用の静的 IP アドレスを割り当てることによって回避できます。このプロセスを簡素化するには、[AlwaysOn 可用性グループのための Azure での Windows フェールオーバー クラスターの構成](http://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx)に関する記事をご覧ください。

詳細については、「[Azure VM での AlwaysOn 可用性グループの構成 (GUI)](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md)」をご覧ください。

### 可用性グループ リスナーのサポート

可用性グループ リスナーは、Windows Server 2008 R2、Windows Server 2012、および Windows Server 2012 R2 を実行している Azure VM でサポートされます。このサポートは、可用性グループ ノードである Azure VM 上で有効になっている負荷分散エンドポイントを使用して実現されます。Azure で実行されているクライアント アプリケーションと、オンプレミスで実行されているクライアント アプリケーションの両方に対してリスナーを動作させるには、特別な構成手順に従う必要があります。

リスナーを設定するための主なオプションには、外部 (パブリック) と内部の 2 つがあります。外部 (パブリック) リスナーは、インターネット経由でアクセスできるパブリック仮想 IP (VIP) と関連付けられます。外部リスナーでは、Direct Server Return を有効にする必要があります。つまり、AlwaysOn 可用性グループ ノードと同じクラウド サービスに含まれていないコンピューターからリスナーに接続する必要があります。もう 1 つのオプションは内部ロード バランサー (ILB) を使用する内部リスナーです。内部リスナーは、同じ Virtual Network 内のクライアントのみをサポートします。

可用性グループが複数の Azure サブネットにまたがっている場合 (たとえば、複数の Azure リージョンにわたるデプロイメント)、クライアント接続文字列には "**MultisubnetFailover=True**" を含める必要があります。これにより、別のサブネット内のレプリカへのパラレル接続が試行されます。リスナーの設定方法については、次を参照してください。

- [Azure での AlwaysOn 可用性グループの ILB リスナーの構成](virtual-machines-windows-classic-ps-sql-int-listener.md)
- [Azure での AlwaysOn 可用性グループの外部リスナーの構成](virtual-machines-windows-classic-ps-sql-ext-listener.md)

この場合でも、サービス インスタンスに直接接続することで、各可用性レプリカに個別に接続できます。また、AlwaysOn 可用性グループはデータベース ミラーリング クライアントとの下位互換性があるため、次のように、レプリカがデータベース ミラーリングと同様に構成されていれば、データベース ミラーリング パートナーのように可用性レプリカに接続できます。

- 1 つのプライマリ レプリカと 1 つのセカンダリ レプリカ

- セカンダリ レプリカが読み取り不可として構成されている (**[読み取り可能なセカンダリ]** オプションが **[いいえ]** に設定されている)

ADO.NET または SQL Server Native Client を使用する、このデータベース ミラーリングと同様の構成に対応するクライアント接続文字列の例を、次に示します。

	Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

クライアント接続の詳細については、以下を参照してください。

- [SQL Server Native Client での接続文字列キーワードの使用](https://msdn.microsoft.com/library/ms130822.aspx)
- [データベース ミラーリング セッションへのクライアントの接続 (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
- [ハイブリッド IT での可用性グループ リスナーへの接続](http://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
- [可用性グループ リスナー、クライアント接続、およびアプリケーションのフェールオーバー (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
- [可用性グループでのデータベース ミラーリング接続文字列の使用](https://technet.microsoft.com/library/hh213417.aspx)

### ハイブリッド IT でのネットワーク待ち時間

オンプレミス ネットワークと Azure 間に長いネットワーク待ち時間が生じる期間があることを前提にして、HADR ソリューションをデプロイする必要があります。レプリカを Azure にデプロイする際は、同期モードで、同期コミットではなく非同期コミットを使用してください。オンプレミスと Azure の両方にデータベース ミラーリング サーバーをデプロイする際は、高い安全性モードではなく高いパフォーマンス モードを使用してください。

### geo レプリケーション サポート

Azure ディスク内の geo レプリケーションでは、同じデータベースのデータ ファイルとログ ファイルを別個のディスクに格納することはできません。GRS は、各ディスク上の変更を個別に非同期的にレプリケートします。このメカニズムでは、1 つのディスク内の geo レプリケートされたコピーでの書き込み順序は保証されますが、複数のディスクでの geo レプリケートされた各コピーでは保証されません。データ ファイルとログ ファイルを個別のディスクに格納するようにデータベースを構成すると、障害発生後の復旧されたディスクには、ログ ファイルより新しいデータ ファイルのコピーが含まれる可能性があります。その場合、SQL Server の先書きログとトランザクションの ACID プロパティが破損します。ストレージ アカウントの geo レプリケーションを無効にするオプションがない場合は、特定のデータベースのすべてのデータ ファイルとログ ファイルを同じディスクに置く必要があります。データベースのサイズのために複数のディスクを使用する必要がある場合は、データの冗長性を確保するために、前に示した障害復旧ソリューションのいずれかをデプロイする必要があります。

## 次のステップ

Azure 仮想マシンと SQL Server を作成する必要がある場合は、「[Azure での SQL Server 仮想マシンのプロビジョニング](virtual-machines-windows-portal-sql-server-provision.md)」を参照してください。

Azure VM で実行されている SQL Server のパフォーマンスを最大限まで高めるには、「[Azure Virtual Machines における SQL Server のパフォーマンスに関するベスト プラクティス](virtual-machines-windows-sql-performance.md)」のガイダンスをご覧ください。

Azure VM での SQL Server の実行に関するその他のトピックについては、「[Azure Virtual Machines における SQL Server](virtual-machines-windows-sql-server-iaas-overview.md)」を参照してください。

### その他のリソース

- [Azure での新しい Active Directory フォレストのインストール](../active-directory/active-directory-new-forest-virtual-machine.md)
- [Azure VM での AlwaysOn 可用性グループの WSFC クラスターの作成](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)

<!---HONumber=AcomDC_0511_2016-->