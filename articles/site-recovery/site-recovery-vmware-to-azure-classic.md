<properties
	pageTitle="Azure Site Recovery を使用して VMware 仮想マシンと物理サーバーを Azure にレプリケートする | Microsoft Azure"
	description="この記事では、Azure Site Recovery をデプロイしてオンプレミスの VMware 仮想マシンまたは Windows/Linux 物理サーバーの Azure へのレプリケーション、フェールオーバー、復旧を調整する方法を説明します。"
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

# Azure Site Recovery を使用して VMware 仮想マシンと物理サーバーを Azure にレプリケートする

> [AZURE.SELECTOR]
- [Azure ポータル](site-recovery-vmware-to-azure.md)
- [クラシック ポータル](site-recovery-vmware-to-azure-classic.md)
- [クラシック ポータル (レガシ)](site-recovery-vmware-to-azure-classic-legacy.md)


Azure Site Recovery サービスは、仮想マシンと物理サーバーのレプリケーション、フェールオーバー、復旧を調整してビジネス継続性と障害復旧 (BCDR) 戦略に貢献します。コンピューターを Azure に、またはオンプレミスのセカンダリ データ センターにレプリケートできます。簡単な概要については、「[Azure Site Recovery とは](site-recovery-overview.md)」を参照してください。

## 概要

この記事では、次の方法について説明します。

- **VMware 仮想マシンを Azure にレプリケートする** - Site Recovery をデプロイして、オンプレミス VMware 仮想マシンから Azure Storage へのレプリケーション、フェールオーバー、復旧を調整します。
- **物理サーバーを Azure にレプリケートする** - Azure Site Recovery をデプロイして、Windows/Linux オンプレミス物理サーバーから Azure へのレプリケーション、フェールオーバー、復旧を調整します。

>[AZURE.NOTE] この記事では、Azure にレプリケートする方法について説明します。VMware VM または Windows/Linux 物理サーバーをセカンダリ データセンターにレプリケートする場合は、[この記事](site-recovery-vmware-to-vmware.md)の手順に従ってください。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で投稿してください。

## 強化されたデプロイ

この記事では、クラシック Azure ポータルの強化されたデプロイメントの手順について説明します。すべての新しいデプロイにこのバージョンを使用することをお勧めします。以前のレガシ バージョンを使用してデプロイした場合は、新しいバージョンに移行することをお勧めします。移行の詳細については、[こちら](site-recovery-vmware-to-azure-classic-legacy.md##migrate-to-the-enhanced-deployment)をご覧ください。

強化されたデプロイは大きな更新です。次に、この改善内容の概要について説明します。

- **Azure にインフラストラクチャ VM がない**: データは Azure Storage アカウントに直接レプリケートされます。レガシ デプロイメントでは必要でしたが、レプリケーションとフェールオーバーの目的以外に、インフラストラクチャ VM (構成サーバー、マスター ターゲット サーバー) を設定する必要はありません。
- **統合されたインストール**: 1 回のインストールで、オンプレミス コンポーネントの簡単なセットアップとスケーラビリティを実現します。
- **セキュリティで保護されたデプロイ**: すべてのトラフィックが暗号化され、レプリケーション管理の通信は HTTPS 443 経由で送信されます。
- **復旧ポイント**: Windows/Linux 環境では、クラッシュ整合性およびアプリケーション整合性復旧ポイントがサポートされます。また、単一 VM 整合性とマルチ VM 整合性の両方の構成がサポートされます。
- **テスト フェールオーバー**: 運用環境に影響を与えたり、レプリケーションを一時停止したりすることなく、中断を伴わない Azure へのテスト フェールオーバーがサポートされます。
- **計画外のフェールオーバー**: フェールオーバーの前に VM を自動的にシャットダウンする拡張オプションを使用した Azure への計画外のフェールオーバーがサポートされます。
- **フェールバック**: フェールバックが統合され、差分変更データのみがオンプレミス サイトにレプリケートされます。
- **vSphere 6.0**: VMware Vsphere 6.0 デプロイメントの限定的なサポート。


## Site Recovery で仮想マシンと物理サーバーを保護する方法


- VMware 管理者は、Azure に対して VMware 仮想マシンで実行されているビジネスのワークロードとアプリケーションのオフサイト保護を構成できます。サーバー マネージャーは、オンプレミスの Windows および Linux 物理サーバーを Azure にレプリケートできます。
- Azure Site Recovery コンソールは、レプリケーション、フェールオーバー、および回復プロセスを簡単にセットアップおよび管理できる 1 つの場所として機能します。
- vCenter サーバーで管理される VMware 仮想マシンをレプリケートする場合、Site Recovery はその VM を自動的に検出できます。仮想マシンが ESXi ホスト上にある場合、Site Recovery はそのホスト上の VM を検出します。
- オンプレミス インフラストラクチャから Azure へのフェールオーバーと、Azure からオンプレミス サイトの VMware VM サーバーへのフェールオーバー (復元) を簡単に実行できます。
- 複数のマシンに階層化されているアプリケーション ワークロードをグループ化する回復計画を構成できます。また、その回復計画をフェールオーバーできます。Site Recovery には複数 VM の整合性機能があるので、同じワークロードを実行しているマシンを整合性データ ポイントにまとめて復元できます。


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

シナリオのコンポーネント:

- **オンプレミス管理サーバー**: 管理サーバーは、次の Site Recovery コンポーネントを実行します。
	- **構成サーバー**: 通信を調整し、データ レプリケーションと復旧プロセスを管理します。
	- **プロセス サーバー**: レプリケーション ゲートウェイとして機能します。プロセス サーバーは保護されたソース マシンからデータを受信し、キャッシュ、圧縮、および暗号化を使用して最適化し、レプリケーション データを Azure Storage に送信します。また、保護されたマシンへのモビリティ サービスのプッシュ インストールを処理し、VMware VM の自動検出を実行します。
	- **マスター ターゲット サーバー**: Azure からのフェールバック中にレプリケーション データを処理します。デプロイをスケールするために、プロセス サーバーとしてのみ動作する管理サーバーをデプロイすることもできます。
- **モビリティ サービス**: このコンポーネントは、Azure にレプリケートする各マシン (VMware VM または物理サーバー) にデプロイされます。マシン上のデータの書き込みをキャプチャし、それをプロセス サーバーに転送します。
- **Azure**: レプリケーションとフェールオーバーを処理するために Azure VM を作成する必要はありません。Site Recovery サービスがデータ管理を処理し、データは Azure Storage に直接レプリケートされます。レプリケートされた Azure VM は、Azure へのフェールオーバーが発生した場合にのみ、自動的にスピンアップされます。ただし、Azure からオンプレミス サイトにフェールバックする場合、プロセス サーバーとして動作する Azure VM を設定する必要があります。


次の図は、これらのコンポーネント間でのやり取りを示しています。

![アーキテクチャ](./media/site-recovery-vmware-to-azure/v2a-architecture-henry.png)

**図 1: VMware/物理マシンと Azure** (作成者: Henry Robalino)


## 容量計画

容量計画を立てる場合、次の点について考慮する必要があります。

- **ソース環境** - キャパシティ プランニングや、VMware インフラストラクチャとソース マシンの要件。
- **管理サーバー** - Site Recovery コンポーネントを実行するオンプレミス管理サーバーの計画。
- **ソースからターゲットまでのネットワーク帯域幅** - ソースと Azure の間のレプリケーションに必要なネットワーク帯域幅の計画。

### ソース環境の考慮事項

- **日次変更率の上限** - 保護されたマシンが使用できるプロセス サーバーは 1 つだけです。また、1 つのプロセス サーバーが対応できる日次変更率は最大 2 TB です。そのため、保護されたマシンでサポートされる 1 日のデータ変化率の上限は 2 TB です。
- **最大スループット** - レプリケートされたマシンは、Azure の 1 つのストレージ アカウントに属することができます。Standard ストレージ アカウントは、1 秒間に最大 20,000 要求を処理できます。ソース マシン全体の IOPS 数を 20,000 以下に抑えることをお勧めします。たとえば、5 個のディスクが搭載されたソース マシンで、各ディスクから 120 IOPS (8K サイズ) が生成される場合、Azure 内のディスクごとの IOPS 制限は 500 になります。必要なストレージ アカウントの数は、合計ソース IOPS/20000 です。


### 管理サーバーの考慮事項

管理サーバーは、データの最適化、レプリケーション、および管理を処理する Site Recovery コンポーネントを実行します。管理サーバーは、保護されたマシンで実行されているすべてのワークロードの 1 日の変更率容量を処理できる必要があります。また、Azure Storage にデータを継続的にレプリケートできる十分な帯域幅を確保します。具体的には次の処理が行われます。

- プロセス サーバーは、保護されたマシンからレプリケーション データを受信し、Azure に送信する前にキャッシュ、圧縮、および暗号化を使用して最適化します。管理サーバーには、これらのタスクを実行できる十分なリソースが必要です。
- プロセス サーバーは、ディスク ベースのキャッシュを使用します。ネットワークのボトルネックや障害が発生した場合に、格納されているデータの変更を処理できるように、600 GB 以上のキャッシュ ディスクを別に用意することをお勧めします。デプロイ時に、空き領域が 5 GB 以上ある任意のドライブにキャッシュを構成できますが、600 GB は最小限の推奨領域です。
- ベスト プラクティスとして、保護対象のマシンと同じネットワークおよび LAN セグメントに管理サーバーを配置することをお勧めします。管理サーバーを別のネットワークに配置することもできますが、保護対象のマシンは管理サーバーに対して、L3 のネットワーク可視性が必要です。

管理サーバーのサイズの推奨事項を次の表にまとめています。

**管理サーバーの CPU** | **メモリ** | **キャッシュ ディスク サイズ** | **データの変更率** | **保護されたマシン**
--- | --- | --- | --- | ---
8 vCPU (2 ソケット * 4 コア @ 2.5 GHz) | 16 GB | 300 GB | 500 GB 以下 | 100 台未満のマシンをレプリケートできる設定で管理サーバーをデプロイします。
12 vCPU (2 ソケット * 6 コア @ 2.5 GHz) | 18 GB | 600 GB | 500 GB ～ 1 TB | 100 ～ 150 台のマシンをレプリケートできる設定で管理サーバーをデプロイします。
16 vCPU (2 ソケット * 8 コア @ 2.5GHz) | 32 GB | 1 TB (テラバイト) | 1 TB ～ 2 TB | 150 ～ 200 台のマシンをレプリケートできる設定で管理サーバーをデプロイします。
別のプロセス サーバーをデプロイします | | | > 2 TB | 200 台を超えるマシンをレプリケートする場合、または 1 日のデータ変化率が 2 TB を超える場合、追加のプロセス サーバーをデプロイします。

各値の説明:

- 各ソース マシンは、それぞれ 100 GB の 3 個のディスクで構成されています。
- キャッシュ ディスクの測定には、RAID 10 で 10,000 RPM の SAS ドライブが 8 個というベンチマーク用ストレージを使用しました。

### ソースからターゲットへのネットワーク帯域幅
[容量計画ツール](site-recovery-capacity-planner.md)を使用して、初期レプリケーションと差分レプリケーションに必要な帯域幅を必ず計算します。

#### レプリケーションに使用される帯域幅のスロットル

Azure にレプリケートされる VMware トラフィックは、特定のプロセス サーバーを経由します。プロセス サーバー上の Site Recovery レプリケーションに使用できる帯域幅は、次の手順でスロットルできます。

1. メインの管理サーバー、または追加のプロビジョニングされたプロセス サーバーを実行する管理サーバーで、Microsoft Azure Backup MMC スナップインを開きます。既定で、Microsoft Azure Backup のショートカットはデスクトップに作成されます。また、C:\\Program Files\\Microsoft Azure Recovery Services Agent\\bin\\wabadmin にあります。
2. スナップインで **[プロパティの変更]** をクリックします。

	![スロットルの帯域幅](./media/site-recovery-vmware-to-azure-classic/throttle1.png)

3. **[調整]** タブで、Site Recovery のレプリケーションに使用できる帯域幅と適用可能なスケジュールを指定します。

	![スロットルの帯域幅](./media/site-recovery-vmware-to-azure-classic/throttle2.png)

必要に応じて、PowerShell を使用してスロットルを設定することもできます。次に例を示します。

    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)

