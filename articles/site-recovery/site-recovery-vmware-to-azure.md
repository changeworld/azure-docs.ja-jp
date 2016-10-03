<properties
	pageTitle="Azure ポータルで Azure Site Recovery を使用して VMware 仮想マシンと物理サーバーを Azure にレプリケートする | Microsoft Azure"
	description="Azure ポータルで Azure Site Recovery をデプロイして、オンプレミスの VMware 仮想マシンと Windows/Linux 物理サーバーの Azure へのレプリケーション、フェールオーバー、復旧を調整する方法を説明します。"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/12/2016"
	ms.author="raynew"/>

# Azure ポータルで Azure Site Recovery を使用して VMware 仮想マシンと物理マシンを Azure にレプリケートする

> [AZURE.SELECTOR]
- [Azure ポータル](site-recovery-vmware-to-azure.md)
- [Azure クラシック](site-recovery-vmware-to-azure-classic.md)
- [Azure クラシック (レガシ)](site-recovery-vmware-to-azure-classic-legacy.md)

Azure Site Recovery へようこそ。 この記事では、Azure ポータルで Azure Site Recovery を使用して、オンプレミスの VMware 仮想マシンまたは Windows/Linux 物理サーバーを Azure にレプリケートする方法について説明します。

> [AZURE.NOTE] Azure には、リソースの作成と操作に関して、Azure Resource Manager (ARM) とクラシックの 2 種類の[デプロイメント モデル](../resource-manager-deployment-model.md)があります。また、Azure にも 2 つのポータルがあります。クラシック デプロイメント モデルをサポートする Azure クラシック ポータルと、両方のデプロイメント モデルをサポートする Azure ポータルです。

Azure ポータルの Site Recovery には、次のような多数の新機能が用意されています。

- Azure Backup サービスと Azure Site Recovery サービスが 1 つの Recovery Services コンテナーに統合されたため、ビジネス継続性と障害復旧 (BCDR) を 1 つの場所から設定して管理できます。統合されたダッシュボードで、オンプレミス サイトと Azure パブリック クラウド全体にわたる監視と管理の操作を実行できます。
- Azure サブスクリプションがクラウド ソリューション プロバイダー (CSP) プログラムを使用してプロビジョニングされたユーザーは、Azure ポータルで Site Recovery 操作を管理できるようになりました。
- Azure ポータルの Site Recovery では、マシンを ARM ストレージ アカウントにレプリケートできます。フェールオーバー時には、Azure に ARM ベースの VM が作成されます。
- Site Recovery では、従来のストレージ アカウントへのレプリケーションが引き続きサポートされます。フェールオーバー時には、クラシック モデルを使用して VM が作成されます。

