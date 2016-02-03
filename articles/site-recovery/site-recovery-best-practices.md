<properties
	pageTitle="Site Recovery のデプロイの準備 | Microsoft Azure"
	description="Azure Site Recovery は、オンプレミスのサーバーに配置されている仮想マシンと物理サーバーの Azure またはセカンダリ データセンターへのレプリケーション、フェールオーバー、および復旧を調整します。"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="12/07/2015"
	ms.author="raynew"/>

# Azure Site Recovery のデプロイの準備

## この記事の内容

この記事では、Azure Site Recovery をデプロイするための準備の方法について説明します。この記事の内容について質問がある場合は、[Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。

## Hyper-V 仮想マシンの保護

Hyper-V 仮想マシンを保護するためのデプロイとしていくつかのオプションがあります。オンプレミスの Hyper-VM は、Azure にレプリケートすることも、セカンダリ データセンターにレプリケートすることもできます。各デプロイには、要件に次のような違いがあります。

**要件** | **Azure にレプリケート (VMM を使用)** | **Azure に Hyper-V VM をレプリケート (VMM なし)** | **セカンダリ サイトに Hyper-V VM をレプリケート (VMM を使用)** | **詳細**
---|---|---|---|---
**VMM** | System Center 2012 R2 で実行されている VMM <br/><br/> 1 つ以上の VMM ホスト グループを含む少なくとも 1 つの VMM クラウド。 | 該当なし | 最新の更新プログラムが適用された少なくとも 1 つの System Center 2012 SP1 で実行されている、プライマリおよびセカンダリ サイト内の VMM サーバー。<br/><br/> 各 VMM サーバー上の少なくとも 1 つのクラウド。クラウドには Hyper-V キャパシティ プロファイル セットが必要です。<br/><br/> ソース クラウドには、少なくとも 1 つの VMM ホスト グループが必要です。 | 省略可能。Hyper-V 仮想マシンを Azure にレプリケートする上で System Center VMM をデプロイする必要はありません。デプロイした場合は、VMM サーバーが正しくセットアップされていることを確認する必要があります。具体的には、適切な VMM バージョンを実行しているかどうか、クラウドがセットアップされているかどうか、などを確認します。
**Hyper-V** | Windows Server 2012 R2 以降を実行している、オンプレミスのデータ センター内の少なくとも 1 つの Hyper-V ホスト サーバー | Windows Server 2012 R2 以降を実行していてインターネットに接続されている、ソースおよびターゲット サイト内の少なくとも 1 つの Hyper-V サーバー。<br/><br/> Hyper-V サーバーは、VMM クラウド内のホスト グループに属している必要があります。 | 最新の更新プログラムが適用された少なくとも 1 つの Windows Server 2012 を実行していてインターネットに接続されている、ソースおよびターゲットサイト内の少なくとも 1 つの Hyper-V サーバー。<br/><br/> Hyper-V サーバーは、VMM クラウド内のホスト グループに属している必要があります。 | 
**仮想マシン** | ソース Hyper-V ホスト サーバー上の少なくとも 1 つの VM | ソース VMM クラウド内の Hyper-V ホスト サーバーにある少なくとも 1 つの VM | ソース VMM クラウド内の Hyper-V ホスト サーバーにある少なくとも 1 つの VM。 | Azure にレプリケートする VM は [Azure の仮想マシンの前提条件](site-recovery-best-practices.md/#virtual-machines)を満たす必要があります。
**Azure アカウント** | [Azure](https://azure.microsoft.com/) アカウントと、Site Recovery サービスへのサブスクリプションが必要となります。 | [Azure](https://azure.microsoft.com/) アカウントと、Site Recovery サービスへのサブスクリプションが必要となります。 | 該当なし | アカウントをお持ちでない場合は、[無料試用版](pricing/free-trial/)で作業を開始してください。サービスの価格については、[こちら](pricing/details/site-recovery/)を参照してください。
**Azure Storage** | geo レプリケーションが有効になっている Azure ストレージ アカウントのサブスクリプションが必要です。 | geo レプリケーションが有効になっている Azure ストレージ アカウントのサブスクリプションが必要です。 | 該当なし | アカウントは Azure Site Recovery コンテナーと同じリージョンである必要があり、同じサブスクリプションに関連付けられている必要があります。ストレージの詳細は[こちら](../storage/storage-introduction.md)です。
**ストレージ マッピング** | 該当なし | 該当なし | 仮想マシンがフェールオーバー後に最適な状態で確実にストレージに接続されるように、ストレージ マッピング オプションを設定できます。2 つのオンプレミス VMM サイト間でレプリケートする場合、既定では、レプリカの仮想マシンは、ターゲットの Hyper-V ホスト サーバー上の指定された場所に格納されます。ソースとターゲットの VMM サーバーにある、VMM ストレージの分類間でのマッピングを構成することができます。 | この機能を使用するには、デプロイを開始する前にストレージの分類を設定する必要があります。詳細については、[こちら](site-recovery-storage-mapping.md)を参照してください。
**SAN レプリケーション** | 該当なし | 該当なし | 2 つのオンプレミス VMM サイト間で SAN レプリケーションを使用してレプリケートする場合は、既存の SAN 環境を使用することができます。 | [サポートされた SAN アレイ](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx)が必要となります。さらに、VMM で SAN ストレージを検出および分類する必要があります。<br/><br/>現時点で、レプリケートしない場合は、VMM コンソールで LUN を作成し、ストレージを割り当てる必要があります。既にレプリケートを実行している場合、この手順はスキップできます。
**ネットワーク** | ネットワーク マッピングをセットアップすると、属している復旧計画の種類にかかわらず、同じ Azure ネットワーク上でフェールオーバーするすべての仮想マシンが相互に通信できるようになります。さらに、ネットワーク ゲートウェイがターゲットの Azure ネットワーク上で構成されている場合、仮想マシンは他のオンプレミス仮想マシンに接続できます。ネットワーク マッピングを設定しない場合は、同じ復旧計画に属する、フェールオーバーするマシンのみが接続できます。 | 該当なし | <br/><br/>ネットワーク マッピングをセットアップすると、フェールオーバー後に仮想マシンが適切なネットワークに確実に接続され、レプリカ仮想マシンが Hyper-V ホスト サーバーに最適に配置されます。ネットワーク マッピングを構成しない場合、フェールオーバー後に、レプリケートされたマシンは、どの VM ネットワークにも接続されません。 | ネットワーク マッピングの詳細については、[ここ](site-recovery-network-mapping)を参照してください。<br/><br/> VMM でネットワーク マッピングをセットアップするには、VMM 論理ネットワークと VM ネットワークが正しく構成されていることを確認する必要があります。[詳細](http://blogs.technet.com/b/scvmm/archive/2013/02/14/networking-in-vmm-2012-sp1-logical-networks-part-i.aspx)と [VM ネットワーク](https://technet.microsoft.com/library/jj721575.aspx)に関するページを参照してください。また、[VMM のネットワークに関する考慮事項](site-recovery-network-design.md/#vmm-design)に関するページもお読みください。  
**プロバイダーおよびエージェント** | デプロイ時には、VMM サーバーに Azure Site Recovery プロバイダーをインストールします。VMM クラウド内にある Hyper-V サーバー上で、Azure Recovery Services エージェントをインストールします。 | デプロイ時には、Azure Site Recovery プロバイダーと Azure Recovery Services エージェントの両方を、Hyper-V ホスト サーバーまたはクラスター ノードにインストールします。| デプロイ時には、VMM サーバーに Azure Site Recovery プロバイダーをインストールします。VMM クラウド内にある Hyper-V サーバー上で、Azure Recovery Services エージェントをインストールします。 | プロバイダーとエージェントは、インターネット経由で、暗号化された HTTPS 接続を使用して Site Recovery に接続されます。ファイアウォールの例外を追加したり、プロバイダー接続用に特定のプロキシを作成したりする必要はありません。ただし、カスタム プロキシを使用する場合は、デプロイを開始する前に次の URL にファイアウォール経由で接続できるようにします。<br/><br/> *.hypervrecoverymanager.windowsazure.com <br/><br/> *.accesscontrol.windows.net<br/><br/> *.backup.windowsazure.com <br/><br/> *.blob.core.windows.net <br/><br/>*.store.core.windows.net
**インターネット接続** | VMM サーバーに限りインターネット接続が必要です。 | Hyper-V ホストに限りインターネット接続が必要です。 | VMM サーバーに限りインターネット接続が必要です。 | 仮想マシンにはインストールしておくものは何もありません。直接インターネットに接続する必要もありません。



## VMware 仮想マシンまたは物理サーバーの保護

VMware 仮想マシンまたは Windows/Linux 物理サーバーを保護するデプロイ オプションがいくつかがあります。それらは、Azure にレプリケートすることも、セカンダリ データセンターにレプリケートすることもできます。各デプロイには、要件に次のような違いがあります。

**要件** | **Azure に VMware VM/物理サーバーをレプリケート** | * **セカンダリ サイトに VMware VM/物理サーバーをレプリケート**  
---|---|--- 
**プライマリ サイト** | **プロセス サーバー**: 専用の Windows サーバー (物理または仮想) | **プロセス サーバー**: 専用の Windows サーバー (物理または VMware 仮想マシン<br/><br/>  
**セカンダリ オンプレミス サイト** | 該当なし | **構成サーバー**: 専用の Windows サーバー (物理または仮想) <br/><br/> **マスター ターゲット サーバー**: 専用のサーバー (物理または仮想)。Windows マシンを保護するには Windows で、Linux マシンを保護するには Linux で構成を行います。
**Azure** | **サブスクリプション**: Site Recovery サービスのサブスクリプションが必要です。価格については[こちら](pricing/details/site-recovery/)をご覧ください。<br/><br/> **ストレージ アカウント**: geo レプリケーションが有効になっているストレージ アカウントが必要です。アカウントは Site Recovery コンテナーと同じリージョンである必要があり、同じサブスクリプションに関連付けられている必要があります。[詳細](../storage/storage-introduction.md)。<br/><br/> **構成サーバー**: 構成サーバーを Azure VM としてセットアップする必要があります <br/><br/> **マスター ターゲット サーバー**: マスター ターゲット サーバーを Azure VM としてセットアップする必要があります <br/><br/> Windows マシンを保護するには Windows で、Linux マシンを保護するには Linux で構成を行います。<br/><br/> **Azure 仮想ネットワーク**: 構成サーバーとターゲット マスター サーバーのデプロイ先となる Azure 仮想ネットワークが必要になります。Azure 仮想ネットワークは、Azure Site Recovery コンテナーと同じリージョンである必要があり、同じサブスクリプションに関連付けられている必要があります。 | 該当なし  
**仮想マシン/物理サーバー** | 少なくとも 1 つの VMware 仮想マシンまたは Windows/Linux 物理サーバー<br/><br/>デプロイ時には、各マシンにモビリティ サービスがインストールされます。| 少なくとも 1 つの VMware 仮想マシンまたは Windows/Linux 物理サーバー。<br/><br/> デプロイ時には、各マシンに統合エージェントがインストールされます。




## Azure 仮想マシンの要件

Site Recovery をデプロイすると、Azure でサポートされた任意のオペレーティング システムを実行している仮想マシンまたは物理サーバーをレプリケートできます。これには、Windows と Linux のほとんどのバージョンが含まれます。保護するオンプレミスの仮想マシンが Azure 要件を満たしていることを確認する必要があります。


**機能** | **サポート** | **詳細**
---|---|---
Hyper-V ホスト オペレーティング システム | Windows Server 2012 R2 | サポートされていない場合、前提条件の確認は失敗します。
VMware ハイパーバイザー オペレーティング システム | サポートされたオペレーティング システムの実行 | [詳細](site-recovery-vmware-to-azure.md/#before-you-start) 
ゲスト オペレーティング システム | Hyper-V から Azure へのレプリケーションの場合、Site Recovery では、[Azure でサポートされた](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)すべてのオペレーティング システムがサポートされます。<br/><br/> VMware と物理サーバーのレプリケーションの場合、Windows と Linux の[前提条件](site-recovery-vmware-to-azure.md/#before-you-start)を確認します。 | サポートされていない場合、前提条件の確認は失敗します。 
ゲスト オペレーティング システムのアーキテクチャ | 64 ビット | サポートされていない場合、前提条件の確認は失敗します。
オペレーティング システムのディスク サイズ | 最大 1023 GB | サポートされていない場合、前提条件の確認は失敗します。
オペレーティング システムのディスク数 | 1 | サポートされていない場合、前提条件の確認は失敗します。 
データ ディスク数 | 16 以下 (最大値は、作成される仮想マシンのサイズの関数であり、16 = XL で求められます。) | サポートされていない場合、前提条件の確認は失敗します。
データ ディスク VHD のサイズ | 最大 1023 GB | サポートされていない場合、前提条件の確認は失敗します。
ネットワーク アダプター | 複数のアダプターがサポートされます。 |
静的 IP アドレス | サポートされています | プライマリ仮想マシンが静的 IP アドレスを使用している場合、Azure で作成される仮想マシンに静的 IP アドレスを指定できます。
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
- **容量計画**: Hyper-V VM をレプリケートする場合は、「[Capacity Planner](http://www.microsoft.com/download/details.aspx?id=39057)」をお読みください。
- **レプリケーションの帯域幅**: レプリケーションの帯域幅が不足する場合は、次の点に注意してください。
	- **ExpressRoute**: Site Recovery は、Azure ExpressRoute や、Riverbed などの WAN オプティマイザーと連携します。ExpressRoute の詳細については、[こちら](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx)を参照してください。
	- **レプリケーション トラフィック**: Site Recovery は、VHD 全体ではなく、データ ブロックのみを使用してスマート初期レプリケーションを実行します。レプリケーション進行中にレプリケートされるのは、変更箇所のみです。
	- **ネットワーク トラフィック**: 送信先 IP アドレスとポートに基づくポリシーを用いて [Windows QoS](https://technet.microsoft.com/library/hh967468.aspx) を設定することで、レプリケーションに使用されるネットワーク トラフィックを制御できます。さらに、Azure Backup エージェントを使用して、Azure Site Recovery にレプリケートする場合には、そのエージェントの調整を構成することができます。詳細については、[こちら](https://support.microsoft.com/kb/3056159)を参照してください。
- **RTO**: Site Recovery を使用した場合に予期できる回復時刻の目標 (RTO) を測定する場合は、テスト フェールオーバーを実行し、Site Recovery ジョブを表示して、操作を完了するためにかかる時間を分析することをお勧めします。Azure にフェールオーバーする場合、最適な RTO を実現するには、Site Recovery を Azure Automation と復旧計画に統合して、すべての手動操作を自動化することをお勧めします。
- **RPO**: Azure にレプリケートするときには、Site Recovery は、ほぼ同期の回復ポイントの目標 (RPO) をサポートします。これは、データセンターと Azure の間に十分な帯域幅があることを前提としています。





## 次のステップ

これらのベスト プラクティスを確認した後に、Site Recovery のデプロイを開始できます。

- [オンプレミスの VMM サイトと Azure 間の保護の設定](site-recovery-vmm-to-azure.md)
- [Set up protection between an on-premises Hyper-V site and Azure (オンプレミスの Hyper-V サイトと Azure 間の保護の設定)](site-recovery-hyper-v-site-to-azure.md)
- [Set up protection between two on-premises VMM sites (2 つのオンプレミスの VMM サイト間の保護の設定)](site-recovery-vmm-to-vmm.md)
- [Set up protection between two on-premises VMM sites with SAN (SAN を使用した 2 つのオンプレミスの VMM サイト間の保護の設定)](site-recovery-vmm-san.md)
- [単一の VMM サーバーを使用した保護の設定](site-recovery-single-vmm.md)
 

<!-----HONumber=AcomDC_0128_2016-->