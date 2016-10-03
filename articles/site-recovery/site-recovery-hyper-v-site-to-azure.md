<properties
	pageTitle="Azure ポータルで Azure Site Recovery を使用して (VMM で管理されていない) Hyper-V 仮想マシンを Azure にレプリケートする | Microsoft Azure"
	description="Azure ポータルで Azure Site Recovery をデプロイして、VMM で管理されていないオンプレミスの Hyper-V VM の Azure へのレプリケーション、フェールオーバー、復旧を調整する方法を説明します。"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="09/19/2016"
	ms.author="raynew"/>


# Azure ポータルで Azure Site Recovery を使用して (VMM で管理されていない) Hyper-V 仮想マシンを Azure にレプリケートする | Microsoft Azure

> [AZURE.SELECTOR]
- [Azure ポータル](site-recovery-hyper-v-site-to-azure.md)
- [Azure クラシック](site-recovery-hyper-v-site-to-azure-classic.md)
- [PowerShell ARM](site-recovery-deploy-with-powershell-resource-manager.md)



Azure Site Recovery へようこそ。 この記事では、System Center Virtual Machine Manager (VMM) クラウドで管理されて**いない**オンプレミスの Hyper-V 仮想マシンを Azure にレプリケートする方法について説明します。この記事では、Azure ポータルで Azure Site Recovery を使用してレプリケーションを設定する方法について説明します。

> [AZURE.NOTE] Azure には、リソースの作成と操作に関して、Azure Resource Manager (ARM) とクラシックの 2 種類の[デプロイメント モデル](../resource-manager-deployment-model.md)があります。また、Azure にも 2 つのポータルがあります。クラシック デプロイメント モデルをサポートする Azure クラシック ポータルと、両方のデプロイメント モデルをサポートする Azure ポータルです。


Azure ポータルの Azure Site Recovery には、次のような数多くの新機能が搭載されています。

- Azure ポータルでは、Azure Backup サービスと Azure Site Recovery サービスが統合されて 1 つの Recovery Services コンテナーになっているため、ビジネス継続性と障害復旧 (BCDR) を 1 つの場所から設定して管理できます。統合されたダッシュボードで、オンプレミス サイトと Azure パブリック クラウドの両方に対する監視と管理の操作が可能です。
- Azure サブスクリプションがクラウド ソリューション プロバイダー (CSP) プログラムを使用してプロビジョニングされたユーザーは、Azure ポータルで Site Recovery 操作を管理できるようになりました。
- Azure ポータルの Site Recovery では、マシンを ARM ストレージ アカウントにレプリケートできます。フェールオーバー時には、Azure に ARM ベースの VM が作成されます。
- Site Recovery では、従来のストレージ アカウントへのレプリケーションと、クラシック モデルを使用した VM へのフェールオーバーが引き続きサポートされます。