#### 帯域幅の使用を最大化する
Azure Site Recovery でレプリケーションに使用される帯域幅を増やすには、レジストリ キーを変更する必要があります。

次のキーで、レプリケート時に使用されるレプリケート ディスクごとにスレッド数を制御します。

    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM

 "プロビジョニング超過" 状態のネットワークの場合、このレジストリ キーを既定値から変更する必要があります。サポートされる最大値は 32 です。


詳細な容量計画については、[こちら](site-recovery-capacity-planner.md)をご覧ください。

### 追加のプロセス サーバー

200 台以上のマシンを保護する必要がある場合、または 1 日の変更率が 2 TB を超える場合、負荷を処理するためにサーバーを追加することができます。スケールアウトするには、次の方法があります。

- 管理サーバーの数を増やします。たとえば、2 つの管理サーバーを使用すると、最大 400 台のマシンを保護できます。
- プロセス サーバーを追加し、管理サーバーの代わりに (または管理サーバーに加えて) トラフィックの処理に使用します。

以下の表は、次のようなシナリオの場合を示します。

- 1 つ目の管理サーバーを構成サーバーとしてのみ使用するように設定します。
- 追加のプロセス サーバーを設定します。
- 追加のプロセス サーバーを使用するように保護された仮想マシンを構成します。
- 保護された各ソース マシンには、それぞれ 100 GB の 3 つのディスクが構成されています。

**元の管理サーバー**<br/><br/>(構成サーバー) | **追加のプロセス サーバー**| **キャッシュ ディスク サイズ** | **データの変更率** | **保護されたマシン**
--- | --- | --- | --- | ---
8 vCPU (2 ソケット * 4 コア @ 2.5GHz)、16 GB メモリ | 4 vCPU (2 ソケット * 2 コア @ 2.5GHz)、8 GB メモリ | 300 GB | 250 GB 以下 | 85 台以下のマシンをレプリケートできます。
8 vCPU (2 ソケット * 4 コア @ 2.5GHz)、16 GB メモリ | 8 vCPU (2 ソケット * 4 コア @ 2.5GHz)、12 GB メモリ | 600 GB | 250 GB ～ 1 TB | 85 ～ 150 台のマシンをレプリケートできます。
12 vCPU (2 ソケット * 6 コア @ 2.5GHz)、18 GB メモリ | 12 vCPU (2 ソケット * 6 コア @ 2.5GHz)、24 GB メモリ | 1 TB (テラバイト) | 1 TB ～ 2 TB | 150 ～ 225 台のマシンをレプリケートできます。


サーバーの規模の決め方は、スケールアップ モデルまたはスケールアウト モデルの選択によって変わります。いくつかのハイエンド管理サーバーおよびプロセス サーバーをデプロイしてスケールアップするか、リソースが少ないサーバーを追加してスケールアウトします。たとえば、220 台のマシンを保護する必要がある場合、次のいずれかを実行できます。

- 12 vCPU、18 GB のメモリが搭載された 1 つ目の管理サーバーと、12 vCPU、24 GB のメモリが搭載された追加のプロセス サーバーを構成し、追加のプロセス サーバーのみを使用する保護されたマシンを構成します。
- または、2 つの管理サーバー (2 x 8 vCPU、16 GB RAM) と 2 つの追加プロセス サーバー (135 + 85 (220) 台のマシンを処理する 1 x 8 vCPU と 4vCPU x 1) を構成し、追加のプロセス サーバーのみを使用する保護されたマシンを構成することもできます。


