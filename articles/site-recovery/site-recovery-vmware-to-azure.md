<properties 
	pageTitle="オンプレミスの VMWare 仮想マシンまたは物理サーバーと Azure 間の保護の設定" 
	description="Azure Site Recovery は、オンプレミスの VMWare サーバーに配置されている仮想マシンと Azure、およびオンプレミスの物理サーバーと Azure 間のレプリケーション、フェールオーバー、および復旧を調整します。" 
	services="site-recovery" 
	documentationCenter="" 
	authors="raynew" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/26/2015" 
	ms.author="raynew"/>


# オンプレミスの VMWare 仮想マシンまたは物理サーバーと Azure 間の保護の設定

Azure Site Recovery は、仮想マシンと物理サーバーのレプリケーション、フェールオーバー、復旧を調整してビジネス継続性と障害復旧 (BCDR) 戦略に貢献します。可能なデプロイ シナリオについては、「[Azure Site Recovery の概要](../hyper-v-recovery-manager-overview.md)」を参照してください。

このチュートリアルは、Site Recovery をデプロイして以下を実現する方法について説明しています。

- **オンプレミスの VMWare 仮想マシンと Azure 間の保護**
- **オンプレミスの物理 Windows サーバーまたは Linux サーバーと Azure 間の保護**

以下のようなビジネス上の利点があります。

- 物理 Windows サーバーまたは Linux サーバーの保護。
- Azure Site Recovery ポータルを使用した簡単なレプリケーション、フェールオーバー、および復旧。
- インターネット、サイト間の VPN 接続、または Azure ExpressRoute 経由のデータのレプリケーション。   
- Azure からオンプレミスの VMWare インフラストラクチャへのフェールバック (復元)。 
- VMWare 仮想マシンの検出の簡素化。
- 複数 VM の整合性。特定のワークロードを実行している仮想マシンと物理サーバーを同時に整合性データ ポイントに復旧することができます。
- 複数のマシンに階層化されたワークロードの簡略化したフェールオーバーと復旧の復旧計画。

現在、この機能はプレビュー段階にあります。[プレビューの使用条件に関する補足](preview-supplemental-terms)に関するページを参照してください。

## このガイドについて

このガイドは、概要とデプロイ上の前提条件について説明しています。すべてのデプロイ コンポーネントを設定し、仮想マシンおよびサーバーの保護を有効にする手順について、順をおって説明します。すべてが正しく動作していることを確認するために、最後にフェールオーバーをテストします。