この記事に関するコメントがありましたら、下部にある DISQUS コメント欄に投稿してください。技術的な質問については、[Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。


## 概要


組織には、予定されたダウンタイムおよび予定外のダウンタイム時にアプリ、ワークロード、およびデータの実行と利用可能な状態を維持し、できるだけ早く通常の動作状態に復旧させる方法を決定する BCDR の戦略が必要です。BCDR 戦略では、災害発生時にビジネス データを安全かつ回復可能な状態に維持し、ワークロードが継続的に利用可能な状態に保たれるようにします。

Site Recovery とは、クラウド (Azure) またはセカンダリ データセンターへのオンプレミスの物理サーバーおよび仮想マシンのレプリケーションを統制することで BCDR 戦略を支援する Azure サービスです。プライマリ ロケーションで障害が発生した場合は、セカンダリ ロケーションにフェールオーバーしてアプリとワークロードの可用性を維持します。プライマリの場所が通常の動作に戻ると、その場所にフェールバックします。詳細については、「[Site Recovery とは](site-recovery-overview.md)」をご覧ください。

この記事には、VMM クラウドで管理されていない Hyper-V VM を Azure にレプリケートするために必要な情報がすべて記載されています。アーキテクチャの概要や計画情報、オンプレミス サーバー、Azure、レプリケーション ポリシー、およびキャパシティ プランニングを構成するためのデプロイメント手順などが含まれています。インフラストラクチャをセットアップしたら、保護対象のマシンでレプリケーションを有効にし、フェールオーバーをテストできます。

## ビジネス上の利点

- Hyper-V 仮想マシンで実行されているビジネス ワークロードやアプリケーションのオフサイト (Azure) フェールオーバーを実行できます。
- VMM サーバーを必要とせずに、Azure Site Recovery を使用して Hyper-V VM をレプリケートできます。
- 単一の Recovery Services コンソールを使用して、レプリケーション、フェールオーバー、復旧プロセスを簡単に設定および管理できます。
- オンプレミス インフラストラクチャから Azure へのフェールオーバーと、Azure からオンプレミス サイトへのフェールバック (復元) を簡単に実行できます。
- 複数のマシンを使用した復旧計画を構成し、階層化されたアプリケーション ワークロードをまとめてフェールオーバーできます。

## シナリオのアーキテクチャ

シナリオの構成要素を次に示します。

- **Hyper-V ホストまたはクラスター**: オンプレミスの Hyper-V ホスト サーバーまたはクラスター。保護対象の VM を実行している Hyper-V ホストは、Site Recovery のデプロイ時に Hyper-V 論理サイトに収集されます。
- **Azure Site Recovery Provider と Recovery Services エージェント**: デプロイ時に、Azure Site Recovery Provider と Microsoft Azure Recovery Services エージェントを Hyper-V ホスト サーバーにインストールします。プロバイダーは、HTTPS 443 経由で Azure Site Recovery と通信して、オーケストレーションをレプリケートします。Hyper-V ホスト サーバー上のエージェントは、既定では HTTPS 443 経由で Azure Storage にデータをレプリケートします。
- **Azure**: Azure サブスクリプション、レプリケートされたデータを格納するための Azure ストレージ アカウント、および Azure VM がフェールオーバー後にネットワークに接続されように Azure 仮想ネットワークが必要です。

![Hyper-V サイト アーキテクチャ](./media/site-recovery-hyper-v-site-to-azure/architecture.png)
 


## Azure の前提条件

このシナリオをデプロイするために Azure で必要となるものを次に示します。

**前提条件** | **詳細**
--- | ---
**Azure アカウント**| [Microsoft Azure](http://azure.microsoft.com/) のアカウントが必要です。アカウントがなくても、[無料試用版](https://azure.microsoft.com/pricing/free-trial/)を使用できます。Site Recovery の価格の詳細については、[こちら](https://azure.microsoft.com/pricing/details/site-recovery/)をご覧ください。 
**Azure Storage** | Standard ストレージ アカウントが必要です。LRS または GRS ストレージ アカウントを使用できます。地域的障害が発生した場合やプライマリ リージョンが復旧できない場合にデータの復元性を確保できるように、GRS をお勧めします。[詳細情報](../storage/storage-redundancy.md)。アカウントは、Recovery Services コンテナーと同じリージョンに存在する必要があります。<br/><br/> Premium Storage はサポートされていません。<br/><br/> レプリケートされたデータは Azure Storage に保存され、フェールオーバーが発生すると Azure VM が作成されます。<br/><br/> Azure Storage については、[こちら](../storage/storage-introduction.md)をご覧ください。
**Azure ネットワーク** | フェールオーバーが発生した場合に Azure VM が接続する Azure 仮想ネットワークが必要です。Azure 仮想ネットワークは、Recovery Services コンテナーと同じリージョンに存在する必要があります。 

## オンプレミスの前提条件

オンプレミスで必要なものを次に示します。

**前提条件** | **詳細**
--- | ---
**Hyper-V**| 最新の更新プログラムが適用された Windows Server 2012 R2 と Hyper-V ロールを実行している 1 つ以上のオンプレミス サーバー。<br/><br/>Hyper-V サーバーには、1 つ以上の仮想マシンが含まれています。<br/><br/>Hyper-V サーバーは、インターネットに直接またはプロキシ経由で接続します。<br/><br/>Hyper-V サーバーには、[KB2961977](https://support.microsoft.com/ja-JP/kb/2961977 "KB2961977") に記載されている修正プログラムがインストールされている必要があります。
**プロバイダーとエージェント** | Azure Site Recovery のデプロイ時に、Azure Site Recovery Provider をインストールします。保護対象の仮想マシンを実行している各 Hyper-V サーバーにプロバイダーをインストールすると、Azure Recovery Services エージェントもインストールされます。Site Recovery コンテナー内のすべての Hyper-V サーバーに、同じバージョンのプロバイダーとエージェントが必要です。<br/><br/>プロバイダーは、インターネット経由で Azure Site Recovery に接続する必要があります。トラフィックを直接送信することも、プロキシ経由で送信することもできます。HTTPS ベースのプロキシはサポートされていないことに注意してください。プロキシ サーバーでは、次の URL へのアクセスを許可する必要があります。<br/><br/> *.hypervrecoverymanager.windowsazure.com <br/><br/> *.accesscontrol.windows.net <br/><br/> *.backup.windowsazure.com <br/><br/> *.blog.core.windows.net <br/><br/> *store.core.windows.net <br/><br/> https://www.msftncsi.com/ncsi.txt<br/><br/>サーバーで IP アドレスベースのファイアウォール規則を使用している場合は、規則で Azure への通信が許可されていることを確認します。[Azure データセンターの IP 範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)と HTTPS (433) プロトコルを許可する必要があります。<br/><br/>ご利用のサブスクリプションの Azure リージョンと米国西部の IP アドレス範囲を許可します。

## 保護対象のマシンの前提条件


**前提条件** | **詳細**
--- | ---
**保護対象の VM** | VM をフェールオーバーする前に、Azure VM に割り当てられる名前が [Azure の前提条件](site-recovery-best-practices.md#azure-virtual-machine-requirements)に準拠していることを確認する必要があります。名前は、VM のレプリケーションを有効にした後で変更できます。<br/><br/> 保護対象のマシン上の個々のディスク容量が 1023 GB 以下である必要があります。VM は最大 64 個のディスク (したがって、最大 64 TB) に対応できます。<br/><br/> 共有ディスク ゲスト クラスターはサポートされていません。<br/><br/> ソース VM に NIC チーミングがある場合、Azure へのフェールオーバー後に単一の NIC に変換されます。<br/><br/>静的 IP アドレスを持ち、Linux を実行している VM の保護はサポートされていません。

## デプロイの準備をする

デプロイを準備するには、次の手順に従います。

1. フェールオーバー後に作成された Azure VM が配置される [Azure ネットワークをセットアップ](#set-up-an-azure-network)します。
2. レプリケートされたデータ用に、[Azure ストレージ アカウントを設定](#set-up-an-azure-storage-account)します。
3. 必要な URL に確実にアクセスできるように [Hyper-V ホストを準備](#prepare-the-hyper-v-hosts)します。

### Azure ネットワークをセットアップする

Azure ネットワークをセットアップします。これは、フェールオーバー後に作成された Azure VM がネットワークに接続するために必要となります。

- このネットワークは、Recovery Services コンテナーをデプロイするリージョンと同じリージョンである必要があります。
- フェールオーバーされた Azure VM に使用するリソース モデルに応じて、Azure ネットワークを [ARM モード](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)または[クラシック モード](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)でセットアップします。
- ネットワークをセットアップしてから、以下の作業を開始することをお勧めします。行わない場合は、Site Recovery のデプロイ中に行う必要があります。

> [AZURE.NOTE] Site Recovery のデプロイ用のネットワークでは、同じサブスクリプション内のリソース グループ間またはサブスクリプション間での[ネットワークの移行](../resource-group-move-resources.md)はサポートされていません。

### Azure ストレージ アカウントをセットアップする

- Azure にレプリケートされたデータを保持するために Standard Azure ストレージ アカウントが必要になります。
- フェールオーバーされた Azure VM に使用するリソース モデルに応じて、アカウントを [ARM モード](../storage/storage-create-storage-account.md)または[クラシック モード](../storage/storage-create-storage-account-classic-portal.md)でセットアップします。
- ストレージ アカウントをセットアップしてから、以下の作業を開始することをお勧めします。行わない場合は、Site Recovery のデプロイ中に行う必要があります。アカウントは、Recovery Services コンテナーと同じリージョンに存在する必要があります。

> [AZURE.NOTE] Site Recovery のデプロイ用のストレージ アカウントでは、同じサブスクリプション内のリソース グループ間またはサブスクリプション間での[ストレージ アカウントの移行](../resource-group-move-resources.md)はサポートされていません。

### Hyper-V ホストを準備する

- Hyper-V ホストが[前提条件](#on-premises-prerequisites)に準拠していることを確認します。

### Recovery Services コンテナーを作成する

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. **[新規]**、**[管理]**、**[Backup and Site Recovery (OMS) (バックアップと Site Recovery (OMS))]** の順にクリックします。または、**[参照]**、**[Recovery Services コンテナー]**、**[追加]** の順にクリックします。

	![新しいコンテナー](./media/site-recovery-hyper-v-site-to-azure/new-vault3.png)

3. **[名前]** に、コンテナーを識別するフレンドリ名を入力します。複数のサブスクリプションがある場合は、いずれかを選択します。
4. [新しいリソース グループを作成](../resource-group-template-deploy-portal.md)するか、既存のリソース グループを選択し、Azure リージョンを指定します。マシンは、このリージョンにレプリケートされます。サポートされているリージョンを確認するには、「[Azure Site Recovery Pricing Details (Azure Site Recovery の価格の詳細)](https://azure.microsoft.com/pricing/details/site-recovery/)」で利用可能地域を参照してください。
4. ダッシュボードからコンテナーにすばやくアクセスするには、**[ダッシュボードにピン留めする]** をクリックし、**[コンテナーの作成]** をクリックします。

	![新しいコンテナー](./media/site-recovery-hyper-v-site-to-azure/new-vault-settings.png)

新しいコンテナーは、**[ダッシュボード]** の **[すべてのリソース]** と、メインの **[Recovery Services コンテナー]** ブレードに表示されます。

## 使用の開始

Site Recovery に用意されている [使用の開始] エクスペリエンスを利用すると、最小限の時間でデプロイできます。[作業の開始] によって前提条件が確認され、Site Recovery のデプロイ手順が適切な順序で説明されます。

[作業の開始] では、レプリケートするマシンの種類とレプリケート先の場所を選択します。また、オンプレミス サーバー、Azure ストレージ アカウント、およびネットワークをセットアップします。さらに、レプリケーション ポリシーを作成し、キャパシティ プランニングを実施します。インフラストラクチャのセットアップが完了したら、VM のレプリケーションを有効にします。特定のマシンのフェールオーバーを実行することも、複数のマシンをフェールオーバーする復旧計画を作成することもできます。

[作業の開始] では、まず、Site Recovery をデプロイする方法を選択します。作業の開始フローは、レプリケーションの要件によって多少変化します。



## ステップ 1: 保護の目標を選択する

レプリケートの対象とレプリケート先を選択します。

1. **[Recovery Services コンテナー]** ブレードでコンテナーを選択し、**[設定]** をクリックします。
2. **[設定]** の **[作業の開始]** で、**[Site Recovery]**、**[手順 1: インフラストラクチャを準備する]**、**[保護の目標]** の順にクリックします。

	![Choose goals](./media/site-recovery-hyper-v-site-to-azure/choose-goals.png)

3. **[Protection goal]** (保護の目標) で、**[To Azure]** (Azure) と **[Yes, with Hyper-V]** (はい、Hyper-V を使用する) の順に選択します。VMM を使用していないことを確認するために、**[いいえ]** を選択します。次に、 **[OK]** をクリックします

	![Choose goals](./media/site-recovery-hyper-v-site-to-azure/choose-goals2.png)


## ステップ 2: ソース環境をセットアップする

Hyper-V サイトをセットアップし、Azure Site Recovery Provider と Azure Recovery Services エージェントを Hyper-V ホストにインストールして、ホストをコンテナーに登録します。


1. **[Step 2: Prepare Infrastructure (手順 2: インフラストラクチャを準備する)]**、**[ソース]** の順にクリックします。Hyper-V ホストまたはクラスターのコンテナーとして、新しい Hyper-V サイトを追加するには、**[+ Hyper-V サイト]** をクリックします。

	![Set up source](./media/site-recovery-hyper-v-site-to-azure/set-source1.png)

2. **[Hyper-V サイトの作成]** ブレードで、サイトの名前を指定します。次に、 **[OK]** をクリックします作成したサイトを選択します。

	![Set up source](./media/site-recovery-hyper-v-site-to-azure/set-source2.png)

3. **[+ Hyper-V Server]** をクリックして、サーバーをサイトに追加します。
4. **[サーバーの追加]** の **[サーバーの種類]** で、**[Hyper-V server (Hyper-V サーバー)]** が表示されていることを確認します。追加する Hyper-V サーバーが[前提条件](#on-premises-prerequisites)に準拠しており、指定された URL にアクセスできることを確認します。
4. Azure Site Recovery プロバイダーのインストール ファイルをダウンロードします。このファイルを実行して、各 Hyper-V ホストにプロバイダーと Recovery Services エージェントの両方をインストールします。
5. 登録キーをダウンロードします。セットアップを実行する際に、これが必要になります。キーは生成後 5 日間有効です。

	![Set up source](./media/site-recovery-hyper-v-site-to-azure/set-source3.png)

6. Hyper-V サイトに追加した各ホストで、プロバイダーのセットアップ ファイルを実行します。Hyper-V クラスターにインストールする場合は、各クラスター ノードでセットアップを実行します。各 Hyper-V クラスター ノードにインストールして登録すると、仮想マシンをノード間で移行した場合も保護が維持されます。

### プロバイダーとエージェントのインストール

1. プロバイダーのセットアップ ファイルを実行します。
2. **[Microsoft Update]** で更新プログラムを登録すると、Microsoft Update ポリシーに従ってプロバイダーの更新プログラムがインストールされます。
3. **[インストール]** で、プロバイダーの既定のインストール先をそのまま使用するか、インストール先を変更して、**[インストール]** をクリックします。
4. **[資格情報コンテナー設定]** ページで **[参照]** をクリックして、ダウンロードしたコンテナー キー ファイルを選択します。Azure Site Recovery のサブスクリプション、コンテナー名、Hyper-V サーバーが属している Hyper-V サイトを指定します。

	![サーバー登録](./media/site-recovery-hyper-v-site-to-azure/provider3.png)

5\. **[プロキシ設定]** で、サーバーにインストールされるプロバイダーからインターネット経由で Azure Site Recovery に接続する方法を指定します。

- プロバイダーから直接接続するようにするには、**[Connect directly without a proxy (プロキシなしで直接接続する)]** を選択します。
- サーバーに現在設定されているプロキシを使って接続する場合は、**[Connect with existing proxy settings (既存のプロキシ設定を使用して接続する)]** を選択します。
- 既存のプロキシに認証が必要な場合、またはプロバイダー接続にカスタム プロキシを使用する場合は、**[Connect with custom proxy settings (カスタム プロキシ設定を使用して接続する)]** を選択します。
- カスタム プロキシを使用する場合、アドレス、ポート、資格情報を指定する必要があります。
- プロキシを使用する場合、[前提条件](#on-premises-prerequisites)に関するセクションで示されている URL が許可されていることを確認します。

	![internet](./media/site-recovery-hyper-v-site-to-azure/provider7.PNG)

6\. インストールが完了したら、**[登録]** をクリックして、サーバーをコンテナーに登録します。![インストール場所](./media/site-recovery-hyper-v-site-to-azure/provider2.png)

7\. 登録が完了すると、Azure Site Recovery によって Hyper-V サーバーからメタデータが取得され、**[設定]**、**[Site Recovery インフラストラクチャ]** の順にアクセスすると、**[Hyper-V Hosts (Hyper-V ホスト)]** ブレードにサーバーが表示されます。


### コマンド ラインを使用したインストール

Azure Site Recovery Provider とエージェントは、次のコマンド ラインを使用してインストールすることもできます。このメソッドを使用すると、Windows Server 2012 R2 の Server Core にプロバイダーをインストールできます。

1. プロバイダーのインストール ファイルと登録キーをフォルダーにダウンロードします。たとえば、C:\\ASR です。
2. 管理者特権のコマンド プロンプトで次のコマンドを実行して、プロバイダーのインストーラーを抽出します。

	    	C:\Windows\System32> CD C:\ASR
	    	C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. 次のコマンドを実行して、コンポーネントをインストールします。

			C:\ASR> setupdr.exe /i

4. 次のコマンドを実行して、サーバーをコンテナーに登録します: CD C:\\Program Files\\Microsoft Azure Site Recovery Provider\\ C:\\Program Files\\Microsoft Azure Site Recovery Provider> DRConfigurator.exe /r /Friendlyname <サーバーのフレンドリ名> /Credentials <資格情報ファイルのパス> ここで、

- **/Credentials**: 登録キー ファイルが配置されている場所を指定する必須パラメーターです。
- **/Friendlyname**: Azure Site Recovery ポータルに表示される、Hyper-V ホスト サーバーの名前を表す必須パラメーターです。
- **/proxyAddress**: 省略可能。プロキシ サーバーのアドレスを指定します。
- **/proxyport**: 省略可能。プロキシ サーバーのポートを指定します。
- **/proxyUsername**: 省略可能。プロキシのユーザー名を指定します (認証が必要なプロキシの場合)。
- **/proxyPassword**: 省略可能。プロキシ サーバーの認証に使用するパスワードを指定します (認証が必要なプロキシの場合)。


## ステップ 3: ターゲット環境をセットアップする

レプリケーションに使用する Azure ストレージ アカウントと、フェールオーバー後に Azure VM が接続する Azure ネットワークを指定します。

1.	**[インフラストラクチャの準備]**、**[ターゲット]** の順にクリックし、使用する Azure サブスクリプションを選択します。
2.	フェールオーバー後に VM に使用するデプロイメント モデルを指定します。
3.	Site Recovery によって、互換性のある Azure ストレージ アカウントとネットワークが 1 つ以上あるかどうかが確認されます。

	![Storage](./media/site-recovery-hyper-v-site-to-azure/select-target.png)

4.	まだストレージ アカウントを作成しておらず、ARM を使用して作成する場合は、**[+ ストレージ アカウント]** をクリックしてインラインで作成します。**[ストレージ アカウントの作成]** ブレードで、アカウント名、種類、サブスクリプション、場所を指定します。アカウントは、Recovery Services コンテナーと同じ場所にある必要があります。

	![Storage](./media/site-recovery-hyper-v-site-to-azure/gs-createstorage.png)

	クラシック モデルを使用してストレージ アカウントを作成する場合は、[Azure ポータル](../storage/storage-create-storage-account-classic-portal.md)で作成できます。
	
5.	まだ Azure ネットワークを作成しておらず、ARM を使用して作成する場合は、**[+ ネットワーク]** をクリックしてインラインで作成します。**[仮想ネットワークの作成]** ブレードで、ネットワーク名、アドレス範囲、サブネットの詳細、サブスクリプション、場所を指定します。ネットワークは、Recovery Services コンテナーと同じ場所にある必要があります。

	![ネットワーク](./media/site-recovery-hyper-v-site-to-azure/gs-createnetwork.png)

	クラシック モデルを使用してネットワークを作成する場合は、[Azure ポータル](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)で作成できます。
	

## ステップ 4: レプリケーション設定をセットアップする

1. 新しいレプリケーション ポリシーを作成するには、**[インフラストラクチャの準備]**、**[レプリケーション設定]**、**[+ 作成と関連付け]** の順にクリックします。

	![ネットワーク](./media/site-recovery-hyper-v-site-to-azure/gs-replication.png)

2. **[ポリシーの作成と関連付け]** で、ポリシー名を指定します。
3. **[コピーの頻度]** で、初期レプリケーションの後、差分データをレプリケートする頻度 (30 秒ごと、5 分ごと、または 15 分ごと) を指定します。
4. **[復旧ポイントのリテンション期間]** で、各復旧ポイントのリテンション期間の長さを時間単位で指定します。保護されたマシンはこの期間内のどのポイントにも復旧できます。
6. **[アプリ整合性スナップショットの頻度]** で、アプリケーション整合性スナップショットを含む復旧ポイントの作成頻度 (1 ～ 12 時間) を指定します。Hyper-V では 2 種類のバックアップを使用します。1 つは標準バックアップで、仮想マシン全体の増分バックアップを実行します。もう 1 つは、アプリケーション整合性スナップショットで、仮想マシン内部のアプリケーション データの特定の時点のスナップショットを作成します。アプリケーション整合性スナップショットでは、ボリューム シャドウ コピー サービス (VSS) を使用して、スナップショットを作成するときにアプリケーションを一貫性のある状態に保ちます。アプリケーション整合性スナップショットを有効にすると、ソースの仮想マシンで実行するアプリケーションのパフォーマンスに影響があります。設定する値は、追加で構成する復旧ポイントの数より少ない数にしてください。
3. **[初期レプリケーションの開始時刻]** で、初期レプリケーションを開始する時刻を指定します。レプリケーションはご利用のインターネット帯域幅で行われるため、トラフィックの多い時間帯を避けるようにスケジュールを設定することをお勧めします。次に、 **[OK]** をクリックします

	![Replication policy](./media/site-recovery-hyper-v-site-to-azure/gs-replication2.png)

新しいポリシーを作成すると、自動的に Hyper-V サイトに関連付けられます。**[OK]** をクリックします。Hyper-V サイト (およびサイト内の VM) を複数のレプリケーション ポリシーに関連付けるには、**[設定]**、**[レプリケーション]**、ポリシー名、**[Associate Hyper-V Site]** (Hyper-V サイトの関連付け) の順にクリックします。

## ステップ 5: キャパシティ プランニング

基本的なインフラストラクチャをセットアップできたので、キャパシティ プランニングを立案し、追加のリソースが必要かどうかを検討できます。

Site Recovery が備えている Capacity Planner を使用して、ソース環境、Site Recovery のコンポーネント、ネットワーク、およびストレージに適切なリソースを割り当てることができます。このキャパシティ プランニング ツールは、VM、ディスク、およびストレージの平均数に基づく見積もりを使用するクイック モードか、ワークロード レベルで数値を入力する詳細モードで実行できます。開始する前に、次のことを行う必要があります。

- VM、VM あたりのディスク数、ディスクあたりのストレージなど、レプリケーション環境の情報を収集する。
- レプリケートされたデータの 1 日の変更 (チャーン) 率を見積もる。この見積もりには、[Capacity Planner for Hyper-V Replica](https://www.microsoft.com/download/details.aspx?id=39057) が役立ちます。

1.	**[ダウンロード]** をクリックしてツールをダウンロードし、実行する。ツールに付随する[こちらの記事をご覧ください](site-recovery-capacity-planner.md)。
2.	作業が完了したら、**[Have you run the Capacity Planner? (Capacity Planner を実行しましたか?)]** で **[はい]** を選択する。

	![容量計画](./media/site-recovery-hyper-v-site-to-azure/gs-capacity-planning.png)

### ネットワーク帯域幅に関する考慮事項

Capacity Planner を使用して、レプリケーション (初期レプリケーションとその後の差分レプリケーション) に必要な帯域幅を計算できます。レプリケーションの帯域幅の使用量を制御する方法には、いくつかのオプションがあります。

- **帯域幅を調整する**: Azure にレプリケートされる Hyper-V トラフィックは、特定の Hyper-V ホストを経由します。このホスト サーバーの帯域幅をスロットルできます。
- **帯域幅の微調整**: いくつかのレジストリ キーを使用して、レプリケーションに使用される帯域幅に影響を与えることができます。

#### スロットルの帯域幅

1. Hyper-V ホスト サーバーの Microsoft Azure Backup MMC スナップインを開きます。既定では、Microsoft Azure Backup のショートカットが、デスクトップか C:\\Program Files\\Microsoft Azure Recovery Services Agent\\bin\\wabadmin にあります。
2. スナップインで **[プロパティの変更]** をクリックします。
3. **[調整]** タブで、**[バックアップ操作用のインターネット使用帯域幅の調整を有効にする]** チェック ボックスをオンにし、勤務時間用と勤務時間外用の制限値を設定します。有効な範囲は、1 秒あたり 512 Kbps ～ 102 Mbps です。

	![スロットルの帯域幅](./media/site-recovery-hyper-v-site-to-azure/throttle2.png)

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

## ステップ 6: レプリケーションを有効にする

レプリケーションを有効にするには、次の手順に従います。

1. **[手順 2: アプリケーションをレプリケートする]**、**[ソース]** の順にクリックします。レプリケーションを初めて有効にした後は、コンテナーで **[+ レプリケート]** をクリックして、追加のマシンのレプリケーションを有効にします。

	![Enable replication](./media/site-recovery-hyper-v-site-to-azure/enable-replication.png)

2. **[ソース]** ブレードで、Hyper-V サイトを選択します。次に、 **[OK]** をクリックします
3. **[ターゲット]** で、コンテナーのサブスクリプションと、フェールオーバー後に Azure で使用するフェールオーバー モデル (クラシックまたは Resource Manager) を選択します。
4. 使用するストレージ アカウントを選択します。現在持っているものとは別のストレージ アカウントを使用する場合は、[ストレージ アカウントを作成](#set-up-an-azure-storage-account)できます。ARM モデルを使用してストレージ アカウントを作成するには、**[新規作成]** をクリックします。クラシック モデルを使用してストレージ アカウントを作成する場合は、[Azure ポータル](../storage/storage-create-storage-account-classic-portal.md)で作成できます。次に、 **[OK]** をクリックします
5.  フェールオーバー後に Azure VM がスピンアップされたときに接続する Azure ネットワークとサブネットを選択します。保護の対象として選択したすべてのマシンにネットワーク設定を適用する場合は、**[選択したマシン用に今すぐ構成します。]** を選択します。マシンごとに Azure ネットワークを選択する場合は、**[後で構成する]** を選択します。現在あるものとは別のネットワークを使用する場合は、[ネットワークを作成](#set-up-an-azure-network)できます。ARM モデルを使用してネットワークを作成するには、**[新規作成]** をクリックします。クラシック モデルを使用してネットワークを作成する場合は、[Azure ポータル](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)で作成します。該当する場合は、サブネットを選択します。次に、 **[OK]** をクリックします

	![Enable replication](./media/site-recovery-hyper-v-site-to-azure/enable-replication11.png)

6. **[仮想マシン]** の **[仮想マシンの選択]** で、レプリケートする各マシンをクリックして選択します。選択できるのは、レプリケーションを有効にできるマシンのみです。次に、 **[OK]** をクリックします

	![Enable replication](./media/site-recovery-hyper-v-site-to-azure/enable-replication5.png)

11. **[プロパティ]** の **[プロパティの構成]** で、選択した VM のオペレーティング システムと OS ディスクを選択します。Azure VM の名前 (ターゲット名) が [Azure 仮想マシンの要件](site-recovery-best-practices.md#azure-virtual-machine-requirements)に準拠していることを確認し、必要に応じて変更します。次に、 **[OK]** をクリックします後で追加のプロパティを設定できます。

	![Enable replication](./media/site-recovery-hyper-v-site-to-azure/enable-replication6.png)

12. **[レプリケーションの設定]** の **[レプリケーション設定の構成]** で、保護対象の VM に適用するレプリケーション ポリシーを選択します。次に、 **[OK]** をクリックしますレプリケーション ポリシーを変更するには、**[設定]**、**[レプリケーション ポリシー]**、ポリシー名、**[設定の編集]** の順にクリックします。適用する変更は、既にレプリケートしているマシンと、新しいマシンに使用されます。

	![Enable replication](./media/site-recovery-hyper-v-site-to-azure/enable-replication7.png)

**[設定]**、**[ジョブ]**、**[Site Recovery jobs (Site Recovery ジョブ)]** の順にクリックして、**保護の有効化**ジョブの進行状況を追跡できます。**保護の最終処理**ジョブが実行されると、マシンはフェールオーバーできる状態になります。

### VM プロパティを表示して管理する

ソース マシンのプロパティを確認することをお勧めします。

1. **[設定]**、**[保護された項目]**、**[Replicated Items]** (レプリケートされた項目) の順にクリックし、マシンを選択します。

	![Enable replication](./media/site-recovery-hyper-v-site-to-azure/test-failover1.png)

2. **[プロパティ]** で、VM のレプリケーションとフェールオーバーの情報を確認できます。

	![Enable replication](./media/site-recovery-hyper-v-site-to-azure/test-failover2.png)

3. **[計算とネットワーク]** の **[計算のプロパティ]** で、Azure VM の名前とターゲットのサイズを指定できます。必要に応じて、Azure の要件に準拠するように名前を変更します。Azure VM に割り当てられるターゲット ネットワーク、サブネット、および IP アドレスに関する情報を表示し、変更することもできます。以下の点に注意してください。

	- ターゲット IP アドレスを設定できます。アドレスを指定しなかった場合、フェールオーバーされたマシンで DHCP が使用されます。フェールオーバーで使用できないアドレスが設定された場合、フェールオーバーは失敗します。テスト フェールオーバー ネットワークのアドレスを利用できる場合、テスト フェールオーバーに同じターゲット IP アドレスを使用できます。
	- ネットワーク アダプターの数は、次に示すように、ターゲット仮想マシンに指定したサイズによって異なります。

		- ソース マシン上のネットワーク アダプターの数が、ターゲット マシンのサイズに許可されているアダプターの数以下の場合、ターゲットのアダプターの数は、ソースと同じになります。
		- ソース仮想マシン用のアダプターの数が、ターゲットのサイズに許可されている数を超える場合は、ターゲットの最大サイズが使用されます。
		- たとえば、ソース マシンに 2 つのネットワーク アダプターがあり、ターゲット マシンのサイズが 4 つをサポートしている場合は、ターゲット マシンのアダプターの数は、2 つになります。ソース マシンに 2 つのアダプターがあるが、サポートされているターゲット サイズで 1 つしかサポートしていない場合、ターゲット マシンのアダプターの数は 1 つだけになります。
		- VM に複数のネットワーク アダプターがある場合は、すべて同じネットワークに接続されます。

	![Enable replication](./media/site-recovery-hyper-v-site-to-azure/test-failover4.png)

5.	**[ディスク]** で、レプリケートされる VM のオペレーティング システム ディスクとデータ ディスクを確認できます。


## ステップ 7: デプロイをテストする

デプロイをテストするために、単一の仮想マシンに対して、または 1 つ以上の仮想マシンを含む復旧計画に対してテスト フェールオーバーを実行できます。


### テスト フェールオーバーを準備する

- テスト フェールオーバーを実行する場合は、Azure 実稼働用ネットワークから分離された Azure ネットワークを新たに作成する必要があります (これは Azure で新しいネットワークを作成する場合の既定の動作です)。テスト フェールオーバーの実行の詳細については、[こちら](site-recovery-failover.md#run-a-test-failover)をご覧ください。
- Azure へのフェールオーバーを実行するときに最適なパフォーマンスを得るには、保護されたマシンに Azure エージェントをインストールします。エージェントをインストールすると、起動時間が短縮され、トラブルシューティングにも役立ちます。[Linux](https://github.com/Azure/WALinuxAgent) エージェントまたは [Windows](http://go.microsoft.com/fwlink/?LinkID=394789) エージェントをインストールします。
- デプロイメントを完全にテストするには、レプリケートされたマシンが正常に動作するインフラストラクチャが必要です。Active Directory と DNS をテストする場合は、ドメイン コントローラー兼 DNS として仮想マシンを作成し、これを Azure Site Recovery を使用して Azure にレプリケートします。Active Directory のテスト フェールオーバーの考慮事項については、[こちら](site-recovery-active-directory.md#considerations-for-test-failover)をご覧ください。
- テスト フェールオーバーではなく計画されていないフェールオーバーを実行する場合は、次の点に注意してください。

	- 可能であれば、プライマリ マシンをシャットダウンしてから、計画されていないフェールオーバーを実行します。こうすることで、ソース マシンとレプリカ マシンが同時に実行されないように確保できます。
	- 計画されていないフェールオーバーを実行すると、プライマリ マシンのデータ レプリケーションが停止され、計画されていないフェールオーバーの開始後に、データの差分は転送されなくなります。また、復旧計画で計画されていないフェールオーバーを実行する場合は、エラーが発生した場合でも、復旧計画は最後まで実行されます。
	
### フェールオーバー後に Azure VM に接続するための準備をする

フェールオーバー後に RDP を使用して Azure VM に接続する場合は、次のことを行う必要があります。

**フェールオーバー前にオンプレミスのマシンで行う操作**:

- インターネット経由のアクセスで RDP を有効にする場合は、TCP と UDP の規則が **[パブリック]** に追加されていることを確認し、すべてのプロファイルについて、**[Windows ファイアウォール]** の **[許可されたアプリおよび機能]** で RDP が許可されていることを確認します。
- マシンでサイト間接続経由のアクセスに対して RDP を有効にする場合は、**[ドメイン]** および **[プライベート]** ネットワークについて、**[Windows ファイアウォール]** の **[許可されたアプリおよび機能]** で RDP が許可されていることを確認します。
- オンプレミスのマシンに [Azure VM エージェント](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)をインストールします。
- オペレーティング システムの SAN ポリシーが OnlineAll に設定されていることを確認します。[詳細情報](https://support.microsoft.com/kb/3031135)
- フェールオーバーを実行する前に、IPSec サービスを無効にします。

**フェールオーバー後に Azure VM で行う操作**:

- Azure VM に関連付けられている NIC にパブリック IP アドレスを追加して、RDP を許可します。
- パブリック アドレスを使用する仮想マシンへの接続を妨げるドメイン ポリシーを使用していないことを確認します。
- 接続を試みます。接続できない場合は、VM が実行されていることを確認します。トラブルシューティングのヒントについては、[こちらの記事](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)をご覧ください。

フェールオーバー後に Linux が実行されている Azure VM に Secure Shell クライアント (ssh) を使用してアクセスする場合は、次の手順に従います。

**フェールオーバー前にオンプレミスのマシンで行う操作**:

- Azure VM 上の Secure Shell サービスがシステム起動時に自動的に開始されるよう設定されていることを確認します。
- ファイアウォール規則で SSH 接続が許可されていることを確認します。

**フェールオーバー後に Azure VM で行う操作**:

- フェールオーバーされた VM および接続先の Azure サブネットのネットワーク セキュリティ グループの規則で、SSH ポートへの着信接続を許可する必要があります。
- SSH ポート (既定では TCP ポート 22) で着信接続を許可するようにパブリック エンドポイントが作成されている必要があります。
- VM に VPN 接続 (ExpressRoute またはサイト間 VPN) を介してアクセスする場合は、クライアントを使用して SSH 経由で直接 VM に接続できます。

### テスト フェールオーバーの実行

テスト フェールオーバーを実行するには、次の手順に従います。

1. 1 つの VM をフェールオーバーする場合は、**[設定]** の **[レプリケートされたアイテム]** で、VM をクリックして **[+ テスト フェールオーバー]** をクリックします。

	![テスト フェールオーバー](./media/site-recovery-hyper-v-site-to-azure/run-failover1.png)

2. 復旧計画をフェールオーバーする場合は、**[設定]** の **[復旧計画]** で、計画を右クリックし、**[テスト フェールオーバー]** をクリックします。復旧計画を作成する場合は、[こちらの手順に従ってください](site-recovery-create-recovery-plans.md)。

3. **[テスト フェールオーバー]** で、フェールオーバー後に Azure VM が接続する Azure ネットワークを選択します。

	![テスト フェールオーバー](./media/site-recovery-hyper-v-site-to-azure/run-failover2.png)

4. **[OK]** をクリックすると、フェールオーバーが開始されます。進行状況を追跡するには、VM をクリックしてプロパティを開くか、**[設定]** の **[Site Recovery jobs]** (Site Recovery ジョブ) で **[テスト フェールオーバー]** をクリックします。
5. フェールオーバーが **[テストの完了]** フェーズに達したら、次の手順に従います。
	1. Azure ポータルで、レプリカ仮想マシンを表示します。仮想マシンが正常に起動することを確認します。
	2. オンプレミスのネットワークから、仮想マシンへのアクセスをセットアップすると、仮想マシンへのリモート デスクトップ接続を開始できます。
	3. **[テストの完了]** をクリックして終了します。
	4. **[メモ]** をクリックして、テスト フェールオーバーに関連する監察結果をすべて記録し、保存します。
	5. **[テスト フェールオーバーが完了しました]** をクリックします。テスト環境をクリーンアップして、自動的に電源をオフにし、テスト仮想マシンを削除します。
	6. この段階で、テスト フェールオーバー時に Site Recovery によって自動的に作成されたすべての要素または VM は削除されます。テスト フェールオーバー用に作成した追加の要素は削除されません。
	
	> [AZURE.NOTE] テスト フェールオーバーの実行時間が 2 週間を超えた場合は、強制的に終了されます。

6. フェールオーバーの完了後は、Azure ポータルの **[仮想マシン]** にレプリカの Azure マシンも表示されるようになります。VM が適切なサイズであること、適切なネットワークに接続していること、実行されていることを確認する必要があります。
7. [フェールオーバー後の接続の準備](#prepare-to-connect-to-Azure-VMs-after-failover)が完了したら、Azure VM に接続できます。


## デプロイを監視する

Site Recovery デプロイメントの構成設定、状態、および正常性を監視する方法を次に示します。

1. コンテナー名をクリックして、**[要点]** ダッシュボードにアクセスします。このダッシュボードで、Site Recovery ジョブ、レプリケーションの状態、復旧計画、サーバーの状態、およびイベントを確認できます。[要点] ダッシュボードをカスタマイズして、他の Site Recovery コンテナーや Backup コンテナーの状態など、ニーズに最適なタイルとレイアウトを表示できます。

	![Essentials](./media/site-recovery-hyper-v-site-to-azure/essentials.png)

2. **[正常性]** タイルで、問題が発生しているサイト サーバーと、Site Recovery によって過去 24 時間以内に生成されたイベントを監視できます。
3. **[レプリケートされたアイテム]**、**[復旧計画]**、**[Site Recovery ジョブ]** の各タイルで、レプリケーションの管理と監視を実行できます。**[設定]**、**[ジョブ]**、**[Site Recovery jobs (Site Recovery ジョブ)]** の順にクリックすると、ジョブの詳細を確認できます。





## 次のステップ

デプロイをセットアップし、実行状態にできたら、各種フェールオーバーの[詳細を確認](site-recovery-failover.md)します。

<!---HONumber=AcomDC_0921_2016-->