[この手順に従って](#deploy-additional-process-servers)、追加のプロセス サーバーをセットアップします。

## デプロイを開始する前に

次の表に、このシナリオをデプロイする場合の前提条件をまとめています。


### Azure の前提条件

**前提条件** | **詳細**
--- | ---
**Azure アカウント**| [Microsoft Azure](https://azure.microsoft.com/) のアカウントが必要です。アカウントがなくても、[無料試用版](https://azure.microsoft.com/pricing/free-trial/)を使用できます。Site Recovery の価格の詳細については、[こちら](https://azure.microsoft.com/pricing/details/site-recovery/)をご覧ください。
**Azure Storage** | レプリケートしたデータを格納するには Azure ストレージ アカウントが必要になります。レプリケートされたデータは Azure Storage に格納され、フェールオーバーが発生すると、Azure VM はスピンアップされます。<br/><br/>[Standard 地理冗長ストレージ アカウント](../storage/storage-redundancy.md#geo-redundant-storage)が必要です。アカウントは Site Recovery サービスと同じリージョンである必要があり、同じサブスクリプションに関連付けられている必要があります。Premium torage アカウントへのレプリケーションは現在サポートされていないため、使用しないでください。<br/><br/>[新しい Azure ポータル](../storage/storage-create-storage-account.md)を使用して作成したストレージ アカウントを、リソース グループ間で移動することはできません。Azure ストレージについては、[こちら](../storage/storage-introduction.md)をご覧ください。<br/><br/>
**Azure ネットワーク** | フェールオーバーが発生した場合に Azure VM が接続する Azure 仮想ネットワークが必要です。Azure 仮想ネットワークは、Site Recovery コンテナーと同じリージョンに存在する必要があります。<br/><br/>Azure へのフェールオーバー後にフェールバックするには、Azure ネットワークからオンプレミス サイトへの VPN 接続 (または Azure ExpressRoute) を設定する必要があります。


### オンプレミスの前提条件

**前提条件** | **詳細**
--- | ---
**管理サーバー** | 仮想マシンまたは物理サーバーで実行されているオンプレミス Windows 2012 R2 サーバーが必要です。すべてのオンプレミス Site Recovery コンポーネントは、この管理サーバーにインストールされます。<br/><br/>このサーバーは高可用性 VMware VM としてデプロイすることをお勧めします。フェールオーバーしたのが VM か物理サーバーかに関係なく、Azure からオンプレミス サイトへのフェールバック先は常に VMware VM です。VMware VM として管理サーバーを構成していない場合、フェールバック トラフィックを受信するために、別のマスター ターゲット サーバーを VMware VM としてセットアップする必要があります。<br/><br/>サーバーはドメイン コントローラーであってはなりません。<br/><br/>サーバーには静的 IP アドレスを割り当てる必要があります。<br/><br/>サーバーのホスト名は 15 文字以下にする必要があります。<br/><br/>オペレーティング システムのロケールは、英語のみにする必要があります。<br/><br/>管理サーバーからインターネットにアクセスできる必要があります。<br/><br/>サーバーからの発信アクセスが必要です。具体的には、Site Recovery コンポーネントのセットアップ中に実行される HTTP 80 での一時アクセス (MySQL をダウンロードするため)、レプリケーション管理のための HTTPS 443 での継続的な発信アクセス、レプリケーション トラフィックのための HTTPS 9443 (このポートは変更可能) での継続的な発信アクセスです。<br/><br/>管理サーバーから次の URL にアクセスできることを確認します。<br/>- *.hypervrecoverymanager.windowsazure.com<br/>- *.accesscontrol.windows.net<br/>- *.backup.windowsazure.com<br/>- *.blob.core.windows.net<br/>- *.store.core.windows.net<br/>-https://www.msftncsi.com/ncsi.txt<br/>- [ https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi](https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi "https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi")<br/><br/>サーバーに IP アドレスベースのファイアウォール規則が設定されている場合は、その規則で Azure との通信が許可されていることを確認します。[Azure Datacenter の IP の範囲](https://www.microsoft.com/download/details.aspx?id=41653)と HTTPS (433) プロトコルを許可する必要があります。また、サブスクリプションの Azure リージョンと米国西部の IP アドレスの範囲をホワイト リストに追加する必要があります。[https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi](https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi "https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi") という URL は、MySQL のダウンロード用です。
**VMware vCenter/ESXi ホスト**: | VMware 仮想マシンを管理し、最新の更新プログラムが適用された ESX/ESXi バージョン 6.0、5.5、または 5.1 を実行する 1 つ以上の VMware vSphere ESX/ESXi ハイパーバイザーが必要です。<br/><br/> ESXi ホストを管理する VMware vCenter サーバーをデプロイすることをお勧めします。最新の更新プログラムが適用された vCenter バージョン 6.0 または 5.5 を実行している必要があります。<br/><br/>Site Recovery は、クロス vCenter vMotion、仮想ボリューム、ストレージ DRS などの vCenter および vSphere 6.0 の新しい機能をサポートしていません。Site Recovery のサポートは、バージョン 5.5 で提供されていた機能に限定されます。
**保護されたマシン**: | **Azure**<br/><br/>保護対象のマシンは、Azure VM を作成するための [Azure の前提条件](site-recovery-best-practices.md#azure-virtual-machine-requirements)に準拠している必要があります。<br><br/>フェールオーバー後に Azure VM に接続する場合、ローカル ファイアウォールでリモート デスクトップ接続を有効にする必要があります。<br/><br/>保護されたマシンの個々のディスク容量が 1,023 GB 以下である必要があります。VM は最大 64 個のディスク (従って最大 64 TB) に対応できます。1 TB を超えるディスクがある場合は、SQL Server Always On や Oracle Data Guard などのデータベース レプリケーションを使用することを検討してください。<br/><br/>共有ディスク ゲスト クラスターはサポートされていません。クラスター化されたデプロイメントがある場合は、SQL Server Always On や Oracle Data Guard などのデータベース レプリケーションを使用することを検討してください。<br/><br/>Unified Extensible Firmware Interface(UEFI)/Extensible Firmware Interface(EFI) ブートはサポートされていません。<br/><br/>マシン名は 1 ～ 63 文字 (英字、数字、ハイフン) にする必要があります。文字または数字で始まり、文字または数字で終わる必要があります。マシンが保護されたら、Azure 上の名前を変更できます。<br/><br/>**VMware VM**<br/><br>管理サーバー (構成サーバー) に VMware vSphere PowerCLI 6.0 をインストールする必要があります。<br/><br/>保護対象の VMware VM では、VMware ツールがインストールされ、実行されている必要があります。<br/><br/>ソース VM に NIC チーミングがある場合、Azure へのフェールオーバー後に単一の NIC に変換されます。<br/><br/>保護された VM に iSCSI ディスクがある場合、VM が Azure にフェールオーバーしたときに、Site Recovery は保護された VM iSCSI ディスクを VHD ファイルに変換します。Azure VM から iSCSI ターゲットに到達できる場合、iSCSI ターゲットに接続すると、基本的に Azure VM 上の VHD ディスクとソース iSCSI ディスクという 2 つのディスクを確認できます。この場合、フェールオーバーした Azure VM 上の iSCSI ターゲットを切断する必要があります。<br/><br/>Site Recovery に必要な VMware ユーザーのアクセス許可の詳細については、[こちら](#vmware-permissions-for-vcenter-access)をご覧ください。<br/><br/> **Windows Server マシン (VMware VM または物理サーバー)**<br/><br/>サーバーはサポートされている 64 ビット オペレーティング システム (Windows Server 2012 R2、Windows Server 2012、または Windows Server 2008 R2 SP1 以降) を実行している必要があります。<br/><br/>オペレーティング システムは C:\\ ドライブにインストールする必要があります。また、OS ディスクは Windows ベーシック ディスクにする必要があります (OS は Windows ダイナミック ディスクにインストールしないでください)。<br/><br/>Windows Server 2008 R2 マシンでは、.NET Framework 3.5.1 をインストールする必要があります。<br/><br/>Windows サーバーにモビリティ サービスをプッシュ インストールするために管理者アカウント (Windows マシンのローカル管理者である必要があります) を提供する必要があります。提供するアカウントがドメイン アカウントでない場合、ローカル マシンでのリモート ユーザーのアクセス制御を無効にする必要があります。詳細については、[こちら](#install-the-mobility-service-with-push-installation)をご覧ください。<br/><br/>Site Recovery は RDM ディスクを使用する VM をサポートしています。フェールバック時に、1 つ目のソース VM と RDM ディスクを使用できる場合、Site Recovery は RDM ディスクを再利用します。これらを使用できない場合、フェールバック時に Site Recovery は各ディスクの新しい VMDK ファイルを作成します。<br/><br/>**Linux マシン**<br/><br/>サポートされている 64 ビット オペレーティング システム (Red Hat Enterprise Linux 6.7、Centos 6.5、6.6、6.7、Red Hat 互換カーネルまたは Unbreakable Enterprise Kernel リリース 3 (UEK3) を実行する Oracle Enterprise Linux 6.4、6.5、SUSE Linux Enterprise Server 11 SP3) が必要です。<br/><br/>保護されたマシンの /etc/hosts ファイルには、ローカル ホスト名を、すべてのネットワーク アダプターに関連付けられた IP アドレスにマップするエントリが含まれます。<br/><br/>フェールオーバー後に Secure Shell クライアント (ssh) を使用して Linux を実行している Azure 仮想マシンに接続する場合、保護されたマシンの Secure Shell サービスがシステムの起動時に自動的に開始されるように設定します。また、ファイアウォール規則で ssh 接続を許可します。<br/><br/>ファイル システム (EXT3、ETX4、ReiserFS、XFS)、マルチパス ソフトウェア デバイス マッパー (multipath)、ボリューム マネージャー (LVM2) の各ストレージを使用する Linux マシンでのみ保護を有効にすることができます。HP CCISS コントローラー ストレージを使用する物理サーバーはサポートされていません。ReiserFS ファイルシステムは、SUSE Linux Enterprise Server 11 SP3 でのみサポートされています。<br/><br/>Site Recovery は RDM ディスクが搭載された VM をサポートします。Linux のフェールバックの場合、Site Recovery は RDM ディスクを再利用しません。代わりに、対応する各 RDM ディスク用に新しい VMDK ファイルを作成します。

Linux VM のみ - VMware で VM の構成パラメーターに disk.enableUUID=true が設定されていることを確認してください。この行が存在しない場合は追加してください。これは、一貫性のある UUID を VMDK に提供することで適切なマウントが実行されるようにするために必要です。この設定がないと、VM がオンプレミスで使用可能な場合でもフェールバックで完全なダウンロードが発生します。この設定を追加することで、フェールバック中に差分変更のみが転送されることが保証されます。

## ステップ 1: コンテナーの作成

1. [管理ポータル](https://manage.windowsazure.com/)にサインインします。
2. **[Data Services]**、**[Recovery Services]** の順に展開し、**[Site Recovery コンテナー]** をクリックします。
3. **[新規作成]**、**[簡易作成]** の順にクリックします。
4. **[名前]** ボックスに、コンテナーを識別する表示名を入力します。
5. **[リージョン]** ボックスで、コンテナーのリージョンを選択します。サポートされているリージョンを確認するには、「[Azure Site Recovery Pricing Details (Azure Site Recovery の価格の詳細)](https://azure.microsoft.com/pricing/details/site-recovery/)」で利用可能地域を参照してください。
6. **[コンテナーの作成]** をクリックします。![新しいコンテナー](./media/site-recovery-vmware-to-azure-classic/quick-start-create-vault.png)

ステータス バーを確認して、コンテナーが正常に作成されたことを確かめます。**Recovery Services** のメイン ページでは、資格情報コンテナーは **[アクティブ]** と表示されます。

## 手順 2: Azure ネットワークを設定する

フェールオーバー後に Azure VM がネットワークに接続され、オンプレミス サイトへのフェールバックが期待どおりに動作するように Azure ネットワークを設定します。

1. Azure ポータルの **[仮想ネットワークの作成]** に、ネットワーク名、IP アドレスの範囲、サブネット名を指定します。
2. フェールバックを実行する必要がある場合は、ネットワークに VPN/ExpressRoute を追加する必要があります。VPN/ExpressRoute は、フェールオーバー後でもネットワークに追加できます。

Azure ネットワークの詳細については、[こちら](../virtual-network/virtual-networks-overview.md)をご覧ください。

## 手順 3: VMware コンポーネントをインストールする

VMware 仮想マシンをレプリケートする場合、次の VMware コンポーネントを管理サーバーにインストールします。

1. VMware vSphere PowerCLI 6.0 を[ダウンロード](https://developercenter.vmware.com/tool/vsphere_powercli/6.0)してインストールします。
2. サーバーを再起動します。


## 手順 4: コンテナー登録キーをダウンロードする

1. 管理サーバーから Azure の Site Recovery コンソールを開きます。**[Recovery Services]** ページで、コンテナーをクリックして [クイック スタート] ページを開きます。[クイック スタート] は、アイコンを使っていつでも開くことができます。

	![[クイック スタート] アイコン](./media/site-recovery-vmware-to-azure-classic/quick-start-icon.png)

2. **[クイック スタート]** ページで、**[ターゲット リソースの準備]**、**[登録キーをダウンロード]** の順にクリックします。登録ファイルが自動的に生成されます。キーは生成後 5 日間有効です。


## 手順 5: 管理サーバーをインストールする
> [AZURE.TIP] 管理サーバーからこれらの URL にアクセスできるようにします。
>
- *.hypervrecoverymanager.windowsazure.com
- *.accesscontrol.windows.net
- *.backup.windowsazure.com
- *.blob.core.windows.net
- *.store.core.windows.net
- https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi
- https://www.msftncsi.com/ncsi.txt




[AZURE.VIDEO enhanced-vmware-to-azure-setup-registration]

1. **[クイック スタート]** ページで、統合インストール ファイルをサーバーにダウンロードします。

2. インストール ファイルを実行して Site Recovery 統合セットアップ ウィザードでセットアップを開始します。

3.	**[開始する前に]** の **[構成サーバーとプロセス サーバーをインストールする]** を選択します。

	![開始する前に](./media/site-recovery-vmware-to-azure-classic/combined-wiz1.png)
4. **[Third-Party Software License (サードパーティ製ソフトウェア ライセンス)]** で、**[同意する]** をクリックして MySQL をダウンロードし、インストールします。

	![サード パーティ製ソフトウェア](./media/site-recovery-vmware-to-azure-classic/combined-wiz105.PNG)

5. **[登録]** で、コンテナーからダウンロードした登録キーを参照して選択します。

	![登録](./media/site-recovery-vmware-to-azure-classic/combined-wiz3.png)

6. **[インターネット設定]** で、構成サーバーで実行されているプロバイダーがインターネット経由で Azure Site Recovery に接続する方法を指定します。

	- マシンで現在セットアップされているプロキシを使用して接続する場合は、**[Connect with existing proxy settings (既存のプロキシ設定を使用して接続する)]** を選択します。
	- プロバイダーから直接接続するようにするには、**[Connect directly without a proxy (プロキシなしで直接接続する)]** を選択します。
	- 既存のプロキシで認証が必要な場合、またはプロバイダー接続にカスタム プロキシを使用する場合は、**[Connect with custom proxy settings (カスタム プロキシ設定を使用して接続する)]** を選択します。
		- カスタム プロキシを使用する場合、アドレス、ポート、資格情報を指定する必要があります。
		- プロキシを使用する場合は、次の URL をあらかじめ許可しておく必要があります。
			- *.hypervrecoverymanager.windowsazure.com;
			- *.accesscontrol.windows.net;
			- *.backup.windowsazure.com;
			- *.blob.core.windows.net;
			- *.store.core.windows.net
			

	![ファイアウォール](./media/site-recovery-vmware-to-azure-classic/combined-wiz4.png)

7. **[前提条件の確認]** では、インストールを実行できることを確認するためのチェックが実行されます。

	
	![前提条件](./media/site-recovery-vmware-to-azure-classic/combined-wiz5.png)

	 **グローバル時刻の同期チェック**に関する警告が表示された場合は、システム クロックの時刻 (**[日付と時刻]** 設定) がタイム ゾーンと同じであることを確認します。

 	![TimeSyncIssue](./media/site-recovery-vmware-to-azure-classic/time-sync-issue.png)

8. **[MySQL Configuration (MySQL の構成)]** で、インストールする MySQL サーバー インスタンスにログオンするための資格情報を作成します。

	![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz6.png)

9. **[環境の詳細]** で、VMware VM をレプリケートするかどうかを選択します。レプリケートする場合、PowerCLI 6.0 がインストールされているかどうかがチェックされます。

	![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz7.png)

10. **[インストール場所]** で、バイナリをインストールし、キャッシュを格納する場所を選択します。使用可能な記憶域が 5 GB 以上あるドライブを選択できますが、600 GB 以上の空き領域があるキャッシュ ドライブを使用することをお勧めします。

	![インストール場所](./media/site-recovery-vmware-to-azure-classic/combined-wiz8.png)

11. **[ネットワークの選択]** で、構成サーバーがレプリケーション データを送受信するリスナー (ネットワーク アダプターと SSL ポート) を指定します。既定のポート (9443) は変更できます。このポートに加え、ポート 443 がレプリケーション操作を統制する Web サーバーによって使用されます。レプリケーション トラフィックの受信には 443 を使用しないでください。


	![ネットワークの選択](./media/site-recovery-vmware-to-azure-classic/combined-wiz9.png)



12.  **[概要]** で情報を確認し、**[インストール]** をクリックします。インストールが完了すると、パスフレーズが生成されます。このパスフレーズは、レプリケーションを有効にするときに必要になるので、コピーしてセキュリティで保護された場所に保管してください。

	![概要](./media/site-recovery-vmware-to-azure-classic/combined-wiz10.png)



13.  **[概要]** の情報を確認します。

	![概要](./media/site-recovery-vmware-to-azure-classic/combined-wiz10.png)

>[AZURE.WARNING] Microsoft Azure Recovery Service Agent のプロキシをセットアップする必要があります。インストールが完了したら、Windows の [スタート] メニューから「Microsoft Azure Recovery Services シェル」という名前のアプリケーションを起動します。開いたコマンド ウィンドウで、次のコマンドを実行して、プロキシ サーバー設定をセットアップします。
>
	$pwd = ConvertTo-SecureString -String ProxyUserPassword
	Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumb – ProxyUserName domain\username -ProxyPassword $pwd
	net stop obengine
	net start obengine



### コマンド ラインからセットアップを実行する

次のように、コマンド ラインから統合ウィザードを実行することもできます。

    UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

各値の説明:

- /ServerMode: 必須。構成サーバーとプロセス サーバーをインストールするか、(追加のプロセス サーバーのインストールに使用する) プロセス サーバーのみをインストールするかを指定します。入力値: CS、PS。
- InstallDrive: 必須。ポートがインストールされているフォルダーを指定します。
- /MySQLCredFilePath:必須。MySQL サーバーの資格情報が格納されているファイルのパスを指定します。ファイルを作成するテンプレートを取得します。
- /VaultCredFilePath:必須。コンテナーの資格情報ファイルの場所。
- /EnvType:必須。インストールの種類。値: VMware、NonVMware
- /PSIP と /CSIP:必須。プロセス サーバーと構成サーバーの IP アドレス。
- /PassphraseFilePath:必須。パスフレーズ ファイルの場所。
- /ByPassProxy:省略可能。管理サーバーがプロキシを介さずに Azure に接続することを指定します。
- /ProxySettingsFilePath:省略可能。カスタム プロキシの設定 (認証が必要なサーバー上の既定のプロキシ、またはカスタム プロキシ) を指定します。




## 手順 6: vCenter サーバーの資格情報をセットアップする

> [AZURE.VIDEO enhanced-vmware-to-azure-discovery]

プロセス サーバーは、vCenter サーバーで管理されている VMware VM を自動的に検出できます。自動検出の場合、Site Recovery には、vCenter サーバーにアクセスできるアカウントと資格情報が必要です。物理サーバーのみをレプリケートする場合、これは関係ありません。

これを行うには、次の手順を実行します。

1. vCenter サーバーで、[必要なアクセス許可](#vmware-permissions-for-vcenter-access)を持つロール (**Azure\_Site\_Recovery**) を vCenter レベルに作成します。
2. **Azure\_Site\_Recovery** ロールを vCenter ユーザーに割り当てます。

	>[AZURE.NOTE] 読み取り専用ロールを持つ vCenter ユーザー アカウントは、保護されたソース マシンをシャットダウンすることなくフェールオーバーを実行できます。保護されたソース マシンをシャットダウンする場合は、Azure\_Site\_Recovery ロールが必要です。VMware の VM を Azure に移行するだけで、フェールバックする必要がない場合、読み取り専用のロールで十分です。

3. アカウントを追加するには、**cspsconfigtool** を開きます。cspsconfigtool はデスクトップにショートカットがあり、[インストール場所]\\home\\svsystems\\bin フォルダーに保存されています。
2. **[アカウントの管理]** タブの **[アカウントの追加]** をクリックします。

	![Add account](./media/site-recovery-vmware-to-azure-classic/credentials1.png)

3. **[アカウントの詳細]** で、vCenter サーバーへのアクセスに使用できる資格情報を追加します。アカウント名がポータルに表示されるまでに、15 分以上かかることがあります。すぐに更新するには、**[構成サーバー]** タブの [更新] をクリックします。

	![詳細](./media/site-recovery-vmware-to-azure-classic/credentials2.png)

## 手順 7: vCenter サーバーと ESXi ホストを追加する

VMware VM をレプリケートする場合、vCenter サーバー (または ESXi ホスト) を追加する必要があります。

1. **[サーバー]**、**[構成サーバー]** タブの順にクリックし、構成サーバーを選択して、**[vCenter サーバーの追加]** を選択します。

	![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter1.png)

2. vCenter サーバーまたは ESXi ホストの詳細、前の手順で vCenter サーバーへのアクセスに指定したアカウント名、vCenter サーバーが管理している VMware VM の検出に使用されるプロセス サーバーを追加します。vCenter サーバーまたは ESXi ホストは、プロセス サーバーがインストールされているサーバーと同じネットワークに配置する必要があります。

	>[AZURE.NOTE] vCenter またはホスト サーバーに管理者特権がないアカウントを使用して vCenter サーバーまたは ESXi ホストを追加する場合、その vCenter または ESXi アカウントについて、Datacenter、Datastore、Folder、Jost、Network、Resource、Virtual machine、vSphere Distributed Switch の特権を有効にします。さらに、vCenter サーバーは Storage ビューの特権が必要です。

	![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter2.png)

3. 検出が完了すると、**[構成サーバー]** タブに vCenter サーバーの一覧が表示されます。

	![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter3.png)


## 手順 8: 保護グループを作成する

> [AZURE.VIDEO enhanced-vmware-to-azure-protection]


保護グループは、同じ保護設定を使用して保護する仮想マシンまたは物理サーバーの論理グループです。保護設定を保護グループに適用すると、これらの設定はグループに追加するすべての仮想マシンおよび物理コンピューターに適用されます。

1. **[保護された項目]**、**[保護グループ]** の順に開き、保護グループを追加するためにクリックします。

	![保護グループの作成](./media/site-recovery-vmware-to-azure-classic/protection-groups1.png)

2. **[保護グループ設定の指定]** ページで、グループの名前を指定し、**[ソース]** で、グループを作成する構成サーバーを選択します。**[ターゲット]** は Azure です。

	![保護グループの設定](./media/site-recovery-vmware-to-azure-classic/protection-groups2.png)

3. **[レプリケーションの設定を指定する]** ページで、グループのすべてのマシンで使用するレプリケーションの設定を構成します。

	![保護グループのレプリケーション](./media/site-recovery-vmware-to-azure-classic/protection-groups3.png)

	- **[複数 VM の整合性]**: これをオンにすると、保護グループに属する複数のアプリケーションで共有されるアプリケーション整合性復旧ポイントが作成されます。この設定は、保護グループ内のすべてのマシンが同じワークロードを実行している場合に最も適しています。すべてのマシンは同じデータ ポイントに復旧されます。VMware VM または Windows/Linux 物理サーバーをレプリケートする場合に使用できます。
	- **[RPO しきい値]**: RPO を設定します。構成された RPO しきい値を継続的なデータ保護レプリケーションが超過すると、警告が生成されます。
	- **[復旧ポイントのリテンション期間]**: リテンション期間を指定します。保護されたマシンはこのウィンドウ内のどのポイントにも復旧できます。
	- **[アプリケーション整合性スナップショットの頻度]**: アプリケーション整合性スナップショットを含む復旧ポイントを作成する頻度を指定します。

チェックマークをオンにすると、指定した名前の保護グループが作成されます。さらに、2 つ目の保護グループは、<protection-group-name-Failback> という名前で作成されます。Azure へのフェールオーバー後にオンプレミス サイトにフェールバックする場合、この保護グループが使用されます。保護グループの作成は、**[保護された項目]** ページで監視することができます。

## 手順 9: モビリティ サービスをインストールする

仮想マシンと物理サーバーの保護を有効にするための最初の手順は、モビリティ サービスのインストールです。次の 2 つの方法で行います。

- プロセス サーバーから各マシンにサービスを自動的にプッシュしてインストールします。マシンを保護グループに追加し、適切なバージョンのモビリティ サービスを既に実行している場合は、プッシュ インストールは実行されません。
- WSUS や System Center Configuration Manager などのエンタープライズ プッシュ手法を使用してサービスを自動的にインストールしてください。ただし、インストール前に管理サーバーをセットアップしておきます。
- 保護する各マシンに手動でインストールします。ただし、インストール前に管理サーバーをセットアップしておきます。


### プッシュ インストールでモビリティ サービスをインストールする

保護グループにマシンを追加すると、プロセス サーバーによってモビリティ サービスが自動的にプッシュされ、各マシンにインストールされます。


#### Windows マシンで自動プッシュを準備する

プロセス サーバーによってモビリティ サービスが自動的にインストールされるように Windows マシンを準備する方法について説明します。

1.  プロセス サーバーから使用できる、マシンにアクセスするためのアカウントを作成します。このアカウントには管理者特権 (ローカルまたはドメイン) が必要です。これらの資格情報は、モビリティ サービスのプッシュ インストールのためにのみ使用されます。

	>[AZURE.NOTE] ドメイン アカウントを使用していない場合、ローカル マシンでのリモート ユーザーのアクセス制御を無効にする必要があります。無効にするには、レジストリ エディターで HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Policies\\System に値が 1 の DWORD エントリの LocalAccountTokenFilterPolicy を追加します。CLI からレジストリ エントリを追加するには、コマンドを開くか、PowerShell を使用して、「**`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**」と入力します。

2.  保護対象のマシンの Windows ファイアウォールで、**[ファイアウォールを介したアプリまたは機能を許可する]** を選択し、**[ファイルとプリンターの共有]** と **[Windows Management Instrumentation]** を有効にします。マシンがドメインに属している場合は、GPO を使用してファイアウォール ポリシーを構成できます。

	![ファイアウォールの設定](./media/site-recovery-vmware-to-azure-classic/mobility1.png)

2. 作成したアカウントを追加します。

	- **cspsconfigtool** を開きます。cspsconfigtool はデスクトップにショートカットがあり、[インストール場所]\\home\\svsystems\\bin フォルダーに保存されています。
	- **[アカウントの管理]** タブの **[アカウントの追加]** をクリックします。
	- 作成したアカウントを追加します。アカウントの追加後は、保護グループにマシンを追加するときに資格情報を入力する必要があります。


#### Linux サーバーの自動プッシュを準備する

1.	「[オンプレミスの前提条件](#on-premises-prerequisites)」で説明したとおり、保護対象の Linux マシンがサポートされていることを確認します。また、保護するマシンと、プロセス サーバーを実行する管理サーバー間にネットワーク接続があることを確認します。

2.	プロセス サーバーから使用できる、マシンにアクセスするためのアカウントを作成します。アカウントは、ソース Linux サーバーの root ユーザーである必要があります。これらの資格情報は、モビリティ サービスのプッシュ インストールのためにのみ使用されます。

	- **cspsconfigtool** を開きます。cspsconfigtool はデスクトップにショートカットがあり、[インストール場所]\\home\\svsystems\\bin フォルダーに保存されています。
	- **[アカウントの管理]** タブの **[アカウントの追加]** をクリックします。
	- 作成したアカウントを追加します。アカウントの追加後は、保護グループにマシンを追加するときに資格情報を入力する必要があります。

3.	ソース Linux サーバーの /etc/hosts ファイルに、ローカル ホスト名を、すべてのネットワーク アダプターに関連付けられた IP アドレスにマップするエントリが含まれることを確認します。
4.	保護するマシンに最新の openssh、openssh-server、openssl の各パッケージをインストールします。
5.	SSH がポート 22 で有効であり実行中であることを確認します。
6.	以下の手順で、sshd\_config ファイルで SFTP サブシステムとパスワード認証を有効にします。

	- root としてログインします。
	- /etc/ssh/sshd\_config ファイルで、PasswordAuthentication で始まる行を見つけます。
	- この行のコメントを解除し、値 **no** を **yes** に変更します。
	- **Subsystem** で始まる行を見つけ、その行のコメントを解除します。

		![Linux](./media/site-recovery-vmware-to-azure-classic/mobility2.png)


### モビリティ サービスを手動でインストールする

インストーラーは C:\\Program Files (x86)\\Microsoft Azure Site Recovery\\home\\svsystems\\pushinstallsvc\\repository にあります。

ソース オペレーティング システム | モビリティ サービスのインストール ファイル
--- | ---
Windows Server (64 ビットのみ) | Microsoft-ASR\_UA\_9.*.0.0_Windows_* release.exe
CentOS 6.4、6.5、6.6 (64 ビットのみ) | Microsoft-ASR\_UA\_9.*.0.0\_RHEL6-64\_*release.tar.gz
SUSE Linux Enterprise Server 11 SP3 (64 ビットのみ) | Microsoft-ASR\_UA\_9.*.0.0\_SLES11-SP3-64\_*release.tar.gz
Oracle Enterprise Linux 6.4、6.5 (64 ビットのみ) | Microsoft-ASR\_UA\_9.*.0.0\_OL6-64\_*release.tar.gz


#### Windows サーバーに手動でインストールする


1. 関連するインストーラーをダウンロードして実行します。
2. **[開始する前に]** で **[モビリティ サービス]** を選択します。

	![モビリティ サービス](./media/site-recovery-vmware-to-azure-classic/mobility3.png)

3. **[構成サーバーの詳細]** で、管理サーバーの IP アドレスと、管理サーバーのコンポーネントをインストールしたときに生成されたパスフレーズを指定します。パスフレーズを取得するには、管理サーバーで **<SiteRecoveryInstallationFolder>\\home\\sysystems\\bin\\genpassphrase.exe –n** を実行します。

	![モビリティ サービス](./media/site-recovery-vmware-to-azure-classic/mobility6.png)

4. **[インストール場所]** は既定の場所のままにして、**[次へ]** をクリックしてインストールを開始します。
5. **[インストールの進行状況]** でインストールを監視し、要求されたらマシンを再起動します。

次のようにコマンド ラインからインストールすることもできます。

UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <インストール ディレクトリ>] [/CSIP <登録する CS の IP アドレス>] [/PassphraseFilePath <パスフレーズ ファイルのパス>] [/LogFilePath <ログ ファイルのパス>]

各値の説明:

- /Role: 必須。モビリティ サービスをインストールするかどうかを指定します。
- /InstallLocation: 必須。サービスをインストールする場所を指定します。
- /PassphraseFilePath: 必須。構成サーバーのパスフレーズを指定します。
- /LogFilePath: 必須。ログ セットアップ ファイルの場所を指定します。

#### モビリティ サービスを手動でアンインストールする

モビリティ サービスは、コントロール パネルの [プログラムの追加と削除] を使用するか、コマンド ラインを使用してアンインストールできます。

コマンド ラインを使用して、モビリティ サービスをアンインストールするコマンドは次のとおりです。

	MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1}

#### 管理サーバーの IP アドレスを変更する

ウィザードの実行後は、次の手順で管理サーバーの IP アドレスを変更できます。

1. (デスクトップにある) ファイル hostconfig.exe を開きます。
2. **[グローバル]** タブで、管理サーバーの IP アドレスを変更できます。

	>[AZURE.NOTE] 管理サーバーの IP アドレスのみを変更する必要があります。管理サーバー通信のポート番号は 443 にして、[HTTPS を使用する] を有効なままにしておく必要があります。パスフレーズは変更しないでください。

	![管理サーバーの IP アドレス](./media/site-recovery-vmware-to-azure-classic/host-config.png)

#### Linux サーバーに手動でインストールする:

1. 上の表に基づいて適切な tar アーカイブを保護対象の Linux マシンにコピーします。
2. シェル プログラムを開き、`tar -xvzf Microsoft-ASR_UA_8.5.0.0*` を実行して、zip 形式の tar アーカイブをローカル パスに抽出します。
3. tar アーカイブの内容を抽出したローカル ディレクトリに passphrase.txt ファイルを作成します。このファイルを作成するには、管理サーバーの C:\\ProgramData\\Microsoft Azure Site Recovery\\private\\connection.passphrase のパスフレーズをコピーし、シェルで *`echo <passphrase> >passphrase.txt`* を実行して passphrase.txt に保存します。
4. 「*`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*」と入力して、モビリティ サービスをインストールします。
5. 管理サーバーの内部 IP アドレスを指定し、ポート 443 を選択します。

**次のようにコマンド ラインからインストールすることもできます**。

1. 管理サーバーの C:\\Program Files (x86)\\InMage Systems\\private\\connection のパスフレーズをコピーし、管理サーバーに "passphrase.txt" というファイル名で保存します。次のコマンドを実行します。この例で、管理サーバーの IP アドレスは 104.40.75.37、HTTPS ポートは 443 です。

運用サーバーにインストールするには:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

マスター ターゲット サーバーにインストールするには:


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt


## 手順 10: マシンの保護を有効にする

保護を有効にするには、仮想マシンと物理サーバーを保護グループに追加します。VMware 仮想マシンを保護する場合、開始前に次の点に注意してください。

- VMware VM は 15 分間隔で検出されます。検出後に Site Recovery ポータルに表示されるまでに 15 分以上かかることがあります。
- 仮想マシンでの環境の変化 (VMware ツールのインストールなど) も、Site Recovery で更新されるまでに 15 分以上かかることがあります。
- **[構成サーバー]** タブの vCenter サーバー/ESXi ホストの **[前回のアクセス]** フィールドで VMware VM の最終検出時刻を確認できます。
- 保護グループを既に作成済みで、その後に vCenter サーバーまたは ESXi ホストを追加した場合、Azure Site Recovery ポータルが更新されて仮想マシンが **[Add machines to a protection group (保護グループへのマシンの追加)]** ダイアログのリストに表示されるまでに 15 分以上かかることがあります。
- マシンを保護グループに追加したら、定期検出を待たずにすぐに先に進みたい場合は、構成サーバーを強調表示にして (クリックしないでください)、**[更新]** ボタンをクリックします。

さらに、次の点に注意してください。

- ワークロードが反映されるように、保護グループを構成することをお勧めします。たとえば、特定のアプリケーションを実行するマシンを同じグループに追加します。
- モビリティ サービスがまだインストールされていない場合、保護グループにマシンを追加すると、プロセス サーバーはモビリティ サービスをプッシュしてインストールします。前述の手順のように、プッシュ メカニズムを準備する必要があります。


次の手順で保護グループにマシンを追加します。

1. **[保護された項目]**、**[保護グループ]**、**[コンピューター]**、[コンピューターの追加] の順にクリックします
2. **[仮想マシンの選択]** で VMware 仮想マシンを保護している場合は、仮想マシンを管理している vCenter サーバー (または仮想マシンが実行している EXSi ホスト) を選択し、マシンを選択します。

	![保護を有効にする](./media/site-recovery-vmware-to-azure-classic/enable-protection2.png)

3.  **[仮想マシンの選択]** で物理サーバーを保護している場合は、**[物理マシンの追加]** ウィザードで IP アドレスとフレンドリ名を指定します。次に、オペレーティング システム ファミリを選択します。

	![保護を有効にする](./media/site-recovery-vmware-to-azure-classic/enable-protection1.png)

4. **[ターゲット リソースの指定]** で、レプリケーションを使用しているストレージ アカウントを選択し、その設定をすべてのワークロードに使用するかどうかを選択します。現在、Premium Storage アカウントはサポートされていません。

	>[AZURE.NOTE] [新しい Azure ポータル](../storage/storage-create-storage-account.md)を使用して作成したストレージ アカウントをリソース グループ間で移動する操作はサポートされていません。

	![保護を有効にする](./media/site-recovery-vmware-to-azure-classic/enable-protection3.png)

5. **[アカウントの指定]** で、モビリティ サービスの自動インストールに使用するように[構成した](#install-the-mobility-service-with-push-installation)アカウントを選択します。

	![保護を有効にする](./media/site-recovery-vmware-to-azure-classic/enable-protection4.png)

6. チェック マークをクリックして、保護グループへのマシンの追加を完了し、各マシンの初期レプリケーションを開始します。

	>[AZURE.NOTE] プッシュ インストールを準備した場合、保護グループに追加されるときにまだモビリティ サービスがインストールされていなければ、自動的にマシンにインストールされます。サービスのインストール後に、保護ジョブが開始され、失敗します。失敗後は、モビリティ サービスがインストールされている各マシンを手動で再起動する必要があります。再起動後に、保護ジョブが再び開始され、最初のレプリケーションが実行されます。

**[ジョブ]** ページで状態を監視できます。

![保護を有効にする](./media/site-recovery-vmware-to-azure-classic/enable-protection5.png)

また、保護の状態を監視するには、**[保護された項目]**、[<保護グループ名>]、**[仮想マシン]** の順に選択します。最初のレプリケーションが完了し、データが同期されると、マシンの状態が **[保護]** に変わります。

![保護を有効にする](./media/site-recovery-vmware-to-azure-classic/enable-protection6.png)


## 手順 11: 保護されたマシンのプロパティを設定する

1. マシンの状態が **[保護されています]** になると、マシンのフェールオーバーのプロパティを構成できます。保護グループの詳細で、マシンを選択し、**[構成]** タブを開きます。
2. Site Recovery は、自動的に Azure VM のプロパティを提案し、オンプレミス ネットワーク設定を検出します。

	![仮想マシンのプロパティの設定](./media/site-recovery-vmware-to-azure-classic/vm-properties1.png)

3. 次の設定を変更できます。

	-  **Azure VM の名前**: フェールオーバー後に Azure 内のマシンに付けられる名前です。名前は Azure の要件に準拠する必要があります。

	-  **Azure VM のサイズ**: ネットワーク アダプターの数は、ターゲット仮想マシンに指定したサイズによって異なります。サイズとアダプターの詳細については、[こちら](../virtual-machines/virtual-machines-linux-sizes.md/#size-tables)をご覧ください。以下の点に注意してください。

		- 仮想マシンのサイズを変更し、設定を保存すると、次回 **[構成]** タブを開いたときにネットワーク アダプターの数が変更されます。ターゲット仮想マシンのネットワーク アダプターの数は、ソース仮想マシン上のネットワーク アダプターの最小数と、選択した仮想マシンのサイズでサポートされているネットワーク アダプターの最大数です。
			- ソース マシン上のネットワーク アダプターの数が、ターゲット マシンのサイズに許可されているアダプターの数以下の場合、ターゲットのアダプターの数は、ソースと同じになります。
			- ソース仮想マシン用のアダプターの数が、ターゲットのサイズに許可されている数を超える場合は、ターゲットの最大サイズが使用されます。
			- たとえば、ソース マシンに 2 つのネットワーク アダプターがあり、ターゲット マシンのサイズが 4 つをサポートしている場合は、ターゲット マシンのアダプターの数は、2 つになります。ソース マシンに 2 つのアダプターがあるが、サポートされているターゲット サイズで 1 つしかサポートしていない場合、ターゲット マシンのアダプターの数は 1 つだけになります。
		- 仮想マシンに複数のネットワーク アダプターがある場合は、すべてのアダプターが、同じ Azure ネットワークに接続する必要があります。
	- **Azure ネットワーク**: フェールオーバー後に Azure VM を接続する Azure ネットワークを指定する必要があります。指定しない場合、Azure VM はどのネットワークにも接続されません。さらに、Azure からオンプレミス サイトにフェールバックする場合、Azure ネットワークを指定する必要があります。フェールバックには、Azure ネットワークとオンプレミス ネットワーク間に VPN 接続が必要です。
	- **Azure IP アドレス/サブネット**: 各ネットワーク アダプターについて、Azure VM が接続するサブネットを選択します。以下の点に注意してください。
		- 静的 IP アドレスを使用するようにソース マシンのネットワーク アダプターを構成する場合、Azure VM に静的 IP アドレスを指定できます。静的 IP アドレスを指定しない場合、使用可能な IP アドレスが割り当てられます。ターゲット IP アドレスを指定し、Azure の別の VM で既に使用されている場合、フェールオーバーは失敗します。DHCP を使用するようにソース マシンのネットワーク アダプターを構成すると、Azure の設定として DHCP が表示されます。

## 手順 12: 復旧計画を作成し、フェールオーバーを実行する

> [AZURE.VIDEO enhanced-vmware-to-azure-failover]

単一のマシンでフェールオーバーを実行するか、同じタスクまたは同じワークロードを実行する複数の仮想マシンをフェールオーバーすることができます。同時に複数のマシンをフェールオーバーするには、復旧計画に追加します。

### 復旧計画の作成

1. **[復旧計画]** ページで、**[Add Recovery Plan (復旧計画の追加)]** をクリックして復旧計画を追加します。計画の詳細を指定し、ターゲットに **Azure** を選択します。

	![復旧計画の構成](./media/site-recovery-vmware-to-azure-classic/recovery-plan1.png)

2. **[仮想マシンの選択]** で、保護グループを選択してから、復旧計画に追加するグループ内のマシンを選択します。

	![仮想マシンを追加](./media/site-recovery-vmware-to-azure-classic/recovery-plan2.png)

計画をカスタマイズして、復旧計画でフェールオーバーするマシンのグループを作成し、その実行順序を指定できます。手動の操作のためのスクリプトとプロンプトを追加することもできます。スクリプトは、手動または [Azure Automation Runbook](site-recovery-runbook-automation.md) を使用して作成できます。復旧計画のカスタマイズについては、[こちら](site-recovery-create-recovery-plans.md)をご覧ください。

## フェールオーバーの実行

フェールオーバーを実行する前に、次の点に注意してください。


- 管理サーバーが実行中で使用できることを確認します。使用できない場合、フェールオーバーは失敗します。
- 計画されていないフェールオーバーを実行する場合、次の点に注意してください。

	- 可能であれば、プライマリ マシンをシャットダウンしてから、計画されていないフェールオーバーを実行します。こうすることで、ソース マシンとレプリカ マシンが同時に実行されないように確保できます。VMware VM をレプリケートする場合、計画されていないフェールオーバーを実行するときに、Site Recovery が可能な限りソース マシンをシャットダウンするように指定できます。プライマリ サイトの状態によっては、シャットダウンが動作しない場合があります。物理サーバーをレプリケートする場合、Site Recovery にこのオプションは表示されません。
	- 計画されていないフェールオーバーを実行すると、プライマリ マシンのデータ レプリケーションが停止され、計画されていないフェールオーバーの開始後に、データの差分は転送されなくなります。

- フェールオーバー後に Azure のレプリカ仮想マシンに接続する場合、フェールオーバーの実行前にソース マシンでリモート デスクトップ接続を有効にして、ファイアウォール経由の RDP 接続を許可します。また、フェールオーバー後に、Azure 仮想マシンのパブリック エンドポイントで RDP を許可する必要があります。フェールオーバー後に RDP が動作するように、この[ベスト プラクティス](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)に従ってください。

>[AZURE.NOTE] Azure へのフェールオーバーを実行するときに最適なパフォーマンスを得るには、保護されたマシンに Azure エージェントをインストールしておきます。これは、ブートの速度を上げ、問題が発生した場合の診断にも役立ちます。Linux エージェントは[こちら](https://github.com/Azure/WALinuxAgent)から、Windows エージェントは[こちら](http://go.microsoft.com/fwlink/?LinkID=394789)から入手できます。

### テスト フェールオーバーの実行

運用環境に影響を与えず、レプリケーションが通常どおりに続行するように、テスト フェールオーバーを実行し、分離されたネットワークでフェールオーバーと復旧のプロセスをシミュレートします。ソースでテスト フェールオーバーを開始します。次のようにいくつかの実行方法があります。

- **Azure ネットワークを指定しない**: ネットワークを指定せずにテスト フェールオーバーを実行すると、仮想マシンが開始され、Azure に正しく表示されていることがテストで確認されます。フェールオーバー後、仮想マシンは Azure ネットワークに接続しません。
- **Azure ネットワークを指定する**: この種のフェールオーバーでは、レプリケーション環境全体が正しく動作することと、Azure 仮想マシンが指定したネットワークに接続されることを確認します。


1. **[復旧計画]** ページで計画を選択し、**[テスト フェールオーバー]** をクリックします。

	![仮想マシンを追加](./media/site-recovery-vmware-to-azure-classic/test-failover1.png)

2. **[テスト フェールオーバーの確認]** で **[なし]** を選択して、テスト フェールオーバーに Azure ネットワークを使用しないことを示します。または、フェールオーバー後にテスト VM を接続するネットワークを選択します。チェック マークをクリックしてフェールオーバーを開始します。

	![仮想マシンを追加](./media/site-recovery-vmware-to-azure-classic/test-failover2.png)

3. **[ジョブ]** タブで、フェールオーバーの進行状況を監視します。

	![仮想マシンを追加](./media/site-recovery-vmware-to-azure-classic/test-failover3.png)

4. フェールオーバーの完了後は、Azure ポータルの **[仮想マシン]** にレプリカの Azure マシンも表示されるようになります。Azure VM への RDP 接続を開始する場合、VM エンドポイントでポート 3389 を開く必要があります。

5. 手順を終了し、フェールオーバーがテストの完了フェーズに達したら、[テストの完了] をクリックして終了します。[メモ] を使用して、テスト フェールオーバーに関連する観察結果をすべて記録し、保存します。

6. **[テスト フェールオーバーが完了しました]** をクリックすると、テスト環境は自動的にクリーンアップされます。この処理が完了すると、テスト フェールオーバーには **[完了]** という状態が表示されます。テスト フェールオーバー時に自動的に作成されたすべての要素または VM は削除されます。テスト フェールオーバーの実行時間が 2 週間を超えると、強制的に完了します。



### 計画されていないフェールオーバーの実行

計画されていないフェールオーバーは Azure から開始します。計画されていないフェールオーバーは、プライマリ サイトが使用できない場合でも実行できます。


1. **[復旧計画]** ページで計画を選択し、**[フェールオーバー]**、**[計画されていないフェールオーバー]** の順にクリックします。

	![仮想マシンを追加](./media/site-recovery-vmware-to-azure-classic/unplanned-failover1.png)

2. VMware 仮想マシンをレプリケートする場合、オンプレミス VM のシャットダウンを試行することができます。これはベスト エフォートの処理であり、試行が成功するかどうかに関係なく、フェールオーバーは継続されます。成功しなかった場合は、**[ジョブ]** タブの **[計画されていないフェールオーバー ジョブ]** にエラーの詳細が表示されます。

	![仮想マシンを追加](./media/site-recovery-vmware-to-azure-classic/unplanned-failover2.png)

	>[AZURE.NOTE] 物理サーバーをレプリケートする場合、このオプションは使用できません。可能な場合、手動でシャットダウンを試す必要があります。

3. **[フェールオーバーの確認]** で、フェールオーバーの方向 (Azure に) を確認し、フェールオーバーに使用する復旧ポイントを選択します。レプリケーション プロパティを構成した際に複数 VM を有効にした場合、最新のアプリケーションまたはクラッシュ整合性復旧ポイントに復旧できます。**[カスタム復旧ポイント]** を選択して、以前のポイントまで復旧することもできます。チェック マークをクリックしてフェールオーバーを開始します。

	![仮想マシンを追加](./media/site-recovery-vmware-to-azure-classic/unplanned-failover3.png)

3. 計画されていないフェールオーバー ジョブが完了するまで待ちます。**[ジョブ]** タブで、フェールオーバーの進行状況を監視できます。計画されていないフェールオーバーの実行中にエラーが発生しても、復旧計画は最後まで実行されることに注意してください。Azure ポータルの [Virtual Machines] にレプリカの Azure マシンも表示されるようになります。

### フェールオーバー後にレプリケートされた Azure Virtual Machines に接続する

フェールオーバー後に Azure のレプリケートされた仮想マシンに接続するには、次の手順を実行する必要があります。

1. プライマリ マシンでリモート デスクトップ接続を有効にする必要があります。
2. プライマリ マシンの Windows ファイアウォールで RDP を許可します。
3. フェールオーバー後に、Azure 仮想マシンのパブリック エンドポイントに RDP を追加する必要があります。

このセットアップの詳細については、[こちら](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)をご覧ください。


## 追加のプロセス サーバーをデプロイする

デプロイをスケールアウトしてソース マシンが 200 台を超える場合、または 1 日の合計変動率が 2 TB を超える場合、トラフィック ボリュームに対応するためにプロセス サーバーを追加する必要があります。追加のプロセス サーバーをセットアップする場合は、[追加のプロセス サーバー](#additional-process-servers)の要件を確認し、指示に従ってプロセス サーバーをセットアップします。サーバーをセットアップした後は、そのサーバーを使用するソース マシンを構成できます。

### 追加のプロセス サーバーをセットアップする

追加のプロセス サーバーは、次のようにセットアップします。

- 統合ウィザードを実行して、プロセス サーバーのみとして管理サーバーを構成します。
- 新しいプロセス サーバーのみを使用してデータ レプリケーションを管理するには、保護されたマシンを移行して実行する必要があります。

### プロセス サーバーをインストールする

1. [クイック スタート] ページで、Site Recovery コンポーネント インストールの統合インストール ファイルをダウンロードします。セットアップを実行します。
2. **[開始する前に]** で **[Add additional process servers to scale out deployment (デプロイをスケールアウトするためにプロセス サーバーを追加する)]** を選択します。

	![プロセス サーバーの追加](./media/site-recovery-vmware-to-azure-classic/add-ps1.png)

3. 1 つ目の管理サーバーを[セットアップ](#step-5-install-the-management-server)したのと同じ方法でウィザードを完了します。

4. **[構成サーバーの詳細]** で、構成サーバーをインストールした 1 つ目の管理サーバーの IP アドレスと、パスフレーズを指定します。1 つ目の管理サーバーで、**<SiteRecoveryInstallationFolder>\\home\\sysystems\\bin\\genpassphrase.exe –n** を実行してパスフレーズを取得します。

	![プロセス サーバーの追加](./media/site-recovery-vmware-to-azure-classic/add-ps2.png)

### マシンを移行して新しいプロセス サーバーを使用する

1. **[構成サーバー]**、**[サーバー]**、[<1 つ目の管理サーバーの名前>]、**[サーバーの詳細]** の順に選択して開きます。

	![プロセス サーバーの更新](./media/site-recovery-vmware-to-azure-classic/update-process-server1.png)

2. **[プロセス サーバー]** ボックスの一覧で、変更するサーバーの **[プロセスのサーバーの変更]** をクリックします。

	![プロセス サーバーの更新](./media/site-recovery-vmware-to-azure-classic/update-process-server2.png)

3. **[プロセス サーバーの変更]** の **[ターゲット プロセス サーバー]** で、新しい管理サーバーを選択し、新しいプロセス サーバーが処理する仮想マシンを選択します。サーバーに関する情報を確認するには、情報アイコンをクリックします。負荷の決定に役立つように、選択されている各仮想マシンを新しいプロセス サーバーにレプリケートするために必要な平均容量が表示されます。チェック マークをクリックして、新しいプロセス サーバーへのレプリケーションを開始します。

	![プロセス サーバーの更新](./media/site-recovery-vmware-to-azure-classic/update-process-server3.png)




## vCenter アクセスの VMware アクセス許可

プロセス サーバーは vCenter サーバー上の VM を自動的に検出できます。自動検出を実行するには、vCenter レベルでロール (Azure\_Site\_Recovery) を定義し、Site Recovery が vCenter サーバーにアクセスできるようにする必要があります。VMware マシンを Azure に移行する処理のみが必要で、Azure からのフェールバックは必要ない場合、読み取り専用ロールを定義するだけで十分です。「[手順 6: vCenter サーバーの資格情報をセットアップする](#step-6-set-up-credentials-for-the-vcenter-server)」の説明に従ってアクセス許可をセットアップします。ロールのアクセス許可の概要については、次の表をご覧ください。

**ロール** | **詳細** | **アクセス許可**
--- | --- | ---
Azure\_Site\_Recovery ロール | VMware VM の検出 |vCenter サーバーの次の特権を割り当てます。<br/><br/>データストア -> 空間の割り当て、データストアの参照、低レベル ファイルの操作、ファイルの削除、仮想マシン ファイルの更新<br/><br/>ネットワーク -> ネットワークの割り当て<br/><br/>リソース -> 仮想マシンをリソース プールに割り当て、電源がオフのマシンの移行、電源がオンのマシンの移行<br/><br/>タスク -> タスクの作成、タスクの更新<br/><br/>仮想マシン -> 構成<br/><br/>仮想マシン -> 対話 -> 質問への回答、デバイス接続、CD メディアの構成、フロッピー メディアの構成、電源オフ、電源オン、VMware ツールのインストール<br/><br/>仮想マシン -> インベントリ -> 作成、登録、登録解除<br/><br/>仮想マシン -> プロビジョニング -> 仮想マシンのダウンロードの許可、仮想マシン ファイルのアップロードの許可<br/><br/>仮想マシン -> スナップショット -> スナップショットの削除
vCenter ユーザー ロール | ソース VM をシャットダウンしない VMware VM の検出/フェールオーバー | vCenter サーバーに次の特権を割り当てます。<br/><br/>データ センター オブジェクト -> 子オブジェクトへのプロパゲート、ロール=読み取り専用 <br/><br/>ユーザーはデータセンター レベルで割り当てられるので、データセンター内のすべてのオブジェクトに対してアクセス権を持ちます。アクセス権を制限するには、**子オブジェクトへのプロパゲート**特権がある**アクセスなし**ロールを子オブジェクト (ESX ホスト、データストア、VM、ネットワーク) に割り当てます。
vCenter ユーザー ロール | フェールオーバーとフェールバック | vCenter サーバーに次の特権を割り当てます。<br/><br/>データセンター オブジェクト - 子オブジェクトへのプロパゲート、ロール=Azure\_Site\_Recovery<br/><br/>ユーザーはデータセンター レベルで割り当てられるので、データセンター内のすべてのオブジェクトに対してアクセス権を持ちます。アクセス権を制限するには、**子オブジェクトへのプロパゲート**特権がある**アクセスなし**ロールを子オブジェクト (ESX ホスト、データストア、VM、ネットワーク) に割り当てます。  



## サード パーティ製ソフトウェアに関する通知および情報

Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”).Microsoft is the not original author of the Third Party Code.The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below.Such licenses and information are provided for informational purposes only.This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428).Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.

## 次のステップ

Azure で実行されているフェールオーバーされたマシンをオンプレミス環境に戻す[フェールバックの詳細を確認](site-recovery-failback-azure-to-vmware-classic.md)します。

<!---HONumber=AcomDC_0824_2016-->