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
	ms.date="03/29/2016"
	ms.author="raynew"/>

# Azure Site Recovery のデプロイの準備

Azure Site Recovery サービスでサポートされている各レプリケーション シナリオのデプロイ要件の概要については、この記事をご覧ください。各シナリオの一般要件をお読みになった後には、各デプロイ記事の前提条件セクションに記載されている具体的なデプロイ詳細へのリンクをご利用ください。

この記事に関するコメントや質問は、この記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。

## 概要

組織には、予定されたダウンタイムおよび予定外のダウンタイム時にアプリケーション、ワークロード、およびデータを実行され利用可能な状態に維持し、できるだけ早く通常の動作状態に復旧させる方法を決定するビジネス継続性および障害復旧 (BCDR) の戦略が必要です。BCDR 戦略センターは、災害発生時にビジネス データを安全かつ回復可能な状態に維持し、ワークロードを継続的に利用可能な状態に保つために中心的な役割を果たすソリューションです。

Site Recovery とは、クラウド (Azure) またはセカンダリ データセンターへのオンプレミスの物理サーバーおよび仮想マシンのレプリケーションを統制することで BCDR 戦略を支援する Azure サービスです。プライマリの場所で障害が発生した場合は、セカンダリ サイトにフェールオーバーしてアプリとワークロードの可用性を維持します。プライマリの場所が通常の動作に戻ると、その場所にフェールバックします。Site Recovery は、さまざまなシナリオで使用され、数多くのワークロードを保護することができます。詳細については、「[Azure Site Recovery とは](site-recovery-overview.md)」をご覧ください。


## Hyper-V 仮想マシンをレプリケートするための要件