問題が発生した場合は、[Azure Recovery Services フォーラム](http://go.microsoft.com/fwlink/?LinkId=313628)に質問を投稿してください。

## 概要

次の図は、デプロイの各種コンポーネントを示しています。

![New vault](./media/site-recovery-vmware-to-azure/ASRVMWare_Arch.png)

### デプロイ コンポーネント

- **オンプレミスのマシン** - オンプレミスのサイトに保護するマシンがあります。これらのマシンは、VMWare ハイパーバイザーで稼働している仮想マシンか、Windows または Linux を実行している物理サーバーです。

- **オンプレミスのプロセス サーバー** - 保護されたマシンは、オンプレミスのプロセス サーバーにレプリケーション データを送信します。プロセス サーバーはこのデータを使用してさまざまな操作を実行します。プロセス サーバーは、Azure のマスター ターゲット サーバーにデータを送信する前に、データを最適化します。ディスク ベースのキャッシュを使用して受信したレプリケーション データをキャッシュします。このサーバーは、保護する各仮想マシンまたは物理サーバーにインストールする必要があるモビリティ サービスのプッシュ インストールも処理します。また、VMWare vCenter サーバーの自動検出も実行します。プロセス サーバーは、Windows Server 2012 R2 を実行する仮想サーバーまたは物理サーバーです。保護するマシンと同じネットワークおよび LAN セグメントにプロセス サーバーを配置することをお勧めします。ただし、保護されたマシンがプロセス サーバーに対する L3 ネットワーク可視性を持っている場合は、プロセス サーバーを異なるネットワーク上で実行することもできます。プロセス サーバーはデプロイ時に設定して構成サーバーに登録します。

- **Azure Site Recovery コンテナー** - コンテナーは、オンプレミスのサイトと Azure 間でのデータ レプリケーション、フェールオーバー、および復旧を調整およびオーケストレーションします。

- **Azure 構成サーバー** - 構成サーバーは、保護されたマシン、プロセス サーバー、および Azure のマスター ターゲット サーバー間のやり取りを調整します。レプリケーションを設定し、フェールオーバーが発生した場合に Azure での復旧を調整します。構成サーバーは、Azure サブスクリプションの Azure の Standard A3 仮想マシン上で実行されます。このサーバーは、デプロイ時に設定して Azure Site Recovery コンテナーに登録します。

- **マスター ターゲット サーバー** - Azure のマスター ターゲット サーバーは、Azure ストレージ アカウントの BLOB ストレージに作成された接続済みの VHD を使用して、保護されたマシンからレプリケートされたデータを保持します。このサーバーを Azure 仮想マシンとしてデプロイする際、Windows マシンを保護する場合は Windows Server 2012 R2 ギャラリー イメージに基づく Windows サーバーとしてデプロイします。Linux マシンを保護する場合は、OpenLogic CentOS 6.6 ギャラリー イメージに基づく Linux サーバーとしてデプロイします。このサーバーには、Standard A3 と Standard D14 の 2 つのサイズを利用できます。サーバーは、構成サーバーと同じ Azure ネットワークに接続されます。このサーバーはデプロイ時に作成して、構成サーバーに登録します。

- **モビリティ サービス** - 保護する各 VMWare 仮想マシンまたは Windows/Linux 物理サーバーにモビリティ サービスをインストールします。レプリケーション データは、このサービスからプロセス サーバーに送信され、その後、Azure のマスター ターゲット サーバーに送信されます。プロセス サーバーは、保護されたマシンにモビリティ サービスを自動的にインストールできます。また、社内のソフトウェア デプロイ プロセスを使用してこのサービスを手動でデプロイすることもできます。

- **データ通信およびレプリケーション チャネル** - 2 つのオプションがあります。どちらのオプションでも、保護されたマシン上で受信ネットワーク ポートを開く必要があります。すべてのネットワーク通信は、オンプレミスのサイトから開始されます。
	- **インターネット経由** - 保護されたオンプレミスのサーバーと Azure の通信およびデータ レプリケーションをセキュリティで保護されたパブリック インターネット接続を介して実行します。これが既定のオプションです。
	- **VPN/ExpressRoute** - オンプレミスのサーバーと Azure の通信およびデータ レプリケーションを VPN 接続を介して実行します。オンプレミスのサイトと Azure ネットワークとの間にサイト間 VPN または [ExpressRoute](expressroute) 接続を設定する必要があります。 

 
## 容量計画

- パフォーマンスの最適化を図り、複数 VM の整合性機能を活用して、保護された複数のマシンを整合性データ ポイントに復旧するために、仮想マシンをワークロードごとに保護グループにまとめることをお勧めします。
- 1 台のマシンを複数のマスター ターゲット サーバーで保護することはできません。これは、ディスクのレプリケート時に、このディスクのサイズをミラーリングする VHD が Azure の BLOB ストレージに作成され、データ ディスクとしてマスター ターゲット サーバーに接続されるためです。もちろん、複数のマシンを 1 台のマスター ターゲット サーバーで保護することはできます。
- マスター ターゲット サーバーの仮想マシンに Azure Standard A4 または D14 を使用できます。
	- Standard A4 ディスクを使用する場合、各仮想マシンに 16 個のデータ ディスク (データ ディスクあたり最大 1023 GB) を追加できます。
	- Standard D14 ディスクを使用する場合は、各仮想マシンに 32 個のデータ ディスク (データ ディスクあたり最大 1023 GB) を追加できます。
- マスター ターゲット サーバーに接続されているディスクの 1 つはリテンション期間ドライブとして予約されています。Azure Site Recovery では、リテンション期間ウィンドウを定義し、このウィンドウ内の任意の時点で保護されたマシンを復旧できます。リテンション期間ドライブには、ウィンドウの継続期間中に発生したディスクの変更ジャーナルが保持されます。この機能により、最大ディスク数は A4 で 15 に、D14 で 31 に減少します。 
- デプロイをスケーリングするには、複数のプロセス サーバーとマスター ターゲット サーバーを追加します。既存のマスター ターゲット サーバーに十分な空きディスクがない場合は、2 つ目のマスター ターゲット サーバーをデプロイする必要があります。保護されたマシンのデータ変更率が、既存のプロセス サーバーの能力を超過している場合は、追加のプロセス サーバーをデプロイする必要があります。プロセス サーバーとマスター ターゲット サーバーには、1 対 1 のマッピングは必要ありません。最初のプロセス サーバーを 2 つ目のマスター ターゲット サーバーを使用してデプロイすることができ、それ以降も同様にデプロイできます。
プロセス サーバーは、ディスク ベースのキャッシュを使用します。C:/ にキャッシュ用の十分な空き領域があることを確認してください。キャッシュのサイズ変更は、保護しているマシンのデータ変更率の影響を受けます。通常、中規模のデプロイで推奨されるキャッシュ ディレクトリのサイズは 600 GB ですが、次のガイドラインを使用できます。

	![Cache guidelines](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerSize.png)


## 開始する前に

### Azure の前提条件

- [Microsoft Azure](http://azure.microsoft.com/) のアカウントが必要です。アカウントがなくても、[無料評価版](http://aka.ms/try-azure)を使用できます。 
- レプリケートしたデータを格納するには Azure ストレージ アカウントが必要になります。アカウントではジオ (主要地域) レプリケーションを有効にする必要があります。アカウントは Azure Site Recovery コンテナーと同じリージョンである必要があり、同じサブスクリプションに関連付けられている必要があります。詳細については、「[Microsoft Azure Storage の概要](http://go.microsoft.com/fwlink/?LinkId=398704)」を参照してください。
- 構成サーバーとターゲット マスター サーバーのデプロイ先となる Azure 仮想ネットワークが必要になります。Azure 仮想ネットワークは、Azure Site Recovery コンテナーと同じリージョンである必要があり、同じサブスクリプションに関連付けられている必要があります。
- すべてのコンポーネントをデプロイできるだけの十分な Azure リソースがあることを確認してください。詳細については、[Azure サブスクリプションの制限](azure-subscription-service-limits)を参照してください。
- 保護するマシンが Azure 仮想マシンの要件を満たしていることを確認します。 

	- **ディスクの数** - 1 台の保護されたサーバーでサポートできるディスク数は最大 31 です。
	- **ディスク サイズ** - 個々のディスク容量は 1023 GB 未満である必要があります。
	- **クラスタリング** - クラスター化されたサーバーはサポートされません。
	- **ブート** - Unified Extensible Firmware Interface (UEFI) ブートまたは拡張ファームウェア インターフェイス (EFI) ブートはサポートされません。
	- **ボリューム** - Bitlocker 暗号化ボリュームはサポートされません。
	- **サーバー名** - 名前は 1 ～ 63 文字 (文字、数字、ハイフン) である必要があります。文字または数字で始まり、文字または数字で終わる必要があります。マシンが保護された後で、Azure の名前を変更することができます。	

	Azure の要件の詳細については、「[仮想マシンのサポート](https://msdn.microsoft.com/library/azure/dn469078.aspx#BKMK_E2A)」を参照してください。

### シナリオのコンポーネントの前提条件

- プロセス サーバー: 
	- プロセス サーバーは、最新の更新プログラムがインストールされている Windows Server 2012 R2 を実行している物理マシンまたは仮想マシンにデプロイできます。C:/ にインストールしてください。
	- サーバーには、少なくとも 8 つのプロセッサ コアがあり、64 GB の RAM と C: に 300 GB の空き領域があることが推奨されます。
	- プロセス サーバーは、保護するマシンと同じネットワークおよびサブネット上に配置することをお勧めします。
	- VMWare vCenter サーバーを自動的に検出できるように、このサーバーに VMware vSphere CLI 5.1 をインストールします。
- 構成サーバー、マスター ターゲット サーバー、プロセス サーバー、およびフェールバック サーバーのインストール パスには英字のみ使用できます。たとえば、Linux を実行するマスター ターゲット サーバーのパスは、**/usr/local/ASR** のようにする必要があります。

### VMWare の前提条件

- VMware vSphere ハイパーバイザーを管理する VMWare vCenter サーバーがあること。vCenter バージョン 5.0 以降を実行し、最新の更新プログラムがインストールされている必要があります。 
- 保護する VMWare 仮想マシンを含む、1 つまたは複数の vSphere ハイパーバイザーがあること。このハイパーバイザーは、ESX/ESXi バージョン 5.0 以降を実行し、最新の更新プログラムがインストールされている必要があります。
- vCenter サーバーを通じて検出された VMWare 仮想マシンには VMware ツールがインストールされ、実行されている必要があります。

### 保護された Windows マシンの前提条件

Windows を実行する保護された物理サーバーまたは VMWare 仮想マシンには、以下が必要です。

- サポートされている 64 ビット オペレーティング システム。Windows Server 2012 R2、Windows Server 2012、または Windows Server 2008 R2 SP1 以降が必要です。
- ホスト名、マウント ポイント、デバイス名、Windows システム パス (例: C:\Windows) には英語のみ使用できます。
- オペレーティング システムは C:\ ドライブにインストールする必要があります。
- Windows サーバーの通常のストレージ オプションはサポートされています。
- 保護されたマシンのファイアウォール規則は、保護されたマシンから構成サーバーおよび Azure のマスター ターゲット サーバーにアクセスできるように設定する必要があります。 
- フェールオーバー後に、Azure の Windows 仮想マシンにリモート デスクトップを使用して接続する場合、オンプレミスのマシンでリモート デスクトップが有効になっていることを確認してください。VPN 経由で接続していない場合、ファイアウォール規則でインターネット経由のリモート デスクトップ接続を許可する必要があります。

### 保護された Linux マシンの前提条件

Linux を実行する保護された物理サーバーまたは VMWare 仮想マシンには、以下が必要です。

- サポートされるオペレーティング システム:Centos 6.4、6.5、6.6 (32 または 64 ビット)、Red Hat 互換カーネルまたは Unbreakable Enterprise Kernel リリース 3 (UEK3) を実行している Oracle Enterprise Linux 6.4、6.5 (32 また 64 ビット)、SUSE Linux Enterprise Server 11 SP3 (32 または 64 ビット)
- ホスト名、マウント ポイント、デバイス名、および Linux システム パスとファイル名 (例: /etc/; /usr) には英語のみ使用できます。
- オンプレミスのマシンに対する保護は、以下のストレージで有効にできます。
	- ファイル システム:EXT3、ETX4、ReiserFS、XFS
	- マルチパス ソフトウェアDevice Mapper - マルチパス
	- ボリューム マネージャー: LVM2
	- HP CCISS コントローラー ストレージを使用する物理サーバーはサポートされていません。
- 保護されたマシンのファイアウォール規則は、保護されたマシンから構成サーバーおよび Azure のマスター ターゲット サーバーにアクセスできるように設定する必要があります。
- フェールオーバー後に、Linux を実行する Azure 仮想マシンに Secure Shell クライアント (ssh) を使用して接続する場合、保護されたマシンのシステム ブート時に Secure Shell サービスが自動的に起動し、ファイアウォールの規則で仮想マシンへの ssh 接続が許可されるように設定していることを確認してください。  

### サード パーティの前提条件

このシナリオでは、正しく機能するためにサード パーティのソフトウェアに依存している、一部のデプロイ コンポーネントがあります。すべてを一覧表示するには、「<a href="#thirdparty">Third Party Software Notices and Information</a>」を参照してください。 

## 手順 1:コンテナーの作成

1. [管理ポータル](https://portal.azure.com)にサインインします。


2. **[データ サービス]**、**[復旧サービス]** の順に展開し、**[Site Recovery コンテナー]** をクリックします。


3. **[新規作成]**、**[簡易作成]** の順にクリックします。
	
4. **[名前]** ボックスに、コンテナーを識別する表示名を入力します。

5. **[リージョン]** ボックスで、コンテナーのリージョンを選択します。サポートされているリージョンを確認するには、[Azure Site Recovery の料金の詳細](href="http://go.microsoft.com/fwlink/?LinkId=389880)に関するページで利用可能地域を参照してください。

6. **[コンテナーの作成]** をクリックします。 

	![New vault](./media/site-recovery-vmware-to-azure/ASRVMWare_CreateVault.png)

ステータス バーを確認して、コンテナーが正常に作成されたことを確かめます。メインの **[復旧サービス]** ページで、コンテナーは **[アクティブ]** と表示されています。

## 手順 2:構成サーバーのデプロイ


1. **[復旧サービス]** ページで、コンテナーをクリックして [クイック スタート] ページを開きます。[クイック スタート] は、アイコンを使っていつでも開くことができます。

	![Quick Start Icon](./media/site-recovery-vmware-to-azure/ASRVMWare_QuickStartIcon.png)

2. ドロップダウン リストで、**[オンプレミスのサイトと VMware 間/物理サーバーと Azure 間]** を選択します。
3. **[ターゲット リソースの準備]** で、**[構成サーバーのデプロイ]** をクリックします。

	![Deploy configuration server](./media/site-recovery-vmware-to-azure/ASRVMWare_DeployCS.png)

4. サーバーに接続するための構成サーバーの詳細情報と資格情報を指定します。サーバーを配置する必要がある Azure ネットワークを選択します。サーバーに割り当てる内部 IP アドレスとサブネットを指定します。**[OK]** をクリックすると、Azure Site Recovery Windows Server 2012 R2 ギャラリー イメージに基づいた構成サーバー用の Standard A3 仮想マシンがサブスクリプションに作成されます。新しいクラウド サービスの最初のインスタンスとして作成され、予約されたパブリック IP アドレスが割り当てられます。

	![Configuration server settings](./media/site-recovery-vmware-to-azure/ASRVMWare_CSDetails.png)

5. ジョブの進捗状況は **[ジョブ]** タブで監視できます。

	![Monitor progress](./media/site-recovery-vmware-to-azure/ASRVMWare_MonitorConfigServer.png)

8.  構成サーバーがデプロイされたら、Azure ポータルの **[仮想マシン]** ページで、構成サーバーに割り当てられたパブリック IP アドレスを書き留めます。さらに、**[エンドポイント]** タブで、プライベート ポート 443 にマップされたパブリック HTTPS ポートを書き留めます。この情報は、後で、マスター ターゲット サーバーとプロセス サーバーを構成サーバーに登録するときに必要になります。構成サーバーは、次の 4 つのパブリック エンドポイントでデプロイされます。

	- 443.コンポーネント サーバーと Azure 間の通信を調整するために使用される HTTPS チャネルです。
	- 9443.フェールバック ツールとフェールバック通信で使用されます。
	- リモート デスクトップ
	- PowerShell


## 手順 3:構成サーバーのコンテナーへの登録

1. **[ターゲット リソースの準備]** で、**[登録キーのダウンロード]** をクリックします。キー ファイルが自動的に生成されます。キーは生成後 5 日間有効です。構成サーバーにファイルをコピーします。
2. 仮想マシンの **[ダッシュボード]** ページで **[接続]** をクリックします。ダウンロードした RDP ファイルを使用して、リモート デスクトップで構成サーバーにログオンします。初回ログオン時は、Azure Site Recovery のインストールと登録のウィザードが自動的に実行されます。

	![Registration](./media/site-recovery-vmware-to-azure/ASRVMWareRegister1.png)

3. ウィザードの指示に従います。MySQL Server をダウンロードしてインストールし、資格情報を指定する必要があります。**[Azure Site Recovery の登録]** ページで、サーバーにコピーしたキー ファイルを参照します。

	![Registration key](./media/site-recovery-vmware-to-azure/ASRVMWareRegister2.png)

4. 登録が完了すると、パスフレーズが生成されます。パスフレーズを安全な場所にコピーしてください。このパスフレーズは、プロセス サーバーとマスター ターゲット サーバーを構成サーバーに認証および登録するときに必要になります。また、構成サーバーの通信におけるチャネルの整合性を確保するためにも使用されます。パスフレーズは再生成できますが、その新しいパスフレーズを使用してマスター ターゲット サーバーとプロセス サーバーを再登録する必要があります。

	![Passphrase](./media/site-recovery-vmware-to-azure/ASRVMWareRegister2.png)

登録後、コンテナーの **[構成サーバー]** ページに構成サーバーが一覧されます。

## 手順 4:VPN 接続の設定
 
構成サーバーにはインターネット経由か、VPN または ExpressRoute 接続を使用して接続できます。インターネット接続では、仮想マシンのエンドポイントと、サーバーのパブリック仮想 IP アドレスを使用します。VPN では、サーバーの内部 IP アドレスと、エンドポイントのプライベート ポートを使用します。
 
サーバーへの VPN 接続は、次のように構成できます。

1. サイト間接続または Azure ExpressRoute 接続を設定していない場合、詳細については、以下を参照してください。

	- [ExpressRoute または VPN - 何が適しているか](http://azure.microsoft.com/blog/2014/06/10/expressroute-or-virtual-network-vpn-whats-right-for-me/)に関するページ
	- [Azure 仮想マシンへのサイト間通信の構成](https://msdn.microsoft.com/library/azure/dn133795.aspx)に関するページ
	- [ExpressRoute の構成](https://msdn.microsoft.com/library/azure/dn606306.aspx)に関するページ
	
2. コンテナーで、**[サーバー]**、**[構成サーバー]**、[構成サーバー]**、[構成]** の順にクリックします。
3. **[接続設定]** で、**[接続の種類]** を **[VPN]** に設定します。VPN が設定済みでオンプレミスのサイトからのインターネット アクセスがない場合は、VPN オプションを選択していることを確認してください。このオプションを選択していない場合、プロセス サーバーはパブリック エンドポイントにあるマスター ターゲット サーバーにレプリケーション データを送信できません。

	![Enable VPN](./media/site-recovery-vmware-to-azure/ASRVMWare_EnableVPN.png)

## 手順 5:マスター ターゲット サーバーのデプロイ

1. **[ターゲット リソースの準備]** で、**[マスター ターゲット サーバーのデプロイ]** をクリックします。
2. マスター ターゲット サーバーの詳細情報と資格情報を指定します。サーバーは、登録先の構成サーバーと同じ Azure ネットワークにデプロイされます。クリックして完了すると、Windows または Linux のギャラリー イメージを使用して Azure 仮想マシンが作成されます。 

	![Target server settings](./media/site-recovery-vmware-to-azure/ASRVMWare_TSDetails.png)

3. Windows マスター サーバーの仮想マシンは、以下のパブリック TCP エンドポイントで作成されます。

	- カスタム:レプリケーション データをインターネット経由で送信するためにパブリック ポートが使用されます。プロセス サーバーがマスター ターゲット サーバーに VPN を介してデータを送信するためにプライベート ポート 9443 が使用されます。
	- カスタム 1: プロセス サーバーがターゲット サーバーに VPN を介してデータを送信するためにプライベート ポート 9080 が使用されます。
	- PowerShell:プライベート ポート: 5986
	- リモート デスクトップ: プライベート ポート: 3389

4. Linux マスター サーバーの仮想マシンは、以下のエンドポイントで作成されます。

	- カスタム:レプリケーション データをインターネット経由で送信するためにパブリック ポートが使用されます。プロセス サーバーがマスター ターゲット サーバーに VPN を介してデータを送信するためにプライベート ポート 9443 が使用されます。
	- カスタム 1: プロセス サーバーがマスター ターゲット サーバーに VPN を介してデータを送信するためにプライベート ポート 9080 が使用されます。
	- SSH: プライベート ポート 22

5. **[仮想マシン]** で、仮想マシンが起動するまで待機します。 

	- サーバーを Windows で構成している場合は、リモート デスクトップの詳細情報をメモしておきます。
	- Linux で構成しており、VPN 経由で接続している場合は、仮想マシンの内部 IP アドレスをメモしておきます。インターネット経由で接続している場合は、パブリック IP アドレスをメモしておきます。
6.  サーバーにログオンしてインストールを完了し、構成サーバーに登録します。Windows を実行している場合は以下のようにします。

	1. 仮想マシンへのリモート デスクトップ接続を開始します。初回ログオン時は、PowerShell ウィンドウでスクリプトが実行されます。このウィンドウは閉じないでください。終了すると、ホスト エージェントの構成ツールが自動的に開いてサーバーが登録されます。
	2. **[ホスト エージェントの構成]** で、構成サーバーの内部 IP アドレスとポート番号の 443 を指定します。VPN モード経由で接続していなくても、内部アドレスとプライベート ポート 443 を使用することができます。これは、仮想マシンが構成サーバーと同じ Azure ネットワークに接続しているためです。**[HTTPS を使用する]** は、有効のままにします。先ほどメモしておいた構成サーバーのパスフレーズを入力します。**[OK]** をクリックしてサーバーを登録します。このページの NAT オプションは無視してかまいません。これらは使用されません。

	![Windows master target server](./media/site-recovery-vmware-to-azure/ASRVMWare_TSRegister.png)

6. Linux を実行している場合は以下のようにします。
	1. sftp クライアントを使用して[サーバー インストーラー tar ファイル](http://go.microsoft.com/fwlink/?LinkID=529757&clcid=0x409)を仮想マシンにコピーします。または、ログオン後、[クイック スタート] ページのリンクから wget を使用してこのファイルをダウンロードすることもできます。 
	2. Secure Shell クライアントを使用してサーバーにログオンします。VPN 経由で Azure ネットワークに接続している場合は、内部 IP アドレスを使用します。それ以外の場合は、外部 IP アドレスと SSH パブリック エンドポイントを使用します。
	3. 次を実行して gzipped インストーラーからファイルを抽出します。**tar -xvzf Microsoft-ASR_UA_8.2.0.0_RHEL6-64***."   

		![Linux master target server](./media/site-recovery-vmware-to-azure/ASRVMWare_TSLinuxTar.png)

	4. tar ファイルの内容を抽出したディレクトリで操作していることを確認し、"**sudo ./install.sh**" コマンドを実行します。オプション **2. Master Target** を選択します。他のオプションは既定の設定のままにします。

		![Register target server](./media/site-recovery-vmware-to-azure/ASRVMWare_TSLinux.png)

	5. インストールが終了すると、コマンド ライン **Host Config Interface** が表示されます。ウィンドウのサイズ変更はしないでください。 
	6. 方向キーを使用して **[Global]** を選択し、Enter キーを押します。
	7. **[Enter the IP address]** に、構成サーバーの内部アドレスとポート番号 22 を入力します。
	8. 先ほどメモしておいた構成サーバーのパスフレーズを指定し、Enter キーを押します。**[Quit]** を選択してインストールを終了します。PuTTY クライアントを使用して仮想マシンに ssh 接続している場合は、Shift キーと Ins キーを同時に押すと貼り付けることができます。 
	9. 右方向キーで終了し、Enter キーを押します。

		![Master target server settings](./media/site-recovery-vmware-to-azure/ASRVMWare_TSLinux2.png)

7. 5 ～ 10 分間待機してから、**[サーバー]**、**[構成サーバー]** ページの順にクリックして、**[サーバーの詳細]** タブにマスター ターゲット サーバーが登録済みとして表示されていることを確認します。Linux を実行していて、登録されていない場合は、/usr/local/ASR/Vx/bin/hostconfigcli からホストの構成ツールをもう一度実行してください。スーパー ユーザーとして chmod を実行し、アクセス許可を設定する必要があります。

	![Verify target server](./media/site-recovery-vmware-to-azure/ASRVMWare_TSList.png)

## 手順 6:オンプレミスのプロセス サーバーのデプロイ

1. [クイック スタート] で、**[オンプレミスのプロセス サーバーのインストール]**、**[プロセス サーバーをダウンロードしてインストールする]** の順にクリックします。

	![Install process server](./media/site-recovery-vmware-to-azure/ASRVMWare_PSDeploy.png)

2. ダウンロードした zip ファイルを、プロセス サーバーのインストール先となるサーバーにコピーします。この zip ファイルには、次の 2 つのインストール ファイルが含まれています。

	- Microsoft-ASR_CX_TP_8.2.0.0_Windows*
	- Microsoft-ASR_CX_8.2.0.0_Windows*

3. アーカイブを解凍し、インストール ファイルをサーバー上の場所にコピーします。
4. インストール ファイルの **Microsoft-ASR_CX_TP_8.2.0.0_Windows*** を実行して、指示に従います。これでデプロイに必要なサード パーティのコンポーネントがインストールされます。
5. 次に、**Microsoft-ASR_CX_8.2.0.0_Windows*** を実行します。
6. **[サーバー モード]** ページで、**[プロセス サーバー]** を選択します。
7.	VPN 経由で接続している場合は、**[構成サーバーの詳細]** で、構成サーバーの内部 IP アドレスとポート番号に 443 を指定します。それ以外の場合は、パブリック仮想 IP アドレスとマップされたパブリック HTTP エンドポイントを指定します。
8.	自動プッシュを使用してサービスをインストールする際に検証を無効にする場合は、**[モビリティ サービスのソフトウェア署名を検証する]** をクリアします。署名の検証には、プロセス サーバーからのインターネット接続が必要です。
9.	構成サーバーのパスフレーズを入力します。

	![Register configuration server](./media/site-recovery-vmware-to-azure/ASRVMWare_CSRegister.png)

8. サーバーのインストールを完了します。vCenter サーバーを検出できるようにするためには、サーバー上に VMware vSphere CLI 5.1 をインストールする必要があります。

	![Register process server](./media/site-recovery-vmware-to-azure/ASRVMWare_PSRegister2.png)

**[構成サーバー]**、**[サーバーの詳細]** の順にクリックしてプロセス サーバーがコンテナーに正常に登録されたことを確認します。

![Validate process server](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerRegister.png)

プロセス サーバーを登録したときにモビリティ サービスの署名の検証を無効にしなかった場合、以下のように実行すると後でも無効にできます。

1. プロセス サーバーに管理者としてログオンし、編集のために C:\pushinstallsvc\pushinstaller.conf ファイルを開きます。**[PushInstaller.transport]** セクションに **SignatureVerificationChecks="0"** という行を追加します。ファイルを保存して閉じます。
1. InMage PushInstall サービスを再起動します。


## 手順 7:vCenter サーバーの追加

1. **[サーバー]**、**[構成サーバー]** タブの順にクリックします。構成サーバーを選択し、vCenter サーバーを追加するためにクリックします。

	![Select vCenter server](./media/site-recovery-vmware-to-azure/ASRVMWare_AddVCenter.png)

2. vCenter サーバーの詳細を指定して、このサーバーの検出に使用されるプロセス サーバーを選択します。このプロセス サーバーは、vCenter サーバーと同じネットワークに配置されており、VMware vSphere CLI 5.1 がインストールされている必要もあります。
3. 検出が終了すると、構成サーバーの詳細の下に vCenter サーバーが表示されます。
	
	![vCenter server settings](./media/site-recovery-vmware-to-azure/ASRVMWare_AddVCenter2.png)


## 手順 8:保護グループの作成

1. **[保護された項目]**、**[保護グループ]** の順に開き、保護グループを追加するためにクリックします。

	![Create protection group](./media/site-recovery-vmware-to-azure/ASRVMWare_CreatePG1.png)

2. **[保護グループの設定を指定する]** ページで、グループの名前を指定し、グループを作成する構成サーバーを選択します。

	![Protection group settings](./media/site-recovery-vmware-to-azure/ASRVMWare_CreatePG2.png)

3. **[レプリケーションの設定を指定する]** ページで、グループのすべてのマシンで使用するレプリケーションの設定を構成します。   

	![Protection group replication](./media/site-recovery-vmware-to-azure/ASRVMWare_CreatePG3.png)

4. 以下のように設定します。
	- **[複数 VM の整合性]**: これをオンにすると、保護グループに属する複数のアプリケーションで共有されるアプリケーション整合性復旧ポイントが作成されます。この設定は、保護グループ内のすべてのマシンが同じワークロードを実行している場合に最も適しています。すべてのマシンは同じデータ ポイントに復旧されます。この機能は Windows サーバーのみ利用できます。 
	- **[RPO しきい値]**: 構成された RPO しきい値を継続的なデータ保護レプリケーションの RPO が超過すると、警告が生成されます。
	- **[復旧ポイントのリテンション期間]**: リテンション期間ウィンドウを指定します。保護されたマシンはこのウィンドウ内のどのポイントにも復旧できます。
	- **[アプリケーション整合性スナップショットの頻度]**: アプリケーション整合性スナップショットを含む復旧ポイントを作成する頻度を指定します。

保護グループの作成は、**[保護された項目]** ページで監視することができます。 

## 手順 9:モビリティ サービスのプッシュ

保護グループにマシンを追加すると、プロセス サーバーによってモビリティ サービスが自動的にプッシュされ、各マシンにインストールされます。Windows を実行している保護されたマシンに対してこの自動プッシュのメカニズムを使用する場合、各マシンで以下を実行する必要があります。

1. Windows ファイアウォールで**ファイルとプリンターの共有**および **Windows Management Instrumentation** を許可するように構成します。マシンがドメインに属している場合は、GPO を使用してファイアウォール ポリシーを構成できます。
2. プッシュ インストールを実行するために使用するアカウントは、保護するマシンの管理グループに属している必要があります。これらの資格情報は、プッシュ インストールでのみ使用されます。モビリティ サービスによってどの場所にも格納されることはなく、サーバーが保護された後で削除されます。マシンを保護グループに追加するときに、この資格情報を指定する必要があります。

	![Mobility credentials](./media/site-recovery-vmware-to-azure/ASRVMWare_PushCredentials.png)

3. 管理アカウントがドメイン アカウントでない場合、ローカル マシンでのリモート ユーザーのアクセス制御を無効にする必要があります。これを行うには、HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System で、エントリ LocalAccountTokenFilterPolicy が存在しない場合はそのエントリを作成し、DWORD 値を割り当てます。 

Linux を実行するマシンを保護する場合は、以下を実行する必要があります。

1. アカウントがソース Linux サーバーの root ユーザーであることを確認します。
1. 保護するマシンに最新の openssh、openssh-server、openssl の各パッケージをインストールします。
1. 22 番の ssh ポートを有効にします。
2. 以下の手順で、sshd 構成ファイルで Sftp サブシステムとパスワード認証を有効にします。
	1. root ユーザー アカウントでログインします。
	2. /etc/ssh/sshd_config ファイルで、"PasswordAuthentication" で始まる行を見つけます。この行のコメントを解除し、値 "no" を "yes" に変更します。 

		![Linux mobility](./media/site-recovery-vmware-to-azure/ASRVMWare_LinuxPushMobility1.png)

	4. "Subsystem" で始まる行を見つけ、この行のコメントを解除します。 

		![Linux push mobility](./media/site-recovery-vmware-to-azure/ASRVMWare_LinuxPushMobility1.png)

## 手順 10:保護グループへのマシンの追加

1. **[保護された項目]**、**[保護グループ]**、**[マシン]** タブの順に開き、検出された vCenter サーバーで管理される仮想マシンまたは物理マシンを追加します。保護グループでワークロードをミラーリングすることをお勧めします。これによって特定のアプリケーションを実行しているマシンを同じグループに追加できます。

	![Add machines](./media/site-recovery-vmware-to-azure/ASRVMWare_PushCredentials.png)

2. **[仮想マシンの追加]** ページの **[仮想マシンの選択]** で、V-Center サーバーを選択し、次に、ここからマシンを選択します。

	![Add V-Center server](./media/site-recovery-vmware-to-azure/ASRVMWare_SelectVMs.png)

3. 保護グループにマシンを追加すると、オンプレミスのプロセス サーバーからモビリティ サービスが自動的にインストールされます。自動プッシュ メカニズムが機能するように、保護マシンを前の手順どおりに設定していることを確認してください。
4. **[仮想マシンの選択]** で、保護するマシンを管理する vCenter サーバーを選択し、次に仮想マシンを選択します。

4. レプリケーションで使用するサーバーとストレージを選択します。 

	![vCenter server](./media/site-recovery-vmware-to-azure/ASRVMWare_MachinesResources.png)

5. ソース サーバーのユーザーの資格情報を指定します。この情報は、モビリティ サービスをソース マシンに自動的にインストールするために必要です。Windows サーバーでは、アカウントはソース サーバーに対する管理者権限を持っている必要があります。Linux の場合は、アカウントはサーバーに対するスーパー ユーザー権限を持っている必要があります。

	![Linux credentials](./media/site-recovery-vmware-to-azure/ASRVMWare_VMMobilityInstall.png)

6. チェック マークをクリックして、保護グループへのマシンの追加を完了し、各マシンの初期レプリケーションを開始します。**[ジョブ]** ページで状態を監視できます。

	![Add V-Center server](./media/site-recovery-vmware-to-azure/ASRVMWare_PGJobs.png)

7. さらに、**[保護された項目]**、<保護グループ>、**[仮想マシン]** の順にクリックして、保護の状態を監視します。初期レプリケーションが完了してマシンのデータが同期されると、状態 **[保護されています]** が表示されます。

	![Virtual machine jobs](./media/site-recovery-vmware-to-azure/ASRVMWare_PGJobs.png)

## 手順 11:保護されたマシンのプロパティの設定

1. マシンの状態が **[保護されています]** になると、マシンのフェールオーバーのプロパティを構成できます。保護グループの詳細で、マシンを選択し、**[構成]** タブを開きます。
2. フェールオーバー後に Azure のマシンに付けられる名前と Azure のサイズを変更できます。フェールオーバー後にマシンが接続する Azure ネットワークを選択することもできます。以下の点に注意してください。

	- Azure マシンの名前は、前提条件で説明した Azure の要件を満たしている必要があります。
	- 既定では、レプリケートされた Azure の仮想マシンは、Azure ネットワークに接続しません。レプリケートした仮想マシンが通信する必要がある場合は、それらのマシンに同じ Azure ネットワークを設定していることを確認してください。

	![Set virtual machine properties](./media/site-recovery-vmware-to-azure/ASRVMWare_VMProperties.png)

## 手順 12:フェールオーバーの実行

1. **[復旧計画]** ページで、復旧計画を追加します。計画の詳細を指定し、ターゲットに **Azure** を選択します。

	![Configure recovery plan](./media/site-recovery-vmware-to-azure/ASRVMWare_RP1.png)

2. **[仮想マシンの選択]** で、保護グループを選択してから、復旧計画に追加するグループ内のマシンを選択します。

	![Add virtual machines](./media/site-recovery-vmware-to-azure/ASRVMWare_RP2.png)

3. 必要な場合は、計画をカスタマイズして、復旧計画でフェールオーバーするマシンのグループを作成し、その実行順序を指定できます。手動の操作とスクリプトのためのプロンプトを追加することもできます。

	![Customize recovery plan](./media/site-recovery-vmware-to-azure/ASRVMWare_RP2.png)

5. **[復旧計画]** ページで計画を選択し、**[テスト フェールオーバー]** をクリックします。
6. **[フェールオーバーの確認]** で、フェールオーバーの方向 (Azure に) を確認します。次に、フェールオーバーする復旧ポイントを選択します。 
7. フェールオーバー ジョブが完了するまで待機します。その後、フェールオーバーが予期したとおりに実行され、レプリケートされた仮想マシンが Azure で正常に起動することを確認します。

<a name="thirdparty"></a><h2>THIRD-PARTY SOFTWARE NOTICES AND INFORMATION</h2>

Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, "Third Party Code").Microsoft is the not original author of the Third Party Code.The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below.Such licenses and information are provided for informational purposes only.This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.  

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms. 

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=530254)  h. Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.


<!--HONumber=49--> 