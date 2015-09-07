<properties
	pageTitle="Azure VM 上の SharePoint Server 2010 | Microsoft Azure"
	description="Azure 仮想マシンでの SharePoint の値、詳細な手順、および SharePoint 2010 の使用に関するサポートされるシナリオについて説明します。"
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sharepoint"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/09/2015"
	ms.author="josephd"/>


# Azure 仮想マシンへの SharePoint 2010 のデプロイ

Microsoft SharePoint Server 2010 は高度に柔軟なデプロイを行い、ビジネスのニーズと目標に沿った適切なデプロイ シナリオを決定するのに役立ちます。Microsoft Azure でホストされ管理される Azure Virtual Machines は、Microsoft SQL Server や SharePoint デプロイのように、さまざまなオンデマンド アプリケーションやデータベース ワークロードをサポートする、信頼性が高く実際に利用できる総合的なインフラストラクチャを実現します。

Azure Virtual Machines は複数のワークロードをサポートしますが、この記事では SharePoint のデプロイを取り上げます。Azure の仮想マシンを使用すると、SharePoint インフラストラクチャをすばやく作成して管理できるだけでなく、ほぼすべてのホストを対象として汎用的にプロビジョニングやアクセスを行えます。プロセッサ、RAM、CPU 範囲などの SharePoint 仮想マシン リソースを全面的にコントロールして管理できます。

Azure の仮想マシンを活用すると、ハードウェアを用意する必要性が減少するため、高い事前コストや複雑な処理に対処するのではなく、規模に応じてインフラストラクチャを構築し管理することに目を向けることができます。つまり、従来のデプロイのように日単位や週単位ではなく、時間単位で技術革新、実験、反復作業ができるようになります。