**コンポーネント** | **Azure にレプリケート (VMM を使用)** | **Azure にレプリケート (VMM を使用しない)** | **セカンダリ サイトにレプリケート (VMM を使用)**
---|---|---|---
**VMM** | 1 つまたは複数の VMM サーバーが System Center 2012 R2 で実行されていること。VMM サーバーには、1 つまたは複数の VMM ホスト グループを含むクラウドが少なくとも 1 つ必要です。 | 適用不可 | 少なくとも 1 つの VMM サーバーが System Center 2012 R2 で実行されていること。各サイトに 1 つの VMM サーバーをお勧めします。VMM サーバーには、1 つまたは複数の VMM ホスト グループを含むクラウドが少なくとも 1 つ必要です。クラウドには、Hyper-V 機能プロファイルが設定されている必要があります。 
**Hyper-V** | Windows Server 2012 R2 以上を実行しているオンプレミスのデータセンターに、1 つまたは複数の Hyper-V ホスト サーバーがあること。Hyper-V サーバーが VMM クラウド内のホスト グループに配置されている必要があります。 | Windows Server 2012 R2 以上を実行しているソース サイトとターゲット サイトに、1 つまたは複数の Hyper-V サーバーがあること。 | 最新の更新プログラムが適用された Windows Server 2012 以上を実行しているソース サイトとターゲット サイトに、1 つまたは複数の Hyper-V サーバーがあること。Hyper-V サーバーが VMM クラウド内のホスト グループに配置されている必要があります。
**仮想マシン** | ソース Hyper-V サーバーに少なくとも 1 つの VM が必要です。Azure にレプリケートする VM が [Azure 仮想マシンの前提条件](#azure-virtual-machine-requirements)を満たしている必要があります。<br>[こちら](https://technet.microsoft.com/library/hh846766.aspx#BKMK_step4)で説明されている手順に従い、VM の[統合サービス](https://technet.microsoft.com/library/dn798297.aspx)をインストールまたはアップグレードしてください。 | ソース Hyper-V サーバーに少なくとも 1 つの VM があること。Azure にレプリケートする VM が [Azure 仮想マシンの前提条件](#azure-virtual-machine-requirements)を満たしている必要があります。<br>[こちら](https://technet.microsoft.com/library/hh846766.aspx#BKMK_step4)で説明されている手順に従い、VM の[統合サービス](https://technet.microsoft.com/library/dn798297.aspx)をインストールまたはアップグレードしてください。 | ソース VMM クラウドに少なくとも 1 つの VM があること。<br>[こちら](https://technet.microsoft.com/library/hh846766.aspx#BKMK_step4)で説明されている手順に従い、VM の[統合サービス](https://technet.microsoft.com/library/dn798297.aspx)をインストールまたはアップグレードしてください。
**Azure アカウント** | [Azure](https://azure.microsoft.com/) アカウントとサブスクリプションが必要です。 | 適用不可 | [Azure](https://azure.microsoft.com/) アカウントとサブスクリプションが必要です。
**Azure Storage** | レプリケートしたデータを格納するには [Azure ストレージ アカウント](../storage/storage-redundancy.md#geo-redundant-storage)が必要になります。レプリケートされたデータは Azure Storage に格納され、フェールオーバーが発生すると、Azure VM はスピンアップされます。 | 適用不可 | レプリケートしたデータを格納するには [Azure ストレージ アカウント](../storage/storage-redundancy.md#geo-redundant-storage)が必要になります。レプリケートされたデータは Azure Storage に格納され、フェールオーバーが発生すると、Azure VM はスピンアップされます。
**プロバイダー/エージェント** | デプロイのときに、Azure Site Recovery プロバイダーを VMM サーバーにインストールし、Azure Recovery Services エージェントを Hyper-V ホスト サーバーにインストールします。プロバイダーは、Azure Site Recovery と通信します。エージェントは、ソースとターゲットの Hyper-V サーバー間のデータ レプリケーションを処理します。VM には何もインストールされていません。 | デプロイのときに、Azure Site Recovery プロバイダーと Azure Recovery Services エージェントの両方を Hyper-V ホスト サーバーまたはクラスターにインストールします。VM には何もインストールされていません。 | デプロイのときに、Azure Site Recovery プロバイダーを VMM サーバーにインストールして、Azure Site Recovery と通信できるようにします。レプリケーションは LAN/VPN で Hyper-V ソース サーバーとターゲット サーバーの間で発生します。
**プロバイダー/エージェント接続** | プロバイダーから Site Recovery サービスにプロキシ経由で接続する場合は、プロキシから Site Recovery の URL にアクセスできることを確認する必要があります。 | プロバイダーから Site Recovery にプロキシ経由で接続する場合は、プロキシから Site Recovery の URL にアクセスできることを確認する必要があります。 | プロバイダーから Site Recovery にプロキシ経由で接続する場合は、プロキシから Site Recovery の URL にアクセスできることを確認する必要があります。
**インターネット接続** | VMM サーバーおよび Hyper-V ホストから。 | Hyper-V ホストから。 | VMM サーバー上のみ。
**ネットワーク マッピング** | 仮想マシンがどの復旧計画に含まれているのかに関係なく、同じ Azure ネットワーク上でフェールオーバーするすべての仮想マシンが相互に通信できるように、ネットワーク マッピングをセットアップします。ターゲット Azure ネットワーク上にネットワーク ゲートウェイが存在する場合は、仮想マシンはオンプレミスの仮想マシンにも接続できます。ネットワーク マッピングを設定しない場合は、同じ復旧計画に属する、フェールオーバーするマシンのみが接続できます。 | 適用不可 | 仮想マシンがフェールオーバー後に適切なネットワークに確実に接続されて、レプリカ仮想マシンがターゲット Hyper-V ホスト サーバーに最適に配置されるように、ネットワーク マッピングをセットアップします。ネットワーク マッピングを構成しない場合、フェールオーバー後に、レプリケートされたマシンは、どの VM ネットワークにも接続されません。
**ストレージ マッピング** | 適用不可 | 適用不可 | 仮想マシンがフェールオーバー後にストレージに最適に接続されるように、ストレージ マッピング オプションを任意でセットアップできます (既定で、レプリカ VM はターゲット Hyper-V サーバー上に指定された場所に格納されます)。
**SAN レプリケーション** | 適用不可 | 適用不可 | 2 つのオンプレミス VMM サイト間で SAN レプリケーションを使用してレプリケートする場合は、既存の SAN 環境を使用することができます。[サポートされる SAN 配列](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx)に関するページをご覧ください。
**詳細情報** | [詳細なデプロイメントの前提条件](site-recovery-vmm-to-azure.md#before-you-start) | [詳細なデプロイメントの前提条件](site-recovery-hyper-v-site-to-azure.md#before-you-start#before-you-start) | [詳細なデプロイメントの前提条件](site-recovery-vmm-to-vmm.md#before-you-start)




## VMware VM と物理サーバーをレプリケートするための要件

次の表は、VMware VM と Windows/Linux 物理サーバーを Azure とセカンダリ サイトにレプリケートするための要件をまとめたものです。

>[AZURE.NOTE] VMware VM と物理サーバーを Azure にレプリケートするには、[強化された](site-recovery-vmware-to-azure-classic.md)デプロイ モデルを使用するか、または古いデプロイに使用されていた[レガシ](site-recovery-vmware-to-azure-classic-legacy.md) モデルを使用します。次の表には、強化されたモデルのデプロイ要件が含まれています。

**コンポーネント** | **Azure にレプリケート (拡張)** | **セカンダリ サイトへのレプリケート**
---|---|---
**オンプレミスのプライマリ サイト** | すべての Site Recovery コンポーネント (構成、プロセス、マスター ターゲット) を実行する管理サーバーをインストールします。 | レプリケーション データのキャッシュ、圧縮、および暗号化のためのプロセス サーバーをインストールしてから、セカンダリ サイトに送信します。負荷分散やフォールト トレランスのための他のプロセス サーバーをインストールすることができます。 
**オンプレミスのセカンダリ サイト** | 適用不可 | デプロイの構成、管理、および監視に使用される 1 つの構成サーバーをインストールします。<br/><br>サーバー管理を容易に構成できるように、vContinuum サーバーをインストールすることをお勧めします。<br/><br/>セカンダリ vSphere サーバーで実行されている VM としてマスター ターゲット サーバーをセットアップする必要があります。 
**VMware vCenter/ESXi** | プライマリ サイトに VMware VM をレプリケートする場合 (または物理サーバーをフェールバックする場合)、プライマリ サイトに vSphere ESX/ESXi が必要です。ESXi ホストを管理する vCenter サーバーもお勧めします。 | プライマリ サイトとセカンダリ サイトに 1 つまたは複数の VMware ESXi ホスト (必要に応じて vCenter サーバーも) が必要です。 
**フェールバック** | 物理サーバーをレプリケートする場合でも、Azure からフェールバックする VMware 環境が必要です。<br/><br/>Azure VM としてプロセス サーバーをセットアップする必要があります。<br/><br/>構成サーバーがマスター ターゲット サーバーとして動作しますが、大量のトラフィックをフェールバックする場合は、その他のオンプレミス マスター ターゲット サーバーをセットアップすることが必要になる可能性もあります。[詳細情報](site-recovery-failback-azure-to-vmware-classic.md)| 物理マシンでフェールオーバーした場合でも、セカンダリ サイトからプライマリ サイトへのフェールバックは VMware に限られます。フェールバックのために、マスター ターゲット サーバーをプライマリ vSphere サーバー上の VM としてセットアップする必要があります。
**Azure アカウント** | [Azure](https://azure.microsoft.com/) アカウントとサブスクリプションが必要です。 | 適用不可
**Azure Storage** | レプリケートしたデータを格納するには [Azure ストレージ アカウント](../storage/storage-redundancy.md#geo-redundant-storage)が必要になります。レプリケートされたデータは Azure Storage に格納され、フェールオーバーが発生すると、Azure VM はスピンアップされます。 | 適用不可
**Azure Virtual Network** | フェールオーバーが発生した場合に Azure VM が接続する Azure 仮想ネットワークが必要です。フェールオーバー後にフェールバックするには、Azure ネットワークからオンプレミス サイトへの VPN 接続 (または Azure ExpressRoute) のセットアップが必要です。 | 適用不可
**保護されたマシン** | 少なくとも 1 つの VMware 仮想マシンまたは物理 Windows/Linux サーバー。デプロイのときに、レプリケートする各マシンに Mobility サービスがインストールされます。 | 少なくとも 1 つの VMware 仮想マシンまたは物理 Windows/Linux サーバー。デプロイのときに、レプリケートする各マシンに統合エージェントがインストールされます。
**接続** | 管理サーバーからプロキシ経由で Site Recovery に接続する場合、プロキシ サーバーが特定の URL に接続できることを確認する必要があります。 | 構成サーバーがインターネットにアクセスできる必要があります。
**詳細** | [詳細なデプロイメントの前提条件](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)。 | InMage Scout ユーザー ガイドを[ダウンロード](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf)します。


## Azure 仮想マシンの要件

Site Recovery をデプロイすると、Azure でサポートされた任意のオペレーティング システムを実行している仮想マシンまたは物理サーバーをレプリケートできます。これには、Windows と Linux のほとんどのバージョンが含まれます。保護するオンプレミスの仮想マシンが Azure 要件を満たしていることを確認する必要があります。


**機能** | **要件** | **詳細**
---|---|---
HYPER-V ホスト | Windows Server 2012 R2 が実行されている必要があります。 | オペレーティング システムがサポートされていない場合は、前提条件の確認は失敗します。
VMware ハイパーバイザー | サポートされるオペレーティング システム | [要件の確認](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)
ゲスト オペレーティング システム | Hyper-V から Azure へのレプリケーション: Site Recovery では、[Azure でサポートされている](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)すべてのオペレーティング システムがサポートされます。<br/><br/> VMware と物理サーバーのレプリケーション: Windows と Linux の[前提条件](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)を確認します。 | サポートされていない場合、前提条件の確認は失敗します。
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
- **容量計画**: Site Recovery の[容量計画](site-recovery-capacity-planner.md)のページをご覧ください。
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

<!---HONumber=AcomDC_0413_2016-->