この記事に関するコメントがありましたら、下部にある DISQUS コメント欄に投稿してください。技術的な質問については、[Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。

## 概要

組織には、予定されたダウンタイムおよび予定外のダウンタイム時にアプリ、ワークロード、およびデータの実行と利用可能な状態を維持し、できるだけ早く通常の動作状態に復旧させる方法を決定する BCDR の戦略が必要です。BCDR 戦略は、災害発生時にビジネス データを安全かつ回復可能な状態に維持し、ワークロードが継続的に利用可能な状態に保たれるようなものである必要があります。

Site Recovery とは、クラウド (Azure) またはセカンダリ データセンターへのオンプレミスの物理サーバーおよび仮想マシンのレプリケーションを統制することで BCDR 戦略を支援する Azure サービスです。プライマリ ロケーションで障害が発生した場合は、セカンダリ ロケーションにフェールオーバーしてアプリとワークロードの可用性を維持します。プライマリの場所が通常の動作に戻ると、その場所にフェールバックします。詳細については、「[Site Recovery とは](site-recovery-overview.md)」をご覧ください。

この記事には、オンプレミスの VMware VM と Windows/Linux 物理サーバーを Azure にレプリケートするために必要な情報がすべて記載されています。この情報には、アーキテクチャの概要、計画の詳細、および Azure、オンプレミス サーバー、レプリケーション設定、およびキャパシティ プランニングを構成するためのデプロイメント手順が含まれます。インフラストラクチャをセットアップした後で、保護するマシンでレプリケーションを有効にし、フェールオーバーが機能することを確認します。

## ビジネス上の利点

- Site Recovery では、VMware VM と物理サーバーで実行されているビジネス ワークロードやアプリケーションのオフサイト保護を行うことができます。
- Recovery Services ポータルでは、1 つの場所でレプリケーション、フェールオーバー、および復旧のセットアップ、管理、および監視を実行できます。
- Site Recovery では、vSphere ホストに追加された VMware VM を自動的に検出できます。
- オンプレミス インフラストラクチャから Azure へのフェールオーバーと、Azure からオンプレミス サイトの VMware VM サーバーへのフェールバック (復元) を簡単に実行できます。
- マルチ VM を有効にし、レプリケーション グループを作成することで、複数のマシン間で階層化されたアプリケーション ワークロードを同時にレプリケートできます。レプリケーション グループのすべてのマシンが、フェールオーバー時にクラッシュ整合性復旧ポイントとアプリ整合性復旧ポイントを持ちます。フェールオーバーの場合、復旧計画で複数のマシンをまとめることで、階層化されたアプリケーション ワークロードをまとめてフェールオーバーできます。

## サポートされているオペレーティング システム

### Windows (64 ビットのみ)
- Windows Server 2008 R2 SP1+
- Windows Server 2012
- Windows Server 2012 R2

### Linux (64 ビットのみ)
- Red Hat Enterprise Linux 6.7、7.1、7.2
- CentOS 6.5、6.6、6.7、7.0、7.1、7.2
- Red Hat 互換カーネルまたは Unbreakable Enterprise Kernel リリース 3 (UEK3) を実行している Oracle Enterprise Linux 6.4、6.5
- SUSE Linux Enterprise Server 11 SP3

## シナリオのアーキテクチャ

シナリオの構成要素を次に示します。

- **構成サーバー**: 通信を調整し、データ レプリケーションと復旧プロセスを管理するオンプレミスのマシン。このマシンで 1 つのセットアップ ファイルを実行して、構成サーバーと次の追加コンポーネントをインストールします。
	- **プロセス サーバー**: レプリケーション ゲートウェイとして機能します。プロセス サーバーは保護されたソース マシンからレプリケーション データを受信し、そのデータをキャッシュ、圧縮、暗号化によって最適化して、Azure Storage に送信します。また、保護されたマシンへのモビリティ サービスのプッシュ インストールを処理し、VMware VM の自動検出を実行します。既定のプロセス サーバーは、構成サーバーにインストールされます。追加のスタンドアロン プロセス サーバーをデプロイすることでデプロイメントを拡張できます。
	- **マスター ターゲット サーバー**: Azure からのフェールバック中にレプリケーション データを処理します。

- **モビリティ サービス**: このコンポーネントは、Azure にレプリケートするすべてのマシン (VMware VM または物理サーバー) にデプロイされます。マシン上のデータの書き込みをキャプチャし、それをプロセス サーバーに転送します。
- **Azure**: Azure へのレプリケーションとフェールオーバーを処理するために Azure VM を作成する必要はありません。Azure サブスクリプション、レプリケートされたデータを保存する Azure Storage アカウント、フェールオーバー後に Azure VM がネットワークに接続するための Azure 仮想ネットワークが必要です。ストレージ アカウントとネットワークは、Recovery Services コンテナーと同じリージョンに存在する必要があります。
- **フェールバック**: フェールオーバー後、Azure からオンプレミス サイトにフェールバックする準備ができたら、一時的なプロセス サーバーとして Azure VM を作成する必要があります。この VM は、フェールバックが完了したら削除できます。フェールバックの場合、オンプレミス サイトと Azure VM が配置されている Azure ネットワーク間の VPN (または Azure ExpressRoute) 接続も必要になります。フェールバックのトラフィックが大量の場合、専用のマスター ターゲット サーバー マシンをオンプレミスにセットアップすることが必要になる場合もあります。トラフィックの量が少ない場合は、構成サーバーで実行されている既定のマスター ターゲット サーバーを使用できます。


次の図は、これらのコンポーネント間でのやり取りを示しています。

![アーキテクチャ](./media/site-recovery-vmware-to-azure/v2a-architecture-henry.png)

**図 1: VMware/物理から Azure**

## Azure の前提条件

このシナリオをデプロイするために Azure で必要となるものを次に示します。

**前提条件** | **詳細**
--- | ---
**Azure アカウント**| [Microsoft Azure](http://azure.microsoft.com/) のアカウントが必要です。アカウントがなくても、[無料試用版](https://azure.microsoft.com/pricing/free-trial/)を使用できます。Site Recovery の価格の詳細については、[こちら](https://azure.microsoft.com/pricing/details/site-recovery/)をご覧ください。
**Azure Storage** | レプリケートされたデータは Azure Storage に格納され、フェールオーバーが発生すると Azure VM が作成されます。<br/><br/>データを保存するには、Recovery Services コンテナーと同じリージョンにある Standard Storage アカウントまたは Premium Storage アカウントが必要です。<br/><br/>LRS または GRS ストレージ アカウントを使用できます。地域的障害が発生した場合やプライマリ リージョンが復旧できない場合にデータの復元性を確保できるように、GRS をお勧めします。詳細については、[こちら](../storage/storage-redundancy.md)をご覧ください。<br/><br/> [Premium Storage](../storage/storage-premium-storage.md) は、IO を集中的に行うワークロードをホストするために、常に高い IO パフォーマンスと低遅延時間を必要とする仮想マシンに通常使用されます。<br/><br/> Premium アカウントを使用してレプリケートされたデータを保存する場合は、オンプレミスのデータの継続的な変更をキャプチャするレプリケーション ログを保存するために、Standard Storage アカウントも必要になります。<br/><br/> Azure ポータルで作成されたストレージ アカウントは、リソース グループ間で移動できないことに注意してください。また、現在インド中部、インド南部では Premium Storage アカウントはサポートされていません。<br/><br/> Azure Storage については、[こちら](../storage/storage-introduction.md)をご覧ください。
**Azure ネットワーク** | フェールオーバーが発生した場合に Azure VM が接続する Azure 仮想ネットワークが必要です。Azure 仮想ネットワークは、Recovery Services コンテナーと同じリージョンに存在する必要があります。
**Azure からのフェールバック** | Azure VM としてセットアップされた一時的なプロセス サーバーが必要です。この VM は、フェールバックの準備ができたときに作成し、フェールバックが完了したら削除できます。<br/><br/> フェールバックするには、Azure ネットワークからオンプレミス サイトへの VPN 接続 (または Azure ExpressRoute) が必要です。

## 構成サーバーの前提条件

オンプレミスのマシンを構成サーバーとしてセットアップします。

**前提条件** | **詳細**
--- | ---
**構成サーバー**| Windows Server 2012 R2 を実行しているオンプレミスの物理マシンまたは仮想マシンが必要です。すべてのオンプレミス Site Recovery コンポーネントがこのマシンにインストールされます。<br/><br/>VMware VM のレプリケーションでは、高可用性 VMware VM としてこのサーバーをデプロイすることをお勧めします。物理マシンをレプリケートする場合は、このマシンを物理サーバーにすることができます。<br/><br/> VM と物理サーバーのどちらをフェールオーバーしたかに関係なく、Azure からオンプレミス サイトへのフェールバック先は常に VMware VM になります。構成サーバーを VMware VM としてデプロイしていない場合、フェールバックのトラフィックを受信するために、別のマスター ターゲット サーバーを VMware VM としてセットアップする必要があります。<br/><br/>サーバーが VMware VM の場合、ネットワーク アダプターの種類が VMXNET3 である必要があります。別の種類のネットワーク アダプターを使用している場合、vSphere 5.5 サーバーに [VMware アップデート](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1)をインストールする必要があります。<br/><br/>サーバーには、静的 IP アドレスが必要です。<br/><br/>サーバーはドメイン コントローラーであってはなりません。<br/><br/>サーバーのホスト名は、15 文字以内で指定します。<br/><br/>オペレーティング システムは英語版に限定されます。<br/><br/> 構成サーバーに VMware vSphere PowerCLI 6.0 をインストールする必要があります。<br/><br/>構成サーバーは、インターネットにアクセスできる必要があります。次のような発信アクセスが必要です。<br/><br/>Site Recovery コンポーネントのセットアップ中に実行される HTTP 80 での一時的なアクセス (MySQL をダウンロードするため)<br/><br/>レプリケーション管理のための HTTPS 443 での継続的な発信アクセス<br/><br/>レプリケーション トラフィックのための HTTPS 9443 での継続的な発信アクセス (このポートは変更可能)<br/><br/>サーバーには、Azure に接続できるように、*.hypervrecoverymanager.windowsazure.com*、.accesscontrol.windows.net、*.backup.windowsazure.com、.blob.core.windows.net、.store.core.windows.net* の各 URL へのアクセスも必要となります<br/><br/>サーバーで IP アドレス ベースのファイアウォール規則を使用する場合、規則で Azure への通信が許可されていることを確認します。[Azure データセンターの IP 範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)と HTTPS (443) プロトコルを許可する必要があります。<br/><br/>ご利用のサブスクリプションの Azure リージョンと米国西部の IP アドレス範囲を許可します。<br/><br/>MySQL をダウンロードするために、http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi を許可します


## VMware vCenter/vSphere ホストの前提条件

**前提条件** | **詳細**
--- | ---
**vSphere**| 1 つ以上の VMware vSphere ハイパーバイザーが必要です。<br/><br/>ハイパーバイザーでは、最新の更新プログラムが適用された vSphere バージョン 6.0、5.5、または 5.1 が実行されている必要があります。<br/><br/>vSphere ホストと vCenter サーバーをプロセス サーバーと同じネットワークに配置することをお勧めします (専用のプロセス サーバーをセットアップした場合を除き、これは構成サーバーが配置されているネットワークです)。
**vCenter** | vSphere ホストを管理する VMware vCenter サーバーをデプロイすることをお勧めします。最新の更新プログラムが適用された vCenter バージョン 6.0 または 5.5 を実行している必要があります。<br/><br/>Site Recovery は、クロス vCenter vMotion、仮想ボリューム、ストレージ DRS などの vCenter および vSphere 6.0 の新しい機能をサポートしていません。Site Recovery のサポートは、バージョン 5.5 で提供されていた機能に限定されます。


## 保護対象のマシンの前提条件

**前提条件** | **詳細**
--- | ---
**オンプレミスの VMware VM** | 保護対象の VMware VM では、VMware ツールがインストールされ、実行されている必要があります。<br/><br/> 保護対象のマシンは、Azure VM を作成するための [Azure の前提条件](site-recovery-best-practices.md#azure-virtual-machine-requirements)に準拠している必要があります。<br/><br/>保護対象のマシン上の個々のディスク容量が 1023 GB 以下である必要があります。VM は最大 64 個のディスク (従って最大 64 TB) に対応できます。<br/><br/>暗号化されたディスク (ルートおよびデータ ディスク) を持つ VM の保護はサポートされていません。

共有ディスク ゲスト クラスターはサポートされていません。<br/><br/>**アプリケーションの整合性**を有効にする場合は、**ポート 20004** は、保護対象の VM 上で開いた状態にする必要があります。

Unified Extensible Firmware Interface(UEFI)/Extensible Firmware Interface(EFI) ブートはサポートされていません。<br/><br/>マシン名は 1 ～ 63 文字 (英字、数字、ハイフン) にする必要があります。文字または数字で始まり、文字または数字で終わる必要があります。マシンのレプリケーションを有効にした後で、Azure 上の名前を変更できます。<br/><br/>ソース VM に NIC チーミングがある場合、Azure へのフェールオーバー後に単一の NIC に変換されます。<br/><br/>保護された VM に iSCSI ディスクがある場合、VM が Azure にフェールオーバーしたときに、Site Recovery は保護された VM の iSCSI ディスクを VHD ファイルに変換します。Azure VM から iSCSI ターゲットに到達できる場合、iSCSI ターゲットに接続すると、基本的に Azure VM 上の VHD ディスクとソース iSCSI ディスクの 2 つのディスクがあります。この場合、Azure VM 上の iSCSI ターゲットを切断する必要があります。**Windows マシン (物理または VMware)** | マシンでは、サポートされている 64 ビット オペレーティング システム (Windows Server 2012 R2、Windows Server 2012、または Windows Server 2008 R2 SP1 以降) が実行されている必要があります。<br/><br/> オペレーティング システムは C:\\ ドライブにインストールする必要があります。OS ディスクは、ダイナミック ディスクではなく、Windows ベーシック ディスクである必要があります。データ ディスクはダイナミックでもかまいません。<br/><br/>Site Recovery は、RDM ディスクを使用する VM をサポートします。フェールバック時に、元のソース VM と RDM ディスクを使用できる場合、Site Recovery は RDM ディスクを再利用します。これらが使用できない場合は、フェールバック時に各ディスクの新しい VMDK ファイルが作成されます。**Linux マシン** | サポートされている 64 ビット オペレーティング システム (Red Hat Enterprise Linux 6.7、7.1、7.2、Centos 6.5、6.6、6.7、7.0、7.1、7.2、Red Hat 互換カーネルまたは Unbreakable Enterprise Kernel リリース 3 (UEK3) を実行している Oracle Enterprise Linux 6.4、6.5、SUSE Linux Enterprise Server 11 SP3) が必要です。<br/><br/>保護対象のマシン上の /etc/hosts ファイルには、ローカル ホスト名をすべてのネットワーク アダプターに関連付けられた IP アドレスにマップするエントリが含まれている必要があります。<br/><br/>フェールオーバー後に、Linux を実行する Azure 仮想マシンに Secure Shell クライアント (ssh) を使用して接続する場合は、保護対象のマシン上の Secure Shell サービスがシステム ブート時に自動的に起動するように設定されていることと、ファイアウォール規則で仮想マシンへの ssh 接続が許可されていることを確認します。<br/><br/>ホスト名、マウント ポイント、デバイス名、Linux システム パスとファイル名 (例: /etc/、/usr) には英語のみを使用できます。<br/><br/>ファイル システム (EXT3、ETX4、ReiserFS、XFS)、マルチパス ソフトウェア デバイス マッパー (multipath)、ボリューム マネージャー (LVM2) の各ストレージを使用する Linux マシンでのみ保護を有効にできます。HP CCISS コントローラー ストレージを使用する物理サーバーはサポートされていません。ReiserFS ファイルシステムは、SUSE Linux Enterprise Server 11 SP3 でのみサポートされています。<br/><br/>Site Recovery は RDM ディスクが搭載された VM をサポートします。Linux のフェールバックの場合、Site Recovery は RDM ディスクを再利用しません。代わりに、対応する各 RDM ディスクの新しい VMDK ファイルが作成されます。<br/><br/>VMware の VM の構成パラメーターで disk.enableUUID=true が設定されていることを確認します。このエントリが存在しない場合は作成します。これは、VMDK が適切にマウントされるように、一貫性のある UUID を提供するために必要となります。また、この設定を追加すると、フェールバック時に、完全なレプリケーションではなく、差分変更だけをオンプレミスに転送できます。**モビリティ サービス** | **Windows**: Windows を実行している VM にモビリティ サービスを自動的にプッシュするには、プロセス サーバーがプッシュ インストールを実行できるように、管理者アカウント (Windows マシンのローカル管理者) を指定する必要があります。<br/><br/> **Linux**: Linux を実行している VM にモビリティ サービスを自動的にプッシュするには、プロセス サーバーがプッシュ インストールを実行する際に使用できるアカウントを作成する必要があります。<br/><br/> 既定では、マシンのすべてのディスクがレプリケートされます。[レプリケーションからディスクを除外](#exclude-disks-from-replication)するには、レプリケーションを有効にする前に、モビリティ サービスをマシンに手動でインストールする必要があります。

## デプロイの準備をする

デプロイを準備するには、次の手順に従います。

1. フェールオーバー後にスピンアップされた Azure VM が配置される [Azure ネットワークをセットアップ](#set-up-an-azure-network)します。また、フェールバック用に、Azure ネットワークからオンプレミス サイトへの VPN 接続 (または Azure ExpressRoute) をセットアップする必要があります。
2. レプリケートされたデータ用に、[Azure Storage アカウントを設定](#set-up-an-azure-storage-account)します。
3. Site Recovery が追加された VMware VM を自動的に検出できるように、vCenter サーバーまたは vSphere ホストで[アカウントを準備](#prepare-an-account-for-automatic-discovery)します。
4. 必要な URL にアクセスし、vSphere PowerCLI 6.0 をインストールできるように、[構成サーバーを準備](#prepare-the-configuration-server)します。


### Azure ネットワークをセットアップする

- このネットワークは、Recovery Services コンテナーをデプロイする Azure リージョンと同じリージョンに存在する必要があります。
- フェールオーバーされた Azure VM に使用するリソース モデルに応じて、Azure ネットワークを [ARM モード](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)または[クラシック モード](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)でセットアップします。
- Azure からオンプレミスの VMware サイトにフェールバックするには、レプリケートされた Azure VM が配置されている Azure ネットワークから、構成サーバーが配置されているオンプレミス ネットワークへの VPN 接続 (または Azure ExpressRoute 接続) が必要です。
- VPN サイト間接続のサポートされるデプロイメント モデルと、[接続の設定](../vpn-gateway/vpn-gateway-site-to-site-create.md#create-your-virtual-network)方法については、[こちら](../vpn-gateway/vpn-gateway-site-to-site-create.md)をご覧ください。
- また、[Azure ExpressRoute](../expressroute/expressroute-introduction.md) をセットアップすることもできます。ExpressRoute を使用した Azure ネットワークのセットアップの詳細については、[こちら](../expressroute/expressroute-howto-vnet-portal-classic.md)をご覧ください。

> [AZURE.NOTE] [Migration of networks]Site Recovery のデプロイ用のネットワークでは、同じサブスクリプション内のリソース グループ間またはサブスクリプション間でのネットワークの移行はサポートされていません。

### Azure ストレージ アカウントをセットアップする

- Azure にレプリケートされたデータを保持するために、Standard または Premium Azure Storage アカウントが必要になります。アカウントは、Recovery Services コンテナーと同じリージョンにある必要があります。フェールオーバーされた Azure VM に使用するリソース モデルに応じて、アカウントを [ARM モード](../storage/storage-create-storage-account.md)または[クラシック モード](../storage/storage-create-storage-account-classic-portal.md)でセットアップします。
- レプリケートされたデータに Premium アカウントを使用する場合は、オンプレミスのデータの継続的な変更をキャプチャするレプリケーション ログを保存するために、追加の Standard アカウントを作成する必要があります。

> [AZURE.NOTE] [Migration of storage accounts]Site Recovery のデプロイ用のストレージ アカウントでは、同じサブスクリプション内のリソース グループ間またはサブスクリプション間でのストレージ アカウントの移行はサポートされていません。

### 自動検出用のアカウントを準備する

Site Recovery のプロセス サーバーは、vSphere ホストまたはホストを管理する vCenter サーバー上の VMware VM を自動的に検出できます。Site Recovery で自動検出を実行するには、VMware サーバーにアクセスできる資格情報が必要です。物理マシンだけをレプリケートする場合、これは関係ありません。

1. 自動検出に専用のアカウントを使用するには、[必要なアクセス許可](#vmware-account-permissions)を適用して、vCenter レベルでロール (例: Azure\_Site\_Recovery) を作成します。
2. VSphere ホストまたは vCenter サーバーに新しいユーザーを作成し、そのユーザーにロールを割り当てます。自動検出を実行できるように、後で Site Recovery にこれらの資格情報を伝えます。

	>[AZURE.NOTE] 読み取り専用ロールが割り当てられた vCenter ユーザー アカウントはフェールオーバーを実行できますが、保護されたソース マシンをシャットダウンすることはできません。保護されたソース マシンをシャットダウンする場合は、[Azure\_Site\_Recovery](#vmware-account-permissions) ロールが必要です。VM を VMware から Azure に移行するだけであり、フェールバックする必要はない場合には、読み取り専用ロールで十分です。

### 構成サーバーを準備する

1.	構成サーバーに使用するマシンが[前提条件](#configuration-server-prerequisites)に準拠していることを確認します。具体的には、マシンが次の設定でインターネットに接続されていることを確認します。

	- *.hypervrecoverymanager.windowsazure.com*、.accesscontrol.windows.net、*.backup.windowsazure.com、.blob.core.windows.net、.store.core.windows.net* の各 URL へのアクセスを許可。
	- MySQL をダウンロードするために [http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi](http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi) へのアクセスを許可。
	- [Azure データセンターの IP 範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)と HTTPS (443) プロトコルを使用した Azure へのファイアウォール通信を許可。

2.	構成サーバーに [VMware vSphere PowerCLI 6.0](https://developercenter.vmware.com/tool/vsphere_powercli/6.0) をダウンロードしてインストールします。(現在、バージョン 6.0 の R リリースを含む PowerCLI の他のバージョンはサポートされていません。)


## Recovery Services コンテナーを作成する

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. **[新規]**、**[管理]**、**[Backup and Site Recovery (OMS) (バックアップと Site Recovery (OMS))]** の順にクリックします。または、**[参照]**、**[Recovery Services コンテナー]**、**[追加]** の順にクリックします。

	![新しいコンテナー](./media/site-recovery-vmware-to-azure/new-vault3.png)

3. **[名前]** に、コンテナーを識別するフレンドリ名を入力します。複数のサブスクリプションがある場合は、いずれかを選択します。
4. [新しいリソース グループを作成](../resource-group-template-deploy-portal.md)するか、既存のリソース グループを選択します。Azure リージョンを指定します。マシンは、このリージョンにレプリケートされます。Site Recovery に使用される Azure Storage とネットワークは、同じリージョンに存在する必要があります。サポートされているリージョンを確認するには、「[Azure Site Recovery Pricing Details (Azure Site Recovery の価格の詳細)](https://azure.microsoft.com/pricing/details/site-recovery/)」で利用可能地域を参照してください。
4. ダッシュボードからコンテナーにすばやくアクセスするには、**[ダッシュボードにピン留めする]** をクリックし、**[作成]** をクリックします。

	![新しいコンテナー](./media/site-recovery-vmware-to-azure/new-vault-settings.png)

新しいコンテナーは、**[ダッシュボード]** の **[すべてのリソース]** と、メインの **[Recovery Services コンテナー]** ブレードに表示されます。

## 使用の開始

Site Recovery には、できるだけ速やかにデプロイして運用できるようにすることを目的とした [作業の開始] エクスペリエンスが用意されています。[作業の開始] では、前提条件がチェックされ、Site Recovery をデプロイするために必要な手順が示されます。

レプリケートするマシンの種類とレプリケート先を選択します。オンプレミス サーバー、Azure の設定、レプリケーション ポリシー、キャパシティ プランニングなど、インフラストラクチャをセットアップします。インフラストラクチャの準備ができたら、VM と物理サーバーのレプリケーションを有効にします。特定のマシンのフェールオーバーを実行することも、複数のマシンをフェールオーバーする復旧計画を作成することもできます。

[作業の開始] では、まず、Site Recovery をデプロイする方法を選択します。作業の開始フローは、レプリケーションの要件によって多少変化します。


## ステップ 1: 保護の目標を選択する

レプリケートの対象とレプリケート先を選択します。

1. **[Recovery Services コンテナー]** ブレードでコンテナーを選択し、**[設定]** をクリックします。
2. **[設定]** の **[作業の開始]** で、**[Site Recovery]**、**[手順 1: インフラストラクチャを準備する]**、**[保護の目標]** の順にクリックします。

	![Choose goals](./media/site-recovery-vmware-to-azure/choose-goals.png)

3. **[保護の目標]** で、**[To Azure (Azure へ)]** を選択し、**[Yes, with VMware vSphere Hypervisor (はい、VMware vSphere ハイパーバイザーを使用する)]** を選択します。次に、 **[OK]** をクリックします

	![Choose goals](./media/site-recovery-vmware-to-azure/choose-goals2.png)


## ステップ 2: ソース環境をセットアップする

構成サーバーをセットアップし、Recovery Services コンテナーに登録します。VMware VM をレプリケートする場合は、自動検出に使用する VMware アカウントを指定します。

1. **[手順 1: インフラストラクチャを準備する]**、**[ソース]** の順にクリックします。**[ソースの準備]** で、構成サーバーがない場合は、**[+ 構成サーバー]** をクリックして追加します。

	![Set up source](./media/site-recovery-vmware-to-azure/set-source1.png)

2. **[サーバーの追加]** ブレードで、**[サーバーの種類]** に **[構成サーバー]** が表示されていることを確認します。
3. 構成サーバーをセットアップする前に、[前提条件](#configuration-server-prerequisites)を確認します。具体的には、マシンが必要な URL にアクセスできることを確認します。
4.	Site Recovery 統合セットアップ インストール ファイルをダウンロードします。
5.	コンテナー登録キーをダウンロードします。このキーは、統合セットアップを実行するときに必要になります。キーは生成後 5 日間有効です。

	![Set up source](./media/site-recovery-vmware-to-azure/set-source2.png)

6.	構成サーバーとして使用するマシンで統合セットアップを実行して、構成サーバー、プロセス サーバー、マスター ターゲット サーバーをインストールします。


### Site Recovery 統合セットアップを実行する

1.	統合セットアップ インストール ファイルを実行します。
2.	**[開始する前に]** の **[構成サーバーとプロセス サーバーをインストールする]** を選択します。

	![開始する前に](./media/site-recovery-vmware-to-azure/combined-wiz1.png)

3. **[Third-Party Software License (サードパーティ製ソフトウェア ライセンス)]** で、**[同意する]** をクリックして MySQL をダウンロードし、インストールします。

	![サード パーティ製ソフトウェア](./media/site-recovery-vmware-to-azure/combined-wiz105.PNG)

4. **[登録]** で、コンテナーからダウンロードした登録キーを参照して選択します。

	![登録](./media/site-recovery-vmware-to-azure/combined-wiz3.png)

5. **[インターネット設定]** で、構成サーバーで実行されているプロバイダーがインターネット経由で Azure Site Recovery に接続する方法を指定します。

	- マシンで現在セットアップされているプロキシを使用して接続する場合は、**[Connect with existing proxy settings (既存のプロキシ設定を使用して接続する)]** を選択します。
	- プロバイダーから直接接続するようにするには、**[Connect directly without a proxy (プロキシなしで直接接続する)]** を選択します。
	- 既存のプロキシで認証が必要な場合、またはプロバイダー接続にカスタム プロキシを使用する場合は、**[Connect with custom proxy settings (カスタム プロキシ設定を使用して接続する)]** を選択します。
		- カスタム プロキシを使用する場合、アドレス、ポート、資格情報を指定する必要があります。
		- プロキシを使用している場合は、[前提条件](#configuration-server-prerequisites)に記載されている URL をあらかじめ許可しておく必要があります。

	![ファイアウォール](./media/site-recovery-vmware-to-azure/combined-wiz4.png)

6. **[前提条件の確認]** では、インストールを実行できることを確認するためのチェックが実行されます。**グローバル時刻の同期チェック**に関する警告が表示された場合は、システム クロックの時刻 (**[日付と時刻]** 設定) がタイム ゾーンと同じであることを確認します。

	![前提条件](./media/site-recovery-vmware-to-azure/combined-wiz5.png)

7. **[MySQL Configuration (MySQL の構成)]** で、インストールする MySQL サーバー インスタンスにログオンするための資格情報を作成します。

	![MySQL](./media/site-recovery-vmware-to-azure/combined-wiz6.png)

8. **[環境の詳細]** で、VMware VM をレプリケートするかどうかを選択します。レプリケートする場合、PowerCLI 6.0 がインストールされているかどうかがチェックされます。

	![MySQL](./media/site-recovery-vmware-to-azure/combined-wiz7.png)

9. **[インストール場所]** で、バイナリをインストールし、キャッシュを格納する場所を選択します。使用可能な記憶域が 5 GB 以上あるドライブを選択できますが、600 GB 以上の空き領域があるキャッシュ ドライブを使用することをお勧めします。

	![インストール場所](./media/site-recovery-vmware-to-azure/combined-wiz8.png)

10. **[ネットワークの選択]** で、構成サーバーがレプリケーション データを送受信するリスナー (ネットワーク アダプターと SSL ポート) を指定します。既定のポート (9443) は変更できます。このポートに加え、ポート 443 がレプリケーション操作を統制する Web サーバーによって使用されます。レプリケーション トラフィックの受信には 443 を使用しないでください。


	![ネットワークの選択](./media/site-recovery-vmware-to-azure/combined-wiz9.png)



11.  **[概要]** で情報を確認し、**[インストール]** をクリックします。インストールが完了すると、パスフレーズが生成されます。このパスフレーズは、レプリケーションを有効にするときに必要になるので、コピーしてセキュリティで保護された場所に保管してください。

	![概要](./media/site-recovery-vmware-to-azure/combined-wiz10.png)

12.  登録が完了すると、コンテナーの **[設定]** の **[サーバー]** ブレードに、サーバーが表示されます。



#### コマンド ラインからセットアップを実行する

コマンドラインから構成サーバーをセットアップできます。

    UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

インストールが終了したら、次の手順に従って登録を実行します。

1. Windows の [スタート] メニューから "Microsoft Azure Recovery Services シェル" という名前のアプリケーションを起動します。
2. 表示されたコマンド ウィンドウで、次の一連のコマンドを実行して、プロキシ サーバー設定を構成します。

		PS C:\Windows\System32> $pwd = ConvertTo-SecureString -String ProxyUserPassword
		PS C:\Windows\System32> Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumb – ProxyUserName domain\username -ProxyPassword $pwd
		PS C:\Windows\System32> net stop obengine.exe

パラメーター:

- /ServerMode: 必須。構成サーバーとプロセス サーバーの両方をインストールするか、プロセス サーバーだけをインストールするかを指定します。入力値: CS、PS。
- InstallLocation: 必須。コンポーネントがインストールされているフォルダー。
- /MySQLCredsFilePath: 必須。MySQL サーバーの資格情報が保存されているファイルのパス。このファイルの形式は次のとおりです。
	- [MySQLCredentials]
	- MySQLRootPassword = "<パスワード>"
	- MySQLUserPassword = "<パスワード>"
- /VaultCredsFilePath: 必須。コンテナーの資格情報ファイルの場所。
- /EnvType:必須。インストールの種類。値: VMware、NonVMware
- /PSIP と /CSIP:必須。プロセス サーバーと構成サーバーの IP アドレス。
- /PassphraseFilePath:必須。パスフレーズ ファイルの場所。
- /BypassProxy: 省略可能。構成サーバーがプロキシを介さずに Azure に接続することを指定します。
- /ProxySettingsFilePath:省略可能。プロキシ設定 (認証を必要とする既定のプロキシ、またはカスタム プロキシ)。このファイルの形式は次のとおりです。
	- [ProxySettings]
	- ProxyAuthentication = "Yes/No"
	- Proxy IP = "IP Address>"
	- ProxyPort = "<ポート>"
	- ProxyUserName="<ユーザー名>"
	- ProxyPassword="<パスワード>"
- DataTransferSecurePort: 省略可能。レプリケーション データに使用するポート番号。
- SkipSpaceCheck: 省略可能。キャッシュの領域チェックをスキップします。
- AcceptThirdpartyEULA: 必須。サード パーティのライセンス条項への同意を意味するフラグ。
- ShowThirdpartyEULA: 必須。サード パーティのライセンス条項を表示します。入力として提供された場合、他のすべてのパラメーターが無視されます。

### 自動検出に使用する VMware アカウントを追加する

 デプロイの準備をしたときに、Site Recovery が自動検出に使用できる [VMware アカウントを作成](#prepare-an-account-for-automatic-discovery)しました。次の手順に従って、このアカウントを追加します。

1. **CSPSConfigtool.exe** を開きます。cspsconfigtool はデスクトップにショートカットがあり、[インストール場所]\\home\\svsystems\\bin フォルダーに保存されています。
2. **[アカウントの管理]**、**[アカウントの追加]** の順にクリックします。

	![Add account](./media/site-recovery-vmware-to-azure/credentials1.png)

3. **[アカウントの詳細]** で、自動検出に使用するアカウントを追加します。アカウント名がポータルに表示されるまでに 15 分以上かかることがあります。すぐに更新するには、**[構成サーバー]**、サーバー名、**[サーバーを最新の情報に更新する]** の順にクリックします。

	![詳細](./media/site-recovery-vmware-to-azure/credentials2.png)

### vSphere ホストと vCenter サーバーに接続します。

VMware VM をレプリケートする場合は、vSphere ホストと vCenter サーバーに接続します。

1. 構成サーバーが vSphere ホストと vCenter サーバーにネットワーク アクセスできることを確認します。
2. **[インフラストラクチャの準備]**、**[ソース]** の順にクリックします。**[ソースの準備]** で構成サーバーを選択し、**[+ vCenter]** をクリックして vSphere ホストまたは vCenter サーバーを追加します。
3. **[vCenter の追加]** で、vSphere ホストまたは vCenter サーバーのフレンドリ名を指定し、サーバーの IP アドレスまたは FQDN を指定します。別のポートで要求をリッスンするように VMware サーバーが構成されている場合を除き、ポートは 443 のままにしておきます。VMware サーバーへの接続に使用するアカウントを選択します。**[OK]** をクリックします。

	![VMware](./media/site-recovery-vmware-to-azure/vmware-server.png)

	>[AZURE.NOTE] vCenter サーバーまたはホスト サーバーに対する管理者特権のないアカウントを使用して vCenter サーバーまたは vSphere ホストを追加する場合、そのアカウントで、Datacenter、Datastore、Folder、Host、Network、Resource、Virtual Machine、vSphere Distributed Switch の各特権が有効になっていることを確認します。さらに、vCenter サーバーは Storage ビューの特権が必要です。

Site Recovery は指定した設定を使用して VMware サーバーに接続し、VM を検出します。

## ステップ 3: ターゲット環境をセットアップする

レプリケーションに使用するストレージ アカウントと、フェールオーバー後に Azure VM が接続する Azure ネットワークがあることを確認します。

1.	**[インフラストラクチャの準備]**、**[ターゲット]** の順にクリックし、使用する Azure サブスクリプションを選択します。
2.	フェールオーバー後に VM に使用するデプロイメント モデルを指定します。
3.	Site Recovery によって、互換性のある Azure ストレージ アカウントとネットワークが 1 つ以上あるかどうかが確認されます。

	![ターゲット](./media/site-recovery-vmware-to-azure/gs-target.png)

4.	まだストレージ アカウントを作成しておらず、ARM を使用して作成する場合は、**[+ ストレージ アカウント]** をクリックしてインラインで作成します。**[ストレージ アカウントの作成]** ブレードで、アカウント名、種類、サブスクリプション、場所を指定します。アカウントは、Recovery Services コンテナーと同じリージョンに存在する必要があります。

	![Storage](./media/site-recovery-vmware-to-azure/gs-createstorage.png)

	以下の点に注意してください。

	- クラシック モデルを使用してストレージ アカウントを作成する場合は、Azure ポータルで作成できます。[詳細情報](../storage/storage-create-storage-account-classic-portal.md)
	- レプリケートされたデータに Premium Storage アカウントを使用している場合は、オンプレミスのデータの継続的な変更をキャプチャするレプリケーション ログを格納するために、追加の Standard ストレージ アカウントをセットアップする必要があります。
	
	> [AZURE.NOTE] 現在インド中部、インド南部では Premium Storage アカウントはサポートされていません。

4.	Azure ネットワークを選択します。まだネットワークを作成しておらず、ARM を使用して作成する場合は、**[+ ネットワーク]** をクリックしてインラインで作成します。**[仮想ネットワークの作成]** ブレードで、ネットワーク名、アドレス範囲、サブネットの詳細、サブスクリプション、場所を指定します。ネットワークは、Recovery Services コンテナーと同じ場所にある必要があります。

	![ネットワーク](./media/site-recovery-vmware-to-azure/gs-createnetwork.png)

	クラシック モデルを使用してネットワークを作成する場合は、Azure ポータルで作成できます。[詳細情報](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)。

## ステップ 4: レプリケーション設定をセットアップする

1. 新しいレプリケーション ポリシーを作成するには、**[インフラストラクチャの準備]**、**[レプリケーションの設定]**、**[+ 作成と関連付け]** の順にクリックします。
2. **[ポリシーの作成と関連付け]** で、ポリシー名を指定します。
3. **[RPO しきい値]** で、RPO の制限を指定します。継続的なレプリケーションがこの制限を超えると、アラートが生成されます。
5. **[復旧ポイントの保持期間]** で、各復旧ポイントのリテンション期間の長さを時間単位で指定します。保護されたマシンはこの期間内のどのポイントにも復旧できます。Premium Storage にレプリケートされたマシンでは、最大 24 時間のリテンション期間がサポートされます。
6. **[アプリ整合性スナップショットの頻度]** で、アプリケーション整合性スナップショットを含む復旧ポイントの作成頻度 (分単位) を指定します。
7. レプリケーション ポリシーを作成したときに、既定でフェールバックの照合ポリシーが自動的に作成されます。たとえば、レプリケーション ポリシーが **rep-policy** の場合、フェールバック ポリシーは **rep-policy-failback** になります。このポリシーは、フェールバックを開始するまで使用されません。
8. **[OK]** をクリックしてポリシーを作成します。

	![Replication policy](./media/site-recovery-vmware-to-azure/gs-replication2.png)

9. 新しいポリシーを作成すると、自動的に構成サーバーに関連付けられます。**[OK]** をクリックします。

	![Replication policy](./media/site-recovery-vmware-to-azure/gs-replication3.png)


## ステップ 5: キャパシティ プランニング

基本的なインフラストラクチャをセットアップできたので、キャパシティ プランニングを立案し、追加のリソースが必要かどうかを検討できます。

Site Recovery が備えている Capacity Planner を使用して、ソース環境、Site Recovery のコンポーネント、ネットワーク、およびストレージに適切なリソースを割り当てることができます。このキャパシティ プランニング ツールは、VM、ディスク、およびストレージの平均数に基づく見積もりを使用するクイック モードか、ワークロード レベルで数値を入力する詳細モードで実行できます。開始する前に、次のことを行う必要があります。

- VM、VM あたりのディスク数、ディスクあたりのストレージなど、レプリケーション環境の情報を収集する。
- レプリケートされたデータの 1 日の変更 (チャーン) 率を見積もる。この見積もりには、[vSphere キャパシティ プランニング アプライアンス](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance)が役立ちます。

1.	**[ダウンロード]** をクリックしてツールをダウンロードし、実行する。ツールに付随する[こちらの記事をご覧ください](site-recovery-capacity-planner.md)。
2.	作業が完了したら、**[容量計画は完了していますか?]** で **[はい]** を選択する。

	![容量計画](./media/site-recovery-vmware-to-azure/gs-capacity-planning.png)

このシナリオのキャパシティ プランニングに役立つ項目を次の表に示します。


**コンポーネント** | **詳細**
--- | --- | ---
**レプリケーション** | **日次変更率の上限** - 保護されたマシンが使用できるプロセス サーバーは 1 つだけです。また、1 つのプロセス サーバーが対応できる日次変更率は最大 2 TB (テラバイト) です。そのため、保護されたマシンでサポートされる 1 日のデータ変化率の上限は 2 TB です。<br/><br/> **最大スループット** - レプリケートされたマシンは、Azure の 1 つのストレージ アカウントに属することができます。Standard ストレージ アカウントは、1 秒間に最大 20,000 要求を処理できます。ソース マシン全体の IOPS 数を 20,000 以下に抑えることをお勧めします。たとえば、5 個のディスクが搭載されたソース マシンで、各ディスクから 120 IOPS (8K サイズ) が生成される場合、Azure 内のディスクごとの IOPS 制限は 500 になります。必要なストレージ アカウントの数は、合計ソース IOPS/20000 です。
**構成サーバー** | 構成サーバーは、保護されたマシンで実行されているすべてのワークロードの日次変更率容量を処理できる必要があります。また、Azure Storage にデータを継続的にレプリケートできる十分な帯域幅が必要です。<br/><br/> ベスト プラクティスとして、保護対象のマシンと同じネットワークおよび LAN セグメントに構成サーバーを配置することをお勧めします。管理サーバーを別のネットワークに配置することもできますが、保護対象のマシンは管理サーバーに対して、L3 のネットワーク可視性が必要です。<br/><br/> 構成サーバーのサイズの推奨事項を次の表に示します。
**プロセス サーバー** | 最初のプロセス サーバーは、構成サーバーに既定でインストールされます。追加のプロセス サーバーをデプロイして環境を拡張できます。次の点に注意してください。<br/><br/>プロセス サーバーは、保護されたマシンからレプリケーション データを受信し、Azure に送信する前にキャッシュ、圧縮、暗号化によってデータを最適化します。プロセス サーバー マシンには、これらのタスクを実行できる十分なリソースが必要です。<br/><br/> プロセス サーバーは、ディスク ベースのキャッシュを使用します。ネットワークのボトルネックや障害が発生した場合に、格納されているデータの変更を処理できるように、600 GB 以上のキャッシュ ディスクを別に用意することをお勧めします。

### 構成サーバーのサイズの推奨事項

**CPU** | **メモリ** | **キャッシュ ディスク サイズ** | **データの変更率** | **保護されたマシン**
--- | --- | --- | --- | ---
8 vCPU (2 ソケット * 4 コア @ 2.5 GHz) | 16 GB | 300 GB | 500 GB 以下 | 100 台未満のマシンをレプリケートします。
12 vCPU (2 ソケット * 6 コア @ 2.5 GHz) | 18 GB | 600 GB | 500 GB ～ 1 TB | 100 ～ 150 台のマシンをレプリケートします。
16 vCPU (2 ソケット * 8 コア @ 2.5GHz) | 32 GB | 1 TB (テラバイト) | 1 TB ～ 2 TB | 150 ～ 200 台のマシンをレプリケートします。
別のプロセス サーバーをデプロイします | | | > 2 TB | 200 台を超えるマシンをレプリケートする場合、または 1 日のデータ変化率が 2 TB を超える場合、追加のプロセス サーバーをデプロイします。

各値の説明:

- 各ソース マシンは、それぞれ 100 GB の 3 個のディスクで構成されています。
- キャッシュ ディスクの測定には、RAID 10 で 10,000 RPM の SAS ドライブが 8 個というベンチマーク用ストレージを使用しました。

### プロセス サーバーのサイズの推奨事項

200 台を超えるマシンを保護する必要がある場合や、日次変更率が 2 TB を超える場合は、レプリケーションの負荷を処理するためにプロセス サーバーを追加できます。スケールアウトするには、次の方法があります。

- 構成サーバーの数を増やします。たとえば、2 つの構成サーバーで最大 400 台のマシンを保護できます。
- プロセス サーバーを追加し、構成サーバーの代わりに (または構成サーバーに加えて) トラフィックの処理に使用します。

以下の表は、次のようなシナリオの場合を示します。

- 構成サーバーをプロセス サーバーとして使用しない場合。
- 追加のプロセス サーバーをセットアップした場合。
- 追加のプロセス サーバーを使用するように保護された仮想マシンを構成した場合。
- 保護された各ソース マシンには、それぞれ 100 GB の 3 つのディスクが構成されています。

**構成サーバー** | **追加のプロセス サーバー**| **キャッシュ ディスク サイズ** | **データの変更率** | **保護されたマシン**
--- | --- | --- | --- | ---
8 vCPU (2 ソケット * 4 コア @ 2.5GHz)、16 GB メモリ | 4 vCPU (2 ソケット * 2 コア @ 2.5GHz)、8 GB メモリ | 300 GB | 250 GB 以下 | 85 台以下のマシンをレプリケートします。
8 vCPU (2 ソケット * 4 コア @ 2.5GHz)、16 GB メモリ | 8 vCPU (2 ソケット * 4 コア @ 2.5GHz)、12 GB メモリ | 600 GB | 250 GB ～ 1 TB | 85 ～ 150 台のマシンをレプリケートします。
12 vCPU (2 ソケット * 6 コア @ 2.5GHz)、18 GB メモリ | 12 vCPU (2 ソケット * 6 コア @ 2.5GHz)、24 GB メモリ | 1 TB (テラバイト) | 1 TB ～ 2 TB | 150 ～ 225 台のマシンをレプリケートします。


サーバーの規模の決め方は、スケールアップ モデルまたはスケールアウト モデルの選択によって変わります。少数のハイエンドの構成サーバーとプロセス サーバーをデプロイしてスケールアップするか、リソースが少ないサーバーを追加してスケールアウトします。たとえば、220 台のマシンを保護する必要がある場合、次のいずれかを実行できます。

- 12 vCPU、18 GB のメモリが搭載された構成サーバーと、12 vCPU、24 GB のメモリが搭載された追加のプロセス サーバーをセットアップし、追加のプロセス サーバーだけを使用するように保護されたマシンを構成します。
- また、2 つの構成サーバー (2 x 8 vCPU、16 GB RAM) と、135 + 85 (220) 台のマシンを処理する 2 つの追加のプロセス サーバー (1 x 8 vCPU + 1 x 4vCPU) を構成し、追加のプロセス サーバーだけを使用するように保護されたマシンを構成することもできます。

[この手順に従って](#deploy-additional-process-servers)、追加のプロセス サーバーをセットアップします。

### ネットワーク帯域幅に関する考慮事項

Capacity Planner を使用して、レプリケーション (初期レプリケーションとその後の差分レプリケーション) に必要な帯域幅を計算できます。レプリケーションの帯域幅の使用量を制御する方法には、いくつかのオプションがあります。

- **帯域幅を調整する**: Azure にレプリケートされる VMware トラフィックは、特定のプロセス サーバーを経由します。プロセス サーバーとして実行されているマシンの帯域幅を調整できます。
- **帯域幅に影響を与える**: レジストリ キーをいくつか使用して、レプリケーションに使用される帯域幅に影響を与えることができます。
	- **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows Azure Backup\\UploadThreadsPerVM** レジストリ値は、ディスクのデータ転送 (初期レプリケーションまたは差分レプリケーション) に使用されるスレッドの数を指定します。値を大きくすると、レプリケーションに使用されるネットワーク帯域幅が増加します。
	- **Hkey\_local\_machine \\software\\microsoft\\windows Azure Backup\\DownloadThreadsPerVM** は、フェールバック時にデータ転送に使用されるスレッドの数を指定します。

#### スロットルの帯域幅

1. プロセス サーバーとして機能しているマシンで、Microsoft Azure Backup MMC スナップインを開きます。既定では、Microsoft Azure Backup のショートカットが、デスクトップか C:\\Program Files\\Microsoft Azure Recovery Services Agent\\bin\\wabadmin にあります。
2. スナップインで **[プロパティの変更]** をクリックします。

	![スロットルの帯域幅](./media/site-recovery-vmware-to-azure/throttle1.png)

3. **[調整]** タブで、**[バックアップ操作用のインターネット使用帯域幅の調整を有効にする]** チェック ボックスをオンにし、勤務時間用と勤務時間外用の制限値を設定します。有効な範囲は、1 秒あたり 512 Kbps ～ 102 Mbps です。

	![スロットルの帯域幅](./media/site-recovery-vmware-to-azure/throttle2.png)

[Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) コマンドレットを使用して、スロットルを設定することもできます。サンプルを次に示します。

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** は、スロットルが不要であることを示します。


#### ネットワーク帯域幅に影響を与える

1. レジストリで、**HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows Azure Backup\\Replication** に移動します。
	- レプリケートするディスクで帯域幅のトラフィックに影響を与えるには、**UploadThreadsPerVM** 値を変更するか、このキーが存在しない場合は作成します。
	- Azure からのフェールバックのトラフィックの帯域幅に影響を与えるには、**DownloadThreadsPerVM** 値を変更します。
2. 既定値は 4 ですが、"プロビジョニング超過" 状態のネットワークの場合、このレジストリ キーを既定値から変更する必要があります。最大値は 32 です。トラフィックを監視して値を最適化できます。

## ステップ 6: アプリケーションをレプリケートする

レプリケートするマシンがモビリティ サービスをインストールできるように準備されていることを確認してから、レプリケーションを有効にします。

### モビリティ サービスをインストールする

仮想マシンと物理サーバーの保護を有効にするための最初の手順は、モビリティ サービスのインストールです。これは、次のような何とおりかの方法で実行できます。

- **プロセス サーバー プッシュ**: マシンでレプリケーションを有効にしたら、プロセス サーバーからモビリティ サービス コンポーネントをプッシュしてインストールします。マシンで最新バージョンのコンポーネントを既に実行している場合、プッシュ インストールは実行されません。
- **エンタープライズ プッシュ**: WSUS や System Center Configuration Manager などのエンタープライズ プッシュ プロセスを使用してコンポーネントを自動的にインストールします。これを実行する前に、構成サーバーをセットアップします。
- **手動インストール**: レプリケートする各マシンにコンポーネントを手動でインストールします。これを実行する前に、構成サーバーをセットアップします。


#### Windows マシンで自動プッシュを準備する

プロセス サーバーによってモビリティ サービスが自動的にインストールされるように Windows マシンを準備する方法について説明します。

1.  プロセス サーバーから使用できる、マシンにアクセスするためのアカウントを作成します。このアカウントには管理者特権 (ローカルまたはドメイン) が必要です。このアカウントはプッシュ インストールにのみ使用されます。

	>[AZURE.NOTE] ドメイン アカウントを使用していない場合、ローカル マシンでのリモート ユーザーのアクセス制御を無効にする必要があります。無効にするには、レジストリ エディターで、HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Policies\\System の下に、値 1 を指定した DWORD エントリの LocalAccountTokenFilterPolicy を追加します。CLI からレジストリ エントリを追加するには、「**`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**」と入力します。

2.  保護対象のマシンの Windows ファイアウォールで、**[ファイアウォールを介したアプリまたは機能を許可する]** を選択します。**[ファイルとプリンターの共有]** と **[Windows Management Instrumentation]** を有効にします。マシンがドメインに属している場合は、GPO を使用してファイアウォール設定を構成できます。

	![ファイアウォールの設定](./media/site-recovery-vmware-to-azure/mobility1.png)

2. 作成したアカウントを追加します。

	- **cspsconfigtool** を開きます。cspsconfigtool はデスクトップにショートカットがあり、[インストール場所]\\home\\svsystems\\bin フォルダーに保存されています。
	- **[アカウントの管理]** タブの **[アカウントの追加]** をクリックします。
	- 作成したアカウントを追加します。アカウントを追加したら、マシンのレプリケーションを有効にするときに資格情報を入力する必要があります。


#### Linux サーバーの自動プッシュを準備する

1.	「[保護対象のマシンの前提条件](#protected-machine-prerequisites)」に示すように、保護対象の Linux マシンがサポートされていることを確認します。Linux マシンとプロセス サーバー間にネットワーク接続が存在することを確認します。

2.	プロセス サーバーから使用できる、マシンにアクセスするためのアカウントを作成します。このアカウントは、ソース Linux サーバーのルート ユーザーである必要があり、プッシュ インストールにのみ使用されます。

	- **cspsconfigtool** を開きます。cspsconfigtool はデスクトップにショートカットがあり、[インストール場所]\\home\\svsystems\\bin フォルダーに保存されています。
	- **[アカウントの管理]** タブの **[アカウントの追加]** をクリックします。
	- 作成したアカウントを追加します。アカウントを追加したら、マシンのレプリケーションを有効にするときに資格情報を入力する必要があります。

3.	ソース Linux サーバーの /etc/hosts ファイルに、ローカル ホスト名を、すべてのネットワーク アダプターに関連付けられた IP アドレスにマップするエントリが含まれることを確認します。
4.	レプリケートするマシンに最新の openssh、openssh-server、openssl の各パッケージをインストールします。
5.	SSH がポート 22 で有効であり実行中であることを確認します。
6.	以下の手順で、sshd\_config ファイルで SFTP サブシステムとパスワード認証を有効にします。

	- root としてログインします。
	- /etc/ssh/sshd\_config ファイルで、**PasswordAuthentication** で始まる行を見つけます。
	- この行のコメントを解除し、値 **no** を **yes** に変更します。
	- **Subsystem** で始まる行を見つけ、その行のコメントを解除します。

		![Linux](./media/site-recovery-vmware-to-azure/mobility2.png)


#### モビリティ サービスを手動でインストールする

インストーラーは、プロセス サーバーの **C:\\Program Files (x86)\\Microsoft Azure Site Recovery\\home\\svsystems\\pushinstallsvc\\repository** にあります。

ソース オペレーティング システム | モビリティ サービスのインストール ファイル
--- | ---
Windows Server (64 ビットのみ) | Microsoft-ASR\_UA\_9.*.0.0_Windows_* release.exe
CentOS 6.4、6.5、6.6 (64 ビットのみ) | Microsoft-ASR\_UA\_9.*.0.0\_RHEL6-64\_*release.tar.gz
SUSE Linux Enterprise Server 11 SP3 (64 ビットのみ) | Microsoft-ASR\_UA\_9.*.0.0\_SLES11-SP3-64\_*release.tar.gz
Oracle Enterprise Linux 6.4、6.5 (64 ビットのみ) | Microsoft-ASR\_UA\_9.*.0.0\_OL6-64\_*release.tar.gz


#### Windows サーバーに手動でインストールする


1. 関連するインストーラーをダウンロードして実行します。
2. **[開始する前に]** で **[モビリティ サービス]** を選択します。

	![モビリティ サービス](./media/site-recovery-vmware-to-azure/mobility3.png)

3. **[構成サーバーの詳細]** で、構成サーバーの IP アドレスと、統合セットアップを実行したときに生成されたパスフレーズを指定します。パスフレーズを取得するには、構成サーバーで **<SiteRecoveryInstallationFolder>\\home\\sysystems\\bin\\genpassphrase.exe –v** を実行します。

	![モビリティ サービス](./media/site-recovery-vmware-to-azure/mobility6.png)

4. **[インストール場所]** で既定の設定をそのまま使用し、**[次へ]** をクリックしてインストールを開始します。
5. **[インストールの進行状況]** でインストールを監視し、要求されたらマシンを再起動します。サービスのインストール後、ポータルで状態が更新されるまでに約 15 分かかることがあります。

次のようにコマンド ラインからインストールすることもできます。

UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <インストール ディレクトリ>] [/CSIP <登録する CS の IP アドレス>] [/PassphraseFilePath <パスフレーズ ファイルのパス>] [/LogFilePath <ログ ファイルのパス>]

各値の説明:

- /Role: 必須。モビリティ サービスをインストールするかどうかを指定します。
- /InstallLocation: 必須。サービスをインストールする場所を指定します。
- /PassphraseFilePath: 必須。構成サーバーのパスフレーズ。
- /LogFilePath: 必須。ログ セットアップ ファイルの場所。

#### モビリティ サービスを手動でアンインストールする

モビリティ サービスは、コントロール パネルの [プログラムの追加と削除] を使用するか、コマンド ラインを使用してアンインストールできます。

コマンド ラインを使用して、モビリティ サービスをアンインストールするコマンドは次のとおりです。

	MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1}


#### Linux サーバーに手動でインストールする:

1. 上記の表に基づいた適切な tar アーカイブをレプリケートする Linux マシンにコピーします。
2. シェル プログラムを開き、`tar -xvzf Microsoft-ASR_UA_8.5.0.0*` を実行して、zip 形式の tar アーカイブをローカル パスに抽出します。
3. tar アーカイブの内容を抽出したローカル ディレクトリに passphrase.txt ファイルを作成します。このファイルを作成するには、構成サーバーの C:\\ProgramData\\Microsoft Azure Site Recovery\\private\\connection.passphrase からパスフレーズをコピーし、シェルで *`echo <passphrase> >passphrase.txt`* を実行して passphrase.txt に保存します。
4. *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`* を実行して、モビリティ サービスをインストールします。
5. 構成サーバーの内部 IP アドレスを指定し、ポート 443 が選択されていることを確認します。サービスのインストール後、ポータルで状態が更新されるまでに約 15 分かかることがあります。

**次のようにコマンド ラインからインストールすることもできます**。

1. 構成サーバーの C:\\Program Files (x86)\\InMage Systems\\private\\connection からパスフレーズをコピーし、"passphrase.txt" というファイル名で構成サーバーに保存します。次のコマンドを実行します。この例では、構成サーバーの IP アドレスは 104.40.75.37、HTTPS ポートは 443 です。

運用サーバーにインストールするには:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

マスター ターゲット サーバーにインストールするには:


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt


### Enable replication

#### 開始する前に

VMware 仮想マシンをレプリケートする場合、次の点に注意してください。

- VMware VM は 15 分間隔で検出されます。検出後、ポータルに表示されるまでに 15 分以上かかることがあります。同様に、新しい vCenter サーバーまたは vSphere ホストを追加したときも、検出に 15 分以上かかることがあります。
- 仮想マシンの環境の変更 (VMware ツールのインストールなど) も、ポータルで更新されるまでに 15 分以上かかることがあります。
- **[構成サーバー]** ブレードの vCenter サーバー/vSphere ホストの **[最後の使用]** フィールドで VMware VM の最終検出時刻を確認できます。
- スケジュール済み検出を待たずにレプリケートするマシンを追加するには、構成サーバーを強調表示し (クリックしないでください)、**[更新]** ボタンをクリックします。
- レプリケーションを有効にした場合、マシンの準備が完了すると、プロセス サーバーによってモビリティ サービスが自動的にインストールされます。

#### レプリケーションからディスクを除外する

レプリケーションを有効にすると、既定でマシンのすべてのディスクがレプリケートされます。レプリケーションからディスクを除外できます。たとえば、一時的なデータや、マシンまたはアプリケーションを再起動するたびに更新されるデータ (pagefile.sys や SQL Server tempdb など) が保存されたディスクをレプリケートから除外できます。ディスクを除外する場合、次の点に注意してください。

- 除外できるのは、モビリティ サービスが既にインストールされているディスクだけです。モビリティ サービスは、レプリケーションが有効になった後、プッシュ メカニズムによってのみインストールされるため、[モビリティ サービスを手動でインストール](#install-the-mobility-service-manually)する必要があります。
- レプリケーションから除外できるのは、ベーシック ディスクだけです。OS ディスクまたはダイナミック ディスクは除外できません。
- レプリケーションが有効になった後に、レプリケートするディスクを追加または削除することはできません。ディスクを追加または除外する場合は、マシンの保護を無効にし、再度有効にする必要があります。
- アプリケーションが動作するために必要なディスクを除外した場合、Azure へのフェールオーバー後、レプリケートされたアプリケーションを実行できるように、Azure でディスクを手動で作成する必要があります。別の方法として、Azure Automation を復旧計画に組み込んで、マシンのフェールオーバー時にディスクを作成することもできます。
- Azure で手動で作成したディスクはフェールバックされます。たとえば、3 つのディスクをフェールオーバーし、Azure で 2 つのディスクを直接作成した場合、5 つのディスクがすべてフェールバックされます。手動で作成したディスクは、フェールバックから除外できません。

**レプリケーションを有効にするには、次の手順に従います**。

1. **[手順 2: アプリケーションをレプリケートする]**、**[ソース]** の順にクリックします。レプリケーションを初めて有効にした後は、コンテナーで **[+ レプリケート]** をクリックして、追加のマシンのレプリケーションを有効にします。
2. **[ソース]** ブレードで **[ソース]** をクリックし、構成サーバーを選択します。
3. **[マシンの種類]** で、**[仮想マシン]** または **[物理マシン]** を選択します。
4. **[vCenter/vSphere ハイパーバイザー]** で、vSphere ホストを管理する vCenter サーバーを選択するか、ホストを選択します。物理マシンをレプリケートする場合、この設定は関係ありません。
5. プロセス サーバーを選択します。追加のプロセス サーバーを作成していない場合、これは構成サーバーの名前になります。次に、 **[OK]** をクリックします

	![Enable replication](./media/site-recovery-vmware-to-azure/enable-replication2.png)

6. **[ターゲット]** でコンテナーのサブスクリプションを選択し、**[フェールオーバー後のデプロイ モデル]** でフェールオーバー後に Azure で使用するモデル (クラシックまたはリソース管理) を選択します。
7. データのレプリケーションに使用する Azure Storage アカウントを選択します。以下の点に注意してください。

	- Premium または Standard ストレージ アカウントを選択できます。Premium アカウントを選択した場合は、継続的なレプリケーション ログ用に追加の Standard ストレージ アカウントを指定する必要があります。アカウントは、Recovery Services コンテナーと同じリージョンに存在する必要があります。
	- 現在持っているものとは別のストレージ アカウントを使用する場合は、[ストレージ アカウントを作成](#set-up-an-azure-storage-account)できます。ARM モデルを使用してストレージ アカウントを作成するには、**[新規作成]** をクリックします。クラシック モデルを使用してストレージ アカウントを作成する場合は、[Azure ポータル](../storage/storage-create-storage-account-classic-portal.md)で作成できます。

8. フェールオーバー後に Azure VM がスピンアップされたときに接続する Azure ネットワークとサブネットを選択します。ネットワークは、Recovery Services コンテナーと同じリージョンにある必要があります。保護の対象として選択したすべてのマシンにネットワーク設定を適用する場合は、**[選択したマシン用に今すぐ構成します。]** を選択します。マシンごとに Azure ネットワークを選択する場合は、**[後で構成する]** を選択します。ネットワークがない場合は、[ネットワークを作成](#set-up-an-azure-network)する必要があります。ARM モデルを使用してネットワークを作成するには、**[新規作成]** をクリックします。クラシック モデルを使用してネットワークを作成する場合は、[Azure ポータル](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)で作成します。該当する場合は、サブネットを選択します。次に、 **[OK]** をクリックします

	![Enable replication](./media/site-recovery-vmware-to-azure/enable-replication3.png)

9. **[仮想マシン]** の **[仮想マシンの選択]** で、レプリケートする各マシンをクリックして選択します。選択できるのは、レプリケーションを有効にできるマシンのみです。次に、 **[OK]** をクリックします

	![Enable replication](./media/site-recovery-vmware-to-azure/enable-replication5.png)

10. **[プロパティ]** の **[プロパティの構成]** で、モビリティ サービスをマシンに自動的にインストールするためにプロセス サーバーが使用するアカウントを選択します。既定では、すべてのディスクがレプリケートされます。**[すべてのディスク]** をクリックし、レプリケートしないディスクをオフにします。次に、 **[OK]** をクリックします後で追加のプロパティを設定できます。

	![Enable replication](./media/site-recovery-vmware-to-azure/enable-replication6.png)

11. **[レプリケーション設定]** の **[レプリケーション設定の構成]** で、正しいレプリケーション ポリシーが選択されていることを確認します。レプリケーション ポリシー設定を変更するには、**[設定]**、**[レプリケーション ポリシー]**、ポリシー名、**[設定の編集]** の順にクリックします。ポリシーに適用した変更は、レプリケートしているマシンと新しいマシンに適用されます。

12. マシンをレプリケーション グループにまとめる場合は、**[マルチ VM 整合性]** を有効にし、グループの名前を指定します。次に、 **[OK]** をクリックします以下の点に注意してください。

	- レプリケーション グループのマシンはまとめてレプリケートされ、フェールオーバー時にクラッシュ整合性復旧ポイントとアプリ整合性復旧ポイントを共有します。
	- VM と物理サーバーがワークロードをミラー化できるように、これらをまとめることをお勧めします。マルチ VM 整合性を有効にすると、ワークロードのパフォーマンスに影響を及ぼす可能性があるので、複数のマシンが同じワークロードを実行しており、整合性が必要な場合にのみ使用します。

	![Enable replication](./media/site-recovery-vmware-to-azure/enable-replication7.png)

13. **[レプリケーションを有効にする]** をクリックします。**[設定]**、**[ジョブ]**、**[Site Recovery jobs]** (Site Recovery ジョブ) の順にクリックして、**保護の有効化**ジョブの進行状況を追跡できます。**保護の最終処理**ジョブが実行されると、マシンはフェールオーバーできる状態になります。

> [AZURE.NOTE] 保護が有効になっている場合、マシンでプッシュ インストールの準備が完了すると、モビリティ サービス コンポーネントがインストールされます。コンポーネントがマシンにインストールされたら、保護ジョブが開始され、失敗します。ジョブの失敗後、各マシンを手動で再起動する必要があります。再起動後に、保護ジョブが再び開始され、最初のレプリケーションが実行されます。

### VM プロパティを表示して管理する

ソース マシンのプロパティを確認することをお勧めします。Azure VM の名前は、[Azure 仮想マシンの要件](site-recovery-best-practices.md#azure-virtual-machine-requirements)に準拠している必要があります。

1. **[設定]**、**[レプリケートされたアイテム]** の順にクリックし、マシンを選択します。**[要点]** ブレードにマシンの設定と状態に関する情報が表示されます。

2. **[プロパティ]** で、VM のレプリケーションとフェールオーバーの情報を確認できます。

	![Enable replication](./media/site-recovery-vmware-to-azure/test-failover2.png)

3. **[計算とネットワーク]** の **[計算のプロパティ]** で、Azure VM の名前とターゲットのサイズを指定できます。必要に応じて、Azure の要件に準拠するように名前を変更します。Azure VM に割り当てられるターゲット ネットワーク、サブネット、および IP アドレスに関する情報を表示および追加することもできます。以下の点に注意してください。

	- ターゲット IP アドレスを設定できます。アドレスを指定しなかった場合、フェールオーバーされたマシンで DHCP が使用されます。フェールオーバーで使用できないアドレスを設定した場合、フェールオーバーは機能しません。テスト フェールオーバー ネットワークのアドレスを利用できる場合、テスト フェールオーバーに同じターゲット IP アドレスを使用できます。
	- ネットワーク アダプターの数は、次に示すように、ターゲット仮想マシンに指定したサイズによって異なります。

		- ソース マシン上のネットワーク アダプターの数が、ターゲット マシンのサイズに許可されているアダプターの数以下の場合、ターゲットのアダプターの数は、ソースと同じになります。
		- ソース仮想マシン用のアダプターの数が、ターゲットのサイズに許可されている数を超える場合は、ターゲットの最大サイズが使用されます。
		- たとえば、ソース マシンに 2 つのネットワーク アダプターがあり、ターゲット マシンのサイズが 4 つをサポートしている場合は、ターゲット マシンのアダプターの数は、2 つになります。ソース マシンに 2 つのアダプターがあるが、サポートされているターゲット サイズで 1 つしかサポートしていない場合、ターゲット マシンのアダプターの数は 1 つだけになります。
	- VM に複数のネットワーク アダプターがある場合は、すべて同じネットワークに接続されます。

	![Enable replication](./media/site-recovery-vmware-to-azure/test-failover4.png)

4. **[ディスク]** で、レプリケートされる VM のオペレーティング システム ディスクとデータ ディスクを確認できます。


## ステップ 7: デプロイをテストする

デプロイをテストするために、単一の仮想マシンに対して、または 1 つ以上の仮想マシンを含む復旧計画に対してテスト フェールオーバーを実行できます。


### フェールオーバーを準備する

- テスト フェールオーバーを実行する場合は、Azure 実稼働用ネットワークから分離された Azure ネットワークを新たに作成する必要があります (これは Azure で新しいネットワークを作成する場合の既定の動作です)。テスト フェールオーバーの実行の詳細については、[こちら](site-recovery-failover.md#run-a-test-failover)をご覧ください。
- Azure へのフェールオーバーを実行するときに最適なパフォーマンスを得るには、保護されたマシンに Azure エージェントをインストールします。エージェントをインストールすると、起動時間が短縮され、トラブルシューティングにも役立ちます。[Linux](https://github.com/Azure/WALinuxAgent) エージェントまたは [Windows](http://go.microsoft.com/fwlink/?LinkID=394789) エージェントをインストールします。
- デプロイメントを完全にテストするには、レプリケートされたマシンが正常に動作するインフラストラクチャが必要です。Active Directory と DNS をテストする場合は、ドメイン コントローラー兼 DNS として仮想マシンを作成し、これを Azure Site Recovery を使用して Azure にレプリケートします。Active Directory のテスト フェールオーバーの考慮事項については、[こちら](site-recovery-active-directory.md#considerations-for-test-failover)をご覧ください。
- 構成サーバーが実行されていることを確認します。そうでない場合、フェールオーバーは失敗します。
- レプリケーションからディスクを除外した場合、アプリケーションが予想どおりに動作するように、フェールオーバー後に Azure でそれらのディスクを手動で作成する必要があります。
- テスト フェールオーバーではなく計画されていないフェールオーバーを実行する場合は、次の点に注意してください。

	- 可能であれば、プライマリ マシンをシャットダウンしてから、計画されていないフェールオーバーを実行します。こうすることで、ソース マシンとレプリカ マシンが同時に実行されないように確保できます。VMware VM をレプリケートする場合、Site Recovery が可能な限りソース マシンをシャットダウンするように指定できます。プライマリ サイトの状態によっては、シャットダウンが動作しない場合があります。物理サーバーをレプリケートする場合、Site Recovery にこのオプションは表示されません。
	- 計画されていないフェールオーバーを実行すると、プライマリ マシンのデータ レプリケーションが停止され、計画されていないフェールオーバーの開始後に、データの差分は転送されなくなります。また、復旧計画で計画されていないフェールオーバーを実行する場合は、エラーが発生した場合でも、復旧計画は最後まで実行されます。

### フェールオーバー後に Azure VM に接続するための準備をする

フェールオーバー後に RDP を使用して Azure VM に接続する場合は、次のことを行う必要があります。

**フェールオーバー前にオンプレミスのマシンで行う操作**:

- インターネット経由のアクセスで RDP を有効にする場合は、TCP と UDP の規則が **[パブリック]** に追加されていることを確認し、すべてのプロファイルについて、**[Windows ファイアウォール]** の **[許可されたアプリおよび機能]** で RDP が許可されていることを確認します。
- マシンでサイト間接続経由のアクセスに対して RDP を有効にする場合は、**[ドメイン]** および **[プライベート]** ネットワークについて、**[Windows ファイアウォール]** の **[許可されたアプリおよび機能]** で RDP が許可されていることを確認します。
- オンプレミスのマシンに [Azure VM エージェント](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)をインストールします。
- プロセス サーバーを使用してサービスを自動的にプッシュするのではなく、マシンに[モビリティ サービスを手動でインストール](#install-the-mobility-service-manually)します。これは、マシンのレプリケーションが有効になってから、プッシュ インストールが実行されるためです。
- オペレーティング システムの SAN ポリシーが OnlineAll に設定されていることを確認します。[詳細情報](https://support.microsoft.com/kb/3031135)
- フェールオーバーを実行する前に、IPSec サービスを無効にします。

**フェールオーバー後に Azure VM で行う操作**:

- RDP プロトコル (ポート 3389) のパブリック エンドポイントを追加し、ログインの資格情報を指定します。
- パブリック アドレスを使用する仮想マシンへの接続を妨げるドメイン ポリシーを使用していないことを確認します。
- 接続を試みます。接続できない場合は、VM が実行されていることを確認します。トラブルシューティングのヒントについては、[こちらの記事](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)をご覧ください。


フェールオーバー後に Linux が実行されている Azure VM に Secure Shell クライアント (ssh) を使用してアクセスする場合は、次の手順に従います。

**フェールオーバー前にオンプレミスのマシンで行う操作**:

- Azure VM 上の Secure Shell サービスがシステム起動時に自動的に開始されるよう設定されていることを確認します。
- ファイアウォール規則で SSH 接続が許可されていることを確認します。

**フェールオーバー後に Azure VM で行う操作**:

- フェールオーバーされた VM および接続先の Azure サブネットのネットワーク セキュリティ グループの規則で、SSH ポートへの着信接続を許可する必要があります。
- SSH ポート (既定では TCP ポート 22) で着信接続を許可するようにパブリック エンドポイントが作成されている必要があります。
- VM が VPN 接続 (ExpressRoute またはサイト間 VPN) 経由でアクセスされる場合は、クライアントを使用して SSH 経由で直接 VM に接続できます。

**フェールオーバー後に Azure Windows/Linux VM で行う操作**:

仮想マシンまたはその仮想マシンが属しているサブネットに関連付けられたネットワーク セキュリティ グループがある場合は、そのネットワーク セキュリティ グループの送信規則で HTTP/HTTPS を許可していることを確認してください。また、仮想マシンのフェールオーバー先となるネットワークの DNS が正しく構成されていることも確認してください。確認できない場合は、"PreFailoverWorkflow タスクの WaitForScriptExecutionTask がタイムアウトしました" というエラーでタイムアウトになる可能性があります。詳細については、[監視とトラブルシューティング ガイド](site-recovery-monitoring-and-troubleshooting.md#recovery)の復旧に関するセクションをご覧ください。

## テスト フェールオーバーの実行

1. 1 つの仮想マシンをフェールオーバーする場合は、**[設定]** の **[レプリケートされたアイテム]** で、その VM をクリックし、**[+ テスト フェールオーバー]** をクリックします。

	![テスト フェールオーバー](./media/site-recovery-vmware-to-azure/test-failover1.png)

2. 復旧計画をフェールオーバーする場合は、**[設定]** の **[復旧計画]** で、計画を右クリックし、**[テスト フェールオーバー]** をクリックします。復旧計画を作成する場合は、[こちらの手順に従ってください](site-recovery-create-recovery-plans.md)。

3. **[テスト フェールオーバー]** で、フェールオーバー後に Azure VM が接続する Azure ネットワークを選択します。
4. **[OK]** をクリックすると、フェールオーバーが開始されます。進行状況を追跡するには、VM をクリックしてそのプロパティを開くか、コンテナー名、**[設定]**、**[ジョブ]**、**[Site Recovery ジョブ]** で **[テスト フェールオーバー]** ジョブをクリックします。
5. フェールオーバーが **[テストの完了]** 状態になったら、次の手順に従います。

	1. Azure ポータルで、レプリカ仮想マシンを表示します。仮想マシンが正常に起動することを確認します。
	2. オンプレミスのネットワークから、仮想マシンへのアクセスをセットアップすると、仮想マシンへのリモート デスクトップ接続を開始できます。
	3. **[テストの完了]** をクリックしてテストを終了します。

		![テスト フェールオーバー](./media/site-recovery-vmware-to-azure/test-failover6.png)


	4. **[メモ]** をクリックして、テスト フェールオーバーに関連する監察結果をすべて記録し、保存します。
	5. **[テスト フェールオーバーが完了しました]** をクリックすると、テスト環境は自動的にクリーンアップされます。この処理が完了すると、テスト フェールオーバーには **[完了]** という状態が表示されます。
	6.  この段階で、テスト フェールオーバー時に Site Recovery によって自動的に作成されたすべての要素または VM は削除されます。テスト フェールオーバー用に作成した追加の要素は削除されません。

	> [AZURE.NOTE] テスト フェールオーバーの実行時間が 2 週間を超えた場合は、強制的に終了されます。


6. フェールオーバーの完了後は、Azure ポータルの **[仮想マシン]** にレプリカの Azure マシンも表示されるようになります。VM が適切なサイズであること、適切なネットワークに接続していること、実行されていることを確認する必要があります。
7. [フェールオーバー後の接続の準備](#prepare-to-connect-to-azure-vms-after-failover)が完了したら、Azure VM に接続できます。

## デプロイを監視する

Site Recovery デプロイメントの構成設定、状態、および正常性を監視する方法を次に示します。

1. コンテナー名をクリックして、**[要点]** ダッシュボードにアクセスします。このダッシュボードで、Site Recovery ジョブ、レプリケーションの状態、復旧計画、サーバーの状態、およびイベントを確認できます。[要点] ダッシュボードをカスタマイズして、他の Site Recovery コンテナーや Backup コンテナーの状態など、ニーズに最適なタイルとレイアウトを表示できます。<br>![Essentials](./media/site-recovery-vmware-to-azure/essentials.png)

2. **[正常性]** タイルで、問題が発生しているサイト サーバー (VMM または構成サーバー) と、Site Recovery によって過去 24 時間以内に発生したイベントを監視できます。
3. **[レプリケートされたアイテム]**、**[復旧計画]**、**[Site Recovery ジョブ]** の各タイルで、レプリケーションの管理と監視を実行できます。**[設定]**、**[ジョブ]**、**[Site Recovery ジョブ]** の順にクリックすると、ジョブの詳細を確認できます。


## 追加のプロセス サーバーをデプロイする

200 台を超えるソース マシンまたは 2 TB を超える合計日次変更率にデプロイメントをスケールアウトする必要がある場合、トラフィック ボリュームに対応するためにプロセス サーバーを追加する必要があります。

[プロセス サーバーのサイズの推奨事項](#size-recommendations-for-the-process-server)を確認し、次の手順に従ってプロセス サーバーをセットアップします。サーバーをセットアップしたら、サーバーを使用するソース マシンを移行します。

### 追加のプロセス サーバーをインストールする

1. **[設定]** の **[Site Recovery サーバー]** で、構成サーバー、**[プロセス サーバー]** の順にクリックします。

	![プロセス サーバーの追加](./media/site-recovery-vmware-to-azure/migrate-ps1.png)

2. **[サーバーの種類]** で、**[プロセス サーバー (オンプレミス)]** をクリックします。

	![プロセス サーバーの追加](./media/site-recovery-vmware-to-azure/migrate-ps2.png)

3. Site Recovery 統合セットアップ ファイルをダウンロードします。このファイルを実行してプロセス サーバーをインストールし、コンテナーに登録します。
4. **[開始する前に]** で **[Add additional process servers to scale out deployment (デプロイをスケールアウトするためにプロセス サーバーを追加する)]** を選択します。
5. 構成サーバーを[セットアップ](#step-2-set-up-the-source-environment)したときと同様にウィザードを完了します。

	![プロセス サーバーの追加](./media/site-recovery-vmware-to-azure/add-ps1.png)

6. **[構成サーバーの詳細]** で、構成サーバーの IP アドレスとパスフレーズを指定します。パスフレーズを取得するには、構成サーバーで **<SiteRecoveryInstallationFolder>\\home\\sysystems\\bin\\genpassphrase.exe –n** を実行します。

	![プロセス サーバーの追加](./media/site-recovery-vmware-to-azure/add-ps2.png)

### マシンを移行して新しいプロセス サーバーを使用する

1. **[設定]** の **[Site Recovery サーバー]** で、構成サーバーをクリックし、**[プロセス サーバー]** を展開します。

	![プロセス サーバーの更新](./media/site-recovery-vmware-to-azure/migrate-ps2.png)

2. 現在使用中のプロセス サーバーを右クリックし、**[切り替え]** をクリックします。

	![プロセス サーバーの更新](./media/site-recovery-vmware-to-azure/migrate-ps3.png)

3. **[ターゲット プロセス サーバーの選択]** で、使用する新しいプロセス サーバーを選択し、そのサーバーが処理する仮想マシンを選択します。サーバーに関する情報を確認するには、情報アイコンをクリックします。負荷の決定に役立つように、選択された各仮想マシンを新しいプロセス サーバーにレプリケートするために必要な平均容量が表示されます。チェック マークをクリックして、新しいプロセス サーバーへのレプリケーションを開始します。

## VMware アカウントのアクセス許可

プロセス サーバーは vCenter サーバー上の VM を自動的に検出できます。自動検出を実行するには、[ロール (Azure\_Site\_Recovery) を定義](#prepare-an-account-for-automatic-discovery)して、Site Recovery が VMware サーバーにアクセスできるようにする必要があります。VMware マシンを Azure に移行する処理のみが必要で、Azure からのフェールバックは必要ない場合、読み取り専用ロールを定義するだけで十分です。必要なロールのアクセス許可を次の表に示します。

**ロール** | **詳細** | **アクセス許可**
--- | --- | ---
Azure\_Site\_Recovery ロール | VMware VM の検出 |vCenter サーバーの次の特権を割り当てます。<br/><br/>データストア -> 空間の割り当て、データストアの参照、低レベル ファイルの操作、ファイルの削除、仮想マシン ファイルの更新<br/><br/>ネットワーク -> ネットワークの割り当て<br/><br/>リソース -> 仮想マシンをリソース プールに割り当て、電源がオフのマシンの移行、電源がオンのマシンの移行<br/><br/>タスク -> タスクの作成、タスクの更新<br/><br/>仮想マシン -> 構成<br/><br/>仮想マシン -> 対話 -> 質問への回答、デバイス接続、CD メディアの構成、フロッピー メディアの構成、電源オフ、電源オン、VMware ツールのインストール<br/><br/>仮想マシン -> インベントリ -> 作成、登録、登録解除<br/><br/>仮想マシン -> プロビジョニング -> 仮想マシンのダウンロードの許可、仮想マシン ファイルのアップロードの許可<br/><br/>仮想マシン -> スナップショット -> スナップショットの削除
vCenter ユーザー ロール | ソース VM をシャットダウンしない VMware VM の検出/フェールオーバー | vCenter サーバーに次の特権を割り当てます。<br/><br/>データ センター オブジェクト -> 子オブジェクトへのプロパゲート、ロール=読み取り専用 <br/><br/>ユーザーはデータセンター レベルで割り当てられるので、データセンター内のすべてのオブジェクトに対してアクセス権を持ちます。アクセスを制限する場合は、**子オブジェクトへのプロパゲート**特権を持つ**アクセスなし**ロールを子オブジェクト (vSphere ホスト、データストア、VM、ネットワーク) に割り当てます。
vCenter ユーザー ロール | フェールオーバーとフェールバック | vCenter サーバーに次の特権を割り当てます。<br/><br/>データセンター オブジェクト - 子オブジェクトへのプロパゲート、ロール=Azure\_Site\_Recovery<br/><br/>ユーザーはデータセンター レベルで割り当てられるので、データセンター内のすべてのオブジェクトに対してアクセス権を持ちます。アクセスを制限する場合は、**子オブジェクトへのプロパゲート**特権を持つ**アクセスなし**ロールを子オブジェクト (vSphere ホスト、データストア、VM、ネットワーク) に割り当てます。  
## 次のステップ

- 各種フェールオーバーの[詳細を確認](site-recovery-failover.md)します。
- Azure で実行されているフェールオーバーされたマシンをオンプレミス環境に戻す[フェールバックの詳細を確認](site-recovery-failback-azure-to-vmware.md)します。

## サード パーティ製ソフトウェアに関する通知および情報

Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”).Microsoft is the not original author of the Third Party Code.The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below.Such licenses and information are provided for informational purposes only.This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428).Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.

<!---HONumber=AcomDC_0921_2016-->