> [AZURE.NOTE]Azure に SharePoint 2013 をデプロイする方法の詳細については、「[Azure インフラストラクチャ サービスでの SharePoint 2013 の計画](https://msdn.microsoft.com/library/dn275958.aspx)」および [Azure インフラストラクチャ サービスでホストされている SharePoint ファーム](virtual-machines-sharepoint-infrastructure-services.md)に関するページを参照してください。

## Azure 仮想マシン上の SharePoint Server

SharePoint 2010 は、Azure の仮想マシンにデプロイしても、ほとんどのワークロードを柔軟にサポートします。Azure の仮想マシンは、FIS (SharePoint Server for Internet Sites) シナリオおよび開発シナリオに最も適しています。中核的な SharePoint ワークロードもサポートされています。クラウド インフラストラクチャの仮想化オプションを利用しながら独自の SharePoint 2010 実装を管理してコントロールする場合は、Azure の仮想マシンがデプロイ先として最適です。

Azure の仮想マシンはクラウドでホストされ管理されます。柔軟にデプロイでき、ハードウェア調達に伴う設備投資が減るために、コストが削減されます。インフラストラクチャの俊敏性が向上するため、SharePoint Server を数日や数週間ではなく、数時間でデプロイできます。Azure の仮想マシンでは、"従量課金制" モデルを使ってクラウドに SharePoint ワークロードをデプロイすることもできます。SharePoint のワークロードの増大にあわせて、インフラストラクチャを急速に拡張できます。コンピューティングのニーズが減少した場合は、使用しなくなったリソースを返却し、使用する分についてのみ料金を支払うことができます。

### IT の焦点の推移

多くの組織は、ハードウェアやオペレーティング システム、セキュリティ、データ ストレージ、バックアップのような IT インフラストラクチャの共通コンポーネントおよび管理を外注しても、SharePoint Server のような基幹業務系アプリケーションの管理は組織内に残します。IT プラットフォームの基幹業務系でないサービス レイヤーをすべて仮想プロバイダーに委任することで、IT の焦点を中核的な基幹業務系の SharePoint サービスに移し、インフラストラクチャのセットアップに時間をかける代わりに、SharePoint プロジェクトを使ってビジネス価値を実現できます。

### デプロイの高速化

大規模な SharePoint インフラストラクチャをサポートしてデプロイすると、IT 部門が迅速に行動してビジネス要件をサポートできなくなることがあります。SharePoint サーバーおよびファームの構築、テスト、準備を実行し、運用環境にデプロイするのに必要な時間は、組織のプロセスおよび制約に応じて数週間、ときには数か月かかることがあります。Azure の仮想マシンでは、ハードウェアに設備投資しなくても、SharePoint ワークロードを迅速にデプロイすることができます。こうすることでインフラストラクチャの俊敏性を利用して、数日や数週間ではなく、数時間でデプロイできます。

### 拡張性

SharePoint の物理サーバーとファームをデプロイ、テスト、および準備しなくても、コンピューティング容量を一瞬のうちにオンデマンドで拡張して契約できます。SharePoint のワークロード要件の増大にあわせて、クラウド インフラストラクチャを急速に拡張できます。同様に、コンピューティングのニーズが減少すれば、リソースを縮小して使用している分だけ料金を支払うことができます。Azure の仮想マシンを使用すると、前払いの経費と長期契約が減り、SharePoint インフラストラクチャを規模に応じて構築して管理できます。この場合も、従来のデプロイを使用した日単位や週単位ではなく、時間単位で技術革新、実験、反復作業ができるようになります。

### 使用量の測定

Azure の仮想マシンには、SharePoint シナリオ用のコンピューティング パワー、メモリ、ストレージが用意されており、その価格は一般にリソースの消費量に基づいて決められます。使用した分だけ料金を支払えばよく、SharePoint インフラストラクチャを実行するのに必要な容量はすべて提供されます。料金と課金の詳細については、[料金の概要ページ](http://azure.microsoft.com/pricing/)を参照してください。Azure からオンプレミスのネットワークにストレージとデータを移動すると、料金が若干掛かります。ただし Azure の場合、データのアップロードには料金がかかりません。

### 柔軟性

Azure の仮想マシンでは、開発者は目的の言語またはランタイム環境を柔軟に選択でき、.NET、Node.js、Java、および PHP が正式にサポートされています。開発者はツールを選択することもでき、Microsoft Visual Studio、WebMatrix、Eclipse、およびテキスト エディターがサポートされています。さらに、Microsoft は低コストで低リスクのクラウド移行パスを用意しており、クラウド レポートのニーズを満たす経済的で簡単なプロビジョニングとデプロイが可能であり、各種デバイスと場所にまたがってビジネス インテリジェンス (BI) にアクセスすることができます。最後に、Azure では、ユーザーは VHD をクラウドに移動できるだけでなく、適切なラインセンスを持っている限り、VHD をコピーして、それをローカルに実行したり、別のクラウド プロバイダー経由で実行したりできます。

## プロビジョニング プロセス

Azure のイメージ ライブラリには、利用できる構成済みの仮想マシンが一覧表示されています。ユーザーは、SharePoint Server、SQL Server、Windows Server などの ISO/VHD をイメージ ライブラリに発行できます。仮想マシンの作成を単純にするために、作成された基本イメージはライブラリに発行されます。正規ユーザーはこのイメージを使用して目的の仮想マシンを生成できます。詳細については、[Azure プレビュー ポータルで Windows を実行する仮想マシンを作成する方法](virtual-machines-windows-tutorial.md)に関するページを参照してください。図 1 は、Azure ポータルを使用して仮想マシンを作成する基本的な手順を示しています。

**図 1: 仮想マシンの作成手順の概要**

ユーザーは Azure 管理ポータルに sysprep 済みイメージをアップロードすることもできます。詳細については、「[Windows Server VHD の作成と Azure へのアップロード](virtual-machines-create-upload-vhd-windows-server.md)」を参照してください。図 2 は、イメージをアップロードして VM を作成する基本的な手順を示しています。

Azure ポータルでは、SysPrep ツールが既に実行されている場所にイメージをアップロードできます。詳細については、「[Windows Server VHD の作成と Azure へのアップロード](virtual-machines-create-upload-vhd-windows-server.md)」を参照してください。図 2 は、イメージをアップロードして仮想マシンを作成する基本的な手順を示しています。

**図 2: イメージのアップロード手順の概要**

## Azure への SharePoint 2010 のデプロイ 

SharePoint 2010 を Azure にデプロイするには、次の手順に従います。

1. Azure サブスクリプション アカウントを使用して [Azure 管理ポータル](http://manage.windowsazure.com/)にサインインします。Azure アカウントを持っていない場合は、[Azure の無料試用版にサインアップしてください](http://azure.microsoft.com/pricing/free-trial/)。
2. 基本オペレーティング システムの仮想マシンを作成するには、Azure ポータルで、**[新規]、[コンピューティング]、[仮想マシン]、[ギャラリーから]** の順にクリックします。
3. **[イメージの選択]** ダイアログ ボックスが表示されます。**[Windows Server 2008 R2 SP1]** プラットフォーム イメージをクリックし、右矢印をクリックします。
4. **[仮想マシンの構成]** ダイアログ ボックスが表示されます。次の情報を指定します。
	- **[仮想マシン名]** ボックスに入力します。
	- 適切なサイズを選択します。運用環境の場合 (SharePoint アプリケーション サーバーおよびデータベース)、A3 (4 コア、7 GB メモリ) 以上を使用することをお勧めします。
	- **[新しいユーザー名]** に、ローカル管理者アカウント名を入力します。
	- **[新しいパスワード]** に、強力なパスワードを入力します。
	- **[確認]** に、パスワードを再度入力し、右矢印をクリックします。
5. 2 つ目の **[仮想マシンの構成]** ダイアログ ボックスが表示されます。次の情報を指定します。
	- **[クラウド サービス]** で、**[新しいクラウド サービスの作成]** を選択するか (こちらを選択する場合は、クラウド サービスの DNS 名を入力する必要があります)、既存のクラウド サービスを選択します。
	- **[リージョン/アフィニティ グループ/仮想ネットワーク]** で、仮想イメージをホストするリージョンを選択します。
	- **[ストレージ アカウント]** で、**[自動的に生成されたストレージ アカウントを使用]** をクリックするか、既存のストレージ アカウント名を選択します。自動的に作成されるストレージ アカウントはリージョンあたり 1 つだけです。この設定で作成する他のすべての仮想マシンがこのストレージ アカウントに配置されます。ストレージ アカウントは 20 個に制限されています。詳細については、「[Azure にストレージ アカウントを作成する](virtual-machines-create-upload-vhd-windows-server.md#step-2-create-a-storage-account-in-azure)」を参照してください。
	- **[可用性セット]** の **[(なし)]** を選択し、右矢印をクリックします。
6. 3 つ目の **[仮想マシンの構成]** ダイアログ ボックスで、チェックマークをクリックして仮想マシンを作成します。

仮想マシンへの接続方法については、「[Windows Server が実行されている仮想マシンにログオンする方法](virtual-machines-log-on-windows-server.md)」を参照してください。

次のいずれかのオプションを使用して、SQL Server 仮想マシンを構築します。

- 上記の手順 1. から手順 7. に従って、SQL Server 2012 仮想マシンを作成します。ただし、手順 3. では、Windows Server 2008 R2 SP1 イメージの代わりに SQL Server 2012 イメージを使用します。詳細については、「[Azure での SQL Server 仮想マシンのプロビジョニング](virtual-machines-provision-sql-server.md)」をご覧ください。
	- このオプションを選択した場合は、プロビジョニング プロセスで SQL Server 2012 セットアップ ファイルのコピーが C:\\SQLServer\_11.0\_Full ディレクトリに残されるため、インストールをカスタマイズできます。たとえば、ライセンス キーを使用して SQL Server 2012 の評価版をライセンス版に変換できます。

- SQL Server システム準備 (SysPrep) ツールを使用して、基本オペレーティング システムの仮想マシンに SQL Server をインストールします (上記の手順 1. ～ 7. を参照)。詳細については、「[SysPrep を使用した SQL Server 2012 のインストール](http://msdn.microsoft.com/library/ee210664.aspx)」を参照してください。

- コマンド プロンプトを使用して SQL Server をインストールします。詳細については、「[コマンド プロンプトからの SQL Server 2012 のインストール](http://msdn.microsoft.com/library/ms144259.aspx#SysPrep)」を参照してください。

- サポートされている SQL Server メディアとライセンス キーを使用して、基本オペレーティング システムの仮想マシンに SQL Server をインストールします (上記の手順 1. ～ 7. を参照)。

次の手順に従って、SharePoint ファームを構築します。

手順 1.スクリプト ファイルを使用して Azure サブスクリプションを構成します。

手順 2.基本オペレーティング システムの別の仮想マシンを作成して (上記の手順 1. ～ 6. を参照)、SharePoint Server をプロビジョニングします。この仮想マシン上に SharePoint Server を構築するには、次のいずれかのオプションを選択します。

- SharePoint GUI を使用してプロビジョニングします。
	- SharePoint ファームを作成してプロビジョニングするには、[Microsoft SharePoint Server ファームの作成に関するページ](http://technet.microsoft.com/library/ee805948.aspx#CreateConfigure)を参照してください。
	- ファームに Web またはアプリケーション サーバーを追加するには、「[Add a web or application server to the farm (Web サーバーまたはアプリケーション サーバーをファームに追加する)](http://technet.microsoft.com/library/cc261752.aspx)」を参照してください。
	- データベース サーバーを既存のファームに追加するには、「[SharePoint 2013 の既存のファームにデータベース サーバーを追加する](http://technet.microsoft.com/library/cc262781)」を参照してください。
	- SharePoint ファームで SQL Server 2012 を使用するには、アプリケーションをインストールした後、サーバーの構成はしないで、SharePoint Server 2010 の Service Pack 1 をダウンロードしてインストールする必要があります。詳細については、「[SharePoint Server 2010 Service Pack 1](http://www.microsoft.com/download/details.aspx?id=26623)」を参照してください。
	- SQL Server の BI 機能を活用するには、SharePoint Server をスタンドアロン サーバーではなくサーバー ファームとしてインストールすることをお勧めします。詳細については、「[SQL Server 2012 のビジネス インテリジェンス機能のインストール](http://technet.microsoft.com/library/hh231681.aspx)」を参照してください。

- Windows PowerShell を使用してプロビジョニングします。Psconfig コマンド ライン ツールを代わりに使用して、SharePoint 2010 製品のプロビジョニング方法を制御するいくつかの操作を実行できます。詳細については、「[Psconfig コマンドライン リファレンス](http://technet.microsoft.com/library/cc263093.aspx)」を参照してください。

手順 3.SharePoint を構成します。各 SharePoint 仮想マシンの準備が完了したら、次のいずれかの方法で各サーバーで SharePoint Server を構成します。

- GUI 画面で SharePoint を構成します。
- Windows PowerShell を使用して SharePoint を構成します。詳細については、「[Windows PowerShell を使用して SharePoint Server 2010 をインストールする](http://technet.microsoft.com/library/cc262839.aspx)」を参照してください。
- CodePlex プロジェクトの AutoSPInstaller を使用することもできます。これは Windows PowerShell スクリプト、XML 入力ファイル、Microsoft Windows 標準バッチ ファイルから構成されています。AutoSPInstaller は、Windows PowerShell に基づいた SharePoint 2010 インストール スクリプトのフレームワークです。詳細については、[CodePlex の AutoSPInstaller](http://autospinstaller.codeplex.com/) のページを参照してください。

手順 4.スクリプトの実行が完了したら、仮想マシン ダッシュボードを使用して仮想マシンに接続します。

SharePoint の構成を確認するために、SharePoint Server にサインインして、[サーバーの全体管理] を使用します。

> [AZURE.NOTE]必ず Azure ポータル エンドポイントでセキュリティを構成し、仮想マシンの Windows ファイアウォールで着信ポートを設定してください。その後、管理者の資格情報で Windows PowerShell セッションを開いて、SharePoint アプリケーション サーバーの 1 つに対してリモート Windows PowerShell セッションを開始できることを確認します。

### 仮想ハード ディスクの作成とアップロード

独自のイメージを作成してそれを Azure に VHD ファイルとしてアップロードすることもできます。詳細については、「[Windows Server VHD の作成と Azure へのアップロード](virtual-machines-create-upload-vhd-windows-server.md)」を参照してください。

## 使用シナリオ

このセクションでは、Azure の仮想マシンを使用した SharePoint デプロイの主な顧客シナリオについて説明します。

### シナリオ 1: 単純な SharePoint 開発およびテスト環境

オンショアまたはオフショア開発とテストのために、SharePoint アプリケーションを作成して SharePoint 環境をセットアップするもっと俊敏な方法が求められています。基本的に、求められているのは、SharePoint アプリケーション開発プロジェクトをセットアップするのにかかる時間を短縮し、テスト環境の利用を増やしてコストを削減することです。たとえば、異なる地域にいる同時ユーザー数を増やして、SharePoint Server でオンデマンド負荷テストを実行し、ユーザー受け入れテスト (UAT) を実行する場合です。同様に、オンショアまたはオフショアのチームを統合することが、現代の多くの企業にとってますます重要なビジネス ニーズになっています。

このシナリオでは、開発およびテスト ワークロードのために構成済み SharePoint ファームをどのように利用できるかを説明します。SharePoint デプロイ トポロジは、オンプレミスの仮想化されたデプロイと見かけはまったく同じです。既存の IT スキルは 1 対 1 で Azure の仮想マシンのデプロイに応用でき、設備投資から運用経費にコストをほぼ完全に移転できるということが大きな利点です。物理サーバーを前払いで購入する必要はありません。サーバー ハードウェアの設備投資をゼロにして、テストおよび開発環境向けに SharePoint ファームを作成しセットアップし拡張するのに必要なプロビジョニング時間を大幅に短縮することで柔軟性を実現できます。IT 部門は容量を大幅に増減して、テストおよび開発の変化するニーズに対応できます。さらに、IT 部門は SharePoint プロジェクトによってビジネス価値を実現することにいっそう力を注ぎ、インフラストラクチャの管理に手をかけないで済ますことができます。

負荷テスト コンピューターを全面的に活用するために、Azure で Windows Sever 2008 R2 オペレーティング システムをサポートする SharePoint の仮想化された開発およびテスト コンピューターを構成することができます。これにより開発チームは、アプリケーションを作成してテストし、コードを変更することなく、オンプレミスまたはクラウドの運用環境に簡単に移行できるようになります。同じフレームワークとツールセットをオンプレミスの環境でもクラウド環境でも利用できるため、分散したチームが同じ環境にアクセスできます。また、VPN 接続を直接確立して、オンプレミスのシステムのデータとアプリケーションにアクセスすることもできます。

図 3 は、Azure 仮想マシンの SharePoint 開発環境とテスト環境を示しています。このデプロイを構築するには、まず、アプリケーション開発に使用しているオンプレミスの環境と同じ SharePoint 開発環境およびテスト環境を使用します。次に、テストと開発のためにアプリケーションを Azure 仮想マシンにアップロードしてデプロイします。アプリケーションをオンプレミスの環境に戻すことを決めた場合は、アプリケーションを修正しなくても戻すことができます。

**図 3: Azure の仮想マシンの SharePoint 開発環境とテスト環境**

SharePoint 開発環境とテスト環境を Azure に実装するには、次の手順に従います。

1. プロビジョニング: まず、Azure の仮想ネットワークを使用してオンプレミスのシステムおよび Azure との VPN 接続をプロビジョニングします (ここでは Active Directory を使用していないため、VPN トンネルが必要です)。 詳細については、「[仮想ネットワークの概要](../virtual-network/virtual-networks-overview.md)」を参照してください。次に、Azure ポータルで、イメージ ギャラリーにあるストック イメージを使用して、新しい仮想マシンをプロビジョニングします。
	- オンプレミスの SharePoint 開発およびテスト仮想マシンを Azure のストレージ アカウントにアップロードして、その仮想マシンをイメージ ライブラリから参照して必要な環境を構築することができます。
	- Windows Server 2008 R2 SP1 イメージの代わりに SQL Server 2012 イメージを使用できます。詳細については、「[Azure での SQL Server 仮想マシンのプロビジョニング](virtual-machines-provision-sql-server.md)」をご覧ください。

2. インストール: リモート デスクトップ接続を使用して、SharePoint Server、Visual Studio、および SQL Server を仮想マシンにインストールします。
	- SharePoint 2010 簡易セットアップ スクリプトを使用して、SharePoint 開発マシンを構築します。詳細については、「[SharePoint 2010 Easy Setup Script (SharePoint 2010 簡易セットアップ スクリプト)](http://www.microsoft.com/download/details.aspx?id=23415)」を参照してください。Windows PowerShell を使用します。詳細については、「[Windows PowerShell を使用して SharePoint Server 2010 をインストールする](http://technet.microsoft.com/library/cc262839.aspx)」を参照してください。CodePlex プロジェクトの AutoSPInstaller を使用します。詳細については、[CodePlex の AutoSPInstaller](http://autospinstaller.codeplex.com/) のページを参照してください。
	- Visual Studio をインストールします。詳細については、「[Visual Studio のインストール](http://msdn.microsoft.com/library/e2h7fzkw.aspx)」を参照してください。
	- SQL Server をインストールします。詳細については、「[SysPrep を使用した SQL Server 2014 のインストール](http://msdn.microsoft.com/library/ee210664.aspx)」を参照してください。
3. アプリケーションとデータベースのためのデプロイ パッケージとスクリプトの開発: イメージ ライブラリにある仮想マシンを使用する場合は、目的のオンプレミスのアプリケーションとデータベースを Azure Virtual Machines にデプロイできます。
	- SQL Server Data Tools と Visual Studio を使用して、既存のオンプレミスのアプリケーションとデータベースのためのデプロイ パッケージを作成します。
	- このパッケージを使用して、アプリケーションとデータベースを Azure の仮想マシンにデプロイします。
4. SharePoint アプリケーションとデータベースのデプロイ:
	- Azure ポータル エンドポイントでセキュリティを構成し、仮想マシンの Windows ファイアウォールで着信ポートを設定します。
	- 手順 3. で作成したデプロイ パッケージとスクリプトを使用して、SharePoint アプリケーションとデータベースを Azure の仮想マシンにデプロイします。
- デプロイしたアプリケーションとデータベースをテストします。
5. 仮想マシンの管理
	- Azure ポータルを使用して仮想マシンを監視します。
	- Visual Studio と SQL Server Management Studio を使用してアプリケーションを監視します。
	- Microsoft System Center - Operations Manager のようなオンプレミスの管理ソフトウェアを使用して、仮想マシンを監視し管理することもできます。

### シナリオ 2: カスタマイズして公開された SharePoint ファーム

組織は、クラウドでホストされ、ニーズと需要に応じて簡単に拡張できるインターネット サイトを作成したいと考えています。また、コラボレーションのためにパートナー用のエクストラネット Web サイトを作成して、分散型オーサリングと Web サイト コンテンツ承認のための簡単なプロセスを導入したいとも考えています。最後に、増大する負荷を処理するために、Web サイトの容量をオンデマンドで増やしたいと考えています。

このシナリオの場合、SharePoint Server は公開された Web サイトをホストする土台として使用されます。安全で拡張性の高いクラウド インフラストラクチャ上で、ビジネス Web サイトの迅速なデプロイ、カスタマイズ、およびホストができます。Azure で SharePoint の公開 Web サイトを使用すると、トラフィックの増大に応じてサイズを変更し、使用した分だけ料金を支払うことができます。オンプレミスと同様の一般的ツールを、Azure 上の SharePoint でもコンテンツのオーサリング、ワークフロー、および承認に使用できます。

さらに、Azure Virtual Machines を使用すると、仮想マシン上で動作しているステージング環境と運用環境を簡単に構成できます。Azure で作成した SharePoint の公開仮想マシンは、仮想ストレージにバックアップできます。さらに、災害復旧目的で、連続的な geo レプリケーション機能を利用して、あるデータ センターにある仮想マシンを遠く離れた別のデータ センターに自動的にバックアップすることができます。

Azure インフラストラクチャの仮想マシンは、SQL Server や SharePoint Server など他の Microsoft 製品でも動作することが検証されサポートされています。Azure および SharePoint Server は連携して利用すると一段と便利です。どちらも、最適な体験を提供できるように徹底的に統合、サポート、テストされています。どちらも SharePoint アプリケーションと Azure インフラストラクチャのサポートが一元化されています。

このシナリオでは、余分なトラフィックをサポートするために SharePoint Server にフロントエンドの Web サーバーを追加する必要があります。追加するサーバーは、ユーザー認証とアクセス制御をサポートするために、セキュリティの強化、および、Active Directory Domain Services ドメイン コントローラーが必要です。図 4 に、このシナリオのレイアウトを示します。

![azure-sharepoint-wp-12](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-12.png)

**図 4: カスタマイズして公開された SharePoint ファーム**

公開された SharePoint ファームを Azure に実装するには、次の手順に従います。

1. Active Directory のデプロイ: Active Directory を Azure の仮想マシンにデプロイするときに必要な基本的要件は、オンプレミスの仮想マシン (そしてある程度は物理マシン) にデプロイするときと同様です。ただし同一ではありません。この違いの詳細、指針、その他の考慮事項については、「[Azure の仮想マシンでの Active Directory のデプロイ ガイドライン](http://msdn.microsoft.com/library/jj156090)」を参照してください。Azure で Active Directory をデプロイするには、以下の手順を実行します。
	- 仮想マシンを特定のサブネットに割り当てることのできる仮想ネットワークを定義して作成します。
	- Azure ポータルを使用して、Azure の新しい仮想マシンにドメイン コントローラーを作成してデプロイします。Windows PowerShell スクリプトを参照し、Azure Virtual Machines と Azure Virtual Network を使用して、クラウドにスタンドアロン ドメインをデプロイすることもできます。Azure Virtual Network 上の仮想マシンに新しい Active Directory フォレストを作成する方法の詳細については、「[Azure での新しい Active Directory フォレストのインストール](active-directory-new-forest-virtual-machine.md)」を参照してください。
2. 仮想マシンのプロビジョニング: Azure ポータルで、イメージ ギャラリーにあるストック イメージを使用して、新しい仮想マシンをプロビジョニングします。
3. SharePoint ファームのデプロイ:
	- Azure ポータルを使用してロード バランサーを構成します。仮想マシンエンドポイントを構成し、既存のエンドポイントでトラフィックを負荷分散するオプションを選択して、負荷分散された仮想マシンの名前を指定します。
	- 余分なトラフィック用として既存の SharePoint ファームに別のフロントエンド Web 仮想マシンを追加します。
3. 仮想マシンの管理
	- Azure ポータルを使用して仮想マシンを監視します。
	- [サーバーの全体管理] を使用して SharePoint ファームを監視します。

### シナリオ 3: BI サービスを追加するためにスケール アウトしたファーム

ビジネス インテリジェンス (BI) は、重要な洞察を得て、迅速で健全な意思決定を行うために不可欠です。企業は、オンプレミスから移行するにつれて、既存の BI アプリケーションをクラウドにデプロイしながらも、BI 環境自体は変更したくないと考えています。企業が望んでいるのは、SQL Server Analysis Services (SSAS) または SQL Server Reporting Services (SSRS) のレポートを耐久性と可用性が高い環境でホストしながら、保守に大きな時間と予算をかけることなく、BI アプリケーションを全面的にコントロールすることです。

このシナリオでは、Azure の仮想マシンを使用して基幹業務系の BI アプリケーションをホストする方法について説明します。SharePoint ファームの Azure の仮想マシンにデプロイして、アプリケーション サーバー仮想マシンの BI コンポーネント (SSRS や Excel Services など) をスケールアウトできます。クラウドでリソース集約的なコンポーネントを拡張することで、特殊なワークロードをもっと簡単にうまくサポートできるようになります。小規模から超大規模まで、SQL Server インスタンスのサイズを簡単に変更できるため、Azure の仮想マシンの SQL Server はパフォーマンスが向上します。これがシステムのエラスティック性となり、直近のワークロード要件に基づいて BI インスタンスを動的にプロビジョニング (拡大) したりプロビジョニングを解除 (縮小) したりできるようになります。

既存の BI アプリケーションを Azure に移行すると拡張性が向上します。SQL Server Analysis Services (SSAS)、SQL Server Reporting Service (SSRS)、および SharePoint Server の機能活用すると、スケールアップまたはスケールダウンできる強力な BI およびレポート アプリケーションおよびダッシュボードを作成できます。このようなアプリケーションとダッシュボードは、オンプレミスのデータおよびアプリケーションとさらに安全に統合することもできます。Azure では、ISO 27001 のサポートとデータセンターのコンプライアンスが保証されています。詳細については、[Azure のトラスト センター](http://azure.microsoft.com/support/trust-center/compliance/)を参照してください。

BI コンポーネントのデプロイをスケールアウトするには、PowerPivot、Power View、Excel Services、PerformancePoint Services のようなサービスを実行する新しいアプリケーション サーバーをインストールする必要があります。または、SSAS や SSRS のような SQL Server BI インスタンスを既存のファームに追加して、追加のクエリ処理をサポートする必要があります。サーバーは、SharePoint 2010 Server または SQL Server がインストールされた新しい Azure 仮想マシンとして追加できます。その後、そのサーバーに BI コンポーネントをインストールし、デプロイして構成できます (図 5)。

![azure-sharepoint-wp-13](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-13.png)

**図 5: BI サービスを追加するためにスケール アウトした SharePoint ファーム**

Azure で BI 環境をスケールアウトするには、次の手順に従います。

1. プロビジョニング:
	- Azure Virtual Network を使用してオンプレミスのシステムおよび Azure との VPN 接続をプロビジョニングします詳細については、「[仮想ネットワークの概要](http://msdn.microsoft.com/library/jj156007.aspx)」を参照してください。
	- Azure ポータルで、イメージ ギャラリーにあるストック イメージを使用して、新しい仮想マシンをプロビジョニングします。SharePoint Server または SQL Server の BI ワークロード イメージをイメージ ライブラリにアップロードすると、権限のあるユーザーがその BI コンポーネント仮想マシンを選択してスケール アウトした環境を構築できます。
2. インストール: 
	- SharePoint Server または SQL Server の BI コンポーネントの構築済みイメージがない場合は、リモート デスクトップ接続を使用して、SharePoint Server および SQL Server を仮想マシンにインストールします。
	- SharePoint のインストールの詳細については、「[Windows PowerShell を使用して SharePoint Server 2010 をインストールする](http://technet.microsoft.com/library/cc262839.aspx)」または [CodePlex の AutoSPInstaller](http://autospinstaller.codeplex.com/) のページを参照してください。
	- SQL Server のインストールの詳細については、「[SysPrep を使用した SQL Server 2012 のインストール](http://msdn.microsoft.com/library/ee210664.aspx)」を参照してください。
3. BI 仮想マシンの追加
	- Azure ポータル エンドポイントでセキュリティを構成し、仮想マシンの Windows ファイアウォールで着信ポートを設定します。
	- 既存の SharePoint または SQL Server ファームに新しく作成した BI 仮想マシンを追加します。
4. 仮想マシンの管理
	- Azure ポータルを使用して仮想マシンを監視します。
	- [サーバーの全体管理] を使用して SharePoint ファームを監視します。
	- Microsoft System Center - Operations Manager のようなオンプレミスの管理ソフトウェアを使用して、仮想マシンを監視し管理します。

### シナリオ 4: 完全にカスタマイズした SharePoint ベースの Web サイト

全面的にカスタマイズした SharePoint Web サイトをクラウドに作成したいと考えている組織が増えています。必要とされているのは、クラウドで実行されている複雑なアプリケーションを全面的にコントロールして保守できる耐久性と可用性の高い環境です。しかし、大量の時間と予算をかけることは望まれていません。

このシナリオの場合、クラウドに SharePoint ファーム全体をデプロイして、すべてのコンポーネントを動的にスケール アウトして容量を増やすことも、オンプレミスのデプロイをクラウドに拡張して、必要に応じて容量を増やしパフォーマンスを高めることもできます。このシナリオの対象は、アプリケーション開発と企業コンテンツ管理に関して全面的な SharePoint 体験を必要とする組織です。もっと複雑なサイトでも、エンド ツー エンドのフル機能のために、強化されたレポート機能、Power View、PerformancePoint、PowerPivot、詳細なグラフ、その他の SharePoint サイト機能を含めることができます。

Azure の仮想マシンを使用すると、経済的で高度なセキュリティで保護されたクラウド インフラストラクチャ上で、カスタマイズしたアプリケーションおよび関連コンポーネントをホストできます。また、オンプレミスの Microsoft System Center を、オンプレミスのアプリケーションとクラウド アプリケーションに共通の管理ツールとして使用することもできます。

完全にカスタマイズした SharePoint Web サイトを Azure で実装するには、クラウドに Active Directory ドメインをデプロイして、このドメインに新しい仮想マシンをプロビジョニングする必要があります。次に、SQL Server 2012 を実行している仮想マシンを作成して SharePoint ファームの一部として構成する必要があります。最後に、SharePoint ファームを作成して、負荷分散し、Active Directory および SQL Server に接続する必要があります (図 6)。

![azure-sharepoint-wp-14](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-14.png)

**図 6: 完全にカスタマイズした SharePoint ベースの Web サイト**

次に示しているのは、イメージ ライブラリにある構築済みイメージから、カスタマイズした SharePoint ファーム環境を作成する手順です。ただし、SharePoint ファーム仮想マシンをイメージ ライブラリにアップロードして、権限のあるユーザーがその仮想マシンを選択して、必要な SharePoint ファームを Azure 上に構築することもできます。

1. Active Directory のデプロイ: Active Directory を Azure の仮想マシンにデプロイするときに必要な基本的要件は、オンプレミスの仮想マシン (そしてある程度は物理マシン) にデプロイするときと同様です。ただし同一ではありません。この違いの詳細、指針、その他の考慮事項については、「[Azure の仮想マシンでの Active Directory のデプロイ ガイドライン](http://msdn.microsoft.com/library/jj156090)」を参照してください。Azure で Active Directory をデプロイするには、以下の手順を実行します。
	- 仮想マシンを特定のサブネットに割り当てることのできる仮想ネットワークを定義して作成します。
	- Azure ポータルを使用して、Azure の新しい仮想マシンにドメイン コントローラーを作成してデプロイします。
	- Azure Virtual Network 上の仮想マシンに新しい Active Directory フォレストを作成する方法の詳細については、「[Azure での新しい Active Directory フォレストのインストール](active-directory-new-forest-virtual-machine)」を参照してください。
2. SQL Server のデプロイ:
	- Azure ポータルで、イメージ ギャラリーにあるストック イメージを使用して、新しい仮想マシンをプロビジョニングします。
	- 仮想マシンで SQL Server を構成します。詳細については、「[SysPrep を使用した SQL Server 2014 のインストール](http://msdn.microsoft.com/library/ee210664.aspx)」を参照してください。
	- 新しく作成した Active Directory ドメインに仮想マシンを結合します。
3. マルチサーバー SharePoint ファームのデプロイ:
	- 仮想ネットワークを作成します。詳細については、「[仮想ネットワークの概要](http://msdn.microsoft.com/library/jj156007.aspx)」を参照してください。
	- SharePoint 仮想マシンのデプロイ時には、プロビジョニング時にローカル Active Directory ボックスの DNS アドレスが使用できるように、SharePoint Server のサブネットを指定する必要があります。
	- Azure ポータルを使用して、仮想マシンを作成します。
	- SharePoint Server をこの仮想マシンにインストールして、再利用可能なイメージを生成します。SharePoint Server のインストールの詳細については、「[Windows PowerShell を使用して SharePoint Server 2010 をインストールする](http://technet.microsoft.com/library/cc262839.aspx)」または [CodePlex の AutoSPInstaller](http://autospinstaller.codeplex.com/) のページを参照してください。
	- SharePoint 仮想マシンを構成して SharePoint ファームを作成し、[Join-SharePointFarm](http://technet.microsoft.com/library/ff607979.aspx) コマンドを使用してこのファームに接続します。
	- Azure ポータルを使用してロード バランサーを構成します。仮想マシン エンドポイントを構成し、既存のエンドポイントでトラフィックを負荷分散するオプションを選択して、負荷分散された仮想マシンの名前を指定します。
4. System Center による SharePoint ファームの管理:
	- Operations Manager エージェントおよび新しい Azure Integration Pack を使用して、オンプレミスの System Center を Azure の仮想マシンに接続します。
	- 管理機能としては、オンプレミスの App Controller および Orchestrator を使用します。

## 概要

Azure の仮想マシンはありとあらゆる種類の SharePoint デプロイを実現できます。他の Microsoft アプリケーションで最適な体験を提供できるように、全面的にサポートされ、テストがなされています。そのため、新しい SharePoint デプロイ用にインフラストラクチャをプロビジョニングする場合でも、または既存のインフラストラクチャを拡張する場合でも、SharePoint Server を Azure 内に簡単にセットアップしてデプロイできます。SharePoint インフラストラクチャは、ビジネス ワークロードが増大するにつれて迅速に拡張できます。同様に、ワークロードを減らす必要がある場合も、リソースをオンデマンドで契約して、使用する分だけ料金を支払うことができます。この記事で説明した 4 つの SharePoint ベースのシナリオで示したように、Azure の仮想マシンは、広範なビジネス要件を満たす並外れたインフラストラクチャになります。

SharePoint Server を Azure の仮想マシンに正常にデプロイするには、堅実な計画が必要であり、特に重大なファーム アーキテクチャとデプロイ オプションの範囲を検討する必要があります。この記事で概略を示した洞察とベスト プラクティスは、情報を得たうえで SharePoint デプロイを実装するにあたって意思決定の指針として役立ちます。

## その他のリソース

[Azure 仮想マシン上の SharePoint Server](http://msdn.microsoft.com/library/dn275955.aspx)

[Azure インフラストラクチャ サービスでホストされる SharePoint ファーム](virtual-machines-sharepoint-infrastructure-services.md)

[Azure インフラストラクチャ サービスのワークロード: イントラネット SharePoint ファーム](virtual-machines-workload-intranet-sharepoint-farm)

[Azure インフラストラクチャ サービス実装ガイドライン](virtual-machines-infrastructure-services-implementation-guidelines.md)

<!---HONumber=August15_HO9-->