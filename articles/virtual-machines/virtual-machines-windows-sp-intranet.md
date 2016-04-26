<properties
	pageTitle="Azure の SharePoint Server 2013 ファーム | Microsoft Azure"
	description="Azure の SharePoint Server 2013 ファームの価値について説明し、テスト環境の設定および高可用性構成のデプロイを実行します。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/01/2016"
	ms.author="josephd"/>

# Azure インフラストラクチャ サービスのワークロード: イントラネット SharePoint ファーム

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]クラシック デプロイ モデル。

Microsoft Azure で、最初または次の SharePoint ファームを設定し、簡単に構成できる機能と、新機能を追加したり主要な機能を最適化したりするためにファームを迅速に拡張する機能を活用します。多くの SharePoint ファームは、標準、高可用の 3 層構成から、分散キャッシュや検索などのパフォーマンスや個別のロールに合わせて、数十以上のサーバーを含むファームへと成長します。

Azure インフラストラクチャ サービスの仮想マシンと仮想ネットワークの機能を使用すると、オンプレミス ネットワークに透過的に接続された SharePoint ファームをすばやくデプロイし、実行できるようになります。たとえば、次をセットアップできます。

![](./media/virtual-machines-windows-sp-intranet/workload-spsqlao.png)

Azure Virtual Network は、すべての名前とトラフィック ルーティングを修正したオンプレミス ネットワークの拡張であるため、ユーザーはオンプレミス データセンターにあるネットワークと同様にアクセスできます。

この構成では、新しい Azure 仮想マシンを追加することで、SharePoint ファームを簡単に拡張できます。この場合は、ハードウェアとメンテナンス両方の継続的なコストは、独自のデータセンターで同等のファームを実行するよりも低くなります。

Azure インフラストラクチャ サービスでイントラネット SharePoint ファームをホストする例として、基幹業務アプリケーションがあります。概要については、「[基幹業務アプリケーションのアーキテクチャ ブループリント](http://msdn.microsoft.com/dn630664)」を参照してください。

次の手順は、Azure でホストされている開発/テスト イントラネット SharePoint ファームのセットアップです。

## Azure でホストされている開発/テスト イントラネット SharePoint ファームを作成する

Azure でホストされている SharePoint の開発/テスト環境を作成するには、2 つの選択肢があります。

- クラウド専用仮想ネットワーク
- クロスプレミス仮想ネットワーク

[Visual Studio サブスクリプション](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/)または Azure サブスクリプションを持っている場合、このような開発/テスト環境を作成できます。

### クラウド専用仮想ネットワーク

クラウド専用仮想ネットワークは、オンプレミス ネットワークに接続されていません。基本的な、または高可用な SharePoint ファームをすばやく作成する場合は、「[SharePoint サーバー ファームの作成](virtual-machines-windows-sharepoint-farm.md)」を参照してください。基本的な SharePoint ファームの構成例を次に示します。

![](./media/virtual-machines-windows-sp-intranet/Non-HAFarm.png)

### クロスプレミス仮想ネットワーク

クロスプレミス仮想ネットワークは、サイト間 VPN または ExpressRoute 接続を使用して、オンプレミス ネットワークに接続されています。最終的な構成に似せた開発/テスト環境を作成し、VPN 接続で SharePoint サーバーへのアクセスとリモート管理の実行を実験する場合は、「[テスト用のハイブリッド クラウドでの SharePoint イントラネット ファームの設定](virtual-machines-windows-ps-hybrid-cloud-test-env-sp.md)」を参照してください。

![](./media/virtual-machines-windows-sp-intranet/CreateSPFarmHybridCloud.png)

次の手順では、Azure で高可用イントラネット SharePoint ファームを作成します。

## Azure でホストされているイントラネット SharePoint ファームをデプロイする

機能的で高可用なイントラネット SharePoint ファームの基本的で代表的な構成は、次のとおりです。

![](./media/virtual-machines-windows-sp-intranet/workload-spsqlao.png)

構成は次のとおりです。

- Web、アプリケーション、データベース層に 2 つのサーバーがあるイントラネット SharePoint ファーム。
- クラスターに 2 つの SQL サーバーと 1 つのマジョリティ ノード コンピューターがある SQL Server AlwaysOn 可用性グループ構成。
- オンプレミスの Active Directory ドメインの 2 つのレプリカ ドメイン コントローラー。

この構成のインフォグラフィックを確認するには、「[SharePoint と SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788)」を参照してください。

この構成をデプロイするには、次のプロセスを使用します。

- フェーズ 1: Azure を構成する。

	Azure PowerShell を使用して、ストレージ アカウント、可用性セット、およびクロスプレミス仮想ネットワークを作成します。詳細な構成手順については、「[フェーズ 1](virtual-machines-windows-ps-sp-intranet-ph1.md)」を参照してください。

- フェーズ 2: ドメイン コントローラーを構成する

	仮想ネットワークの 2 つの Active Directory レプリカ ドメイン コントローラーと DNS 設定を構成します。詳細な構成手順については、「[フェーズ 2](virtual-machines-windows-ps-sp-intranet-ph2.md)」を参照してください。

- フェーズ 3: SQL Server インフラストラクチャを構成する

	SharePoint に使用する SQL Server 仮想マシンを準備し、SQL Server クラスターを作成します。詳細な構成手順については、「[フェーズ 3](virtual-machines-windows-ps-sp-intranet-ph3.md)」を参照してください。

- フェーズ 4: SharePoint サーバーを構成する

	新しい SharePoint ファームの 4 つの SharePoint 仮想マシンを構成します。詳細な構成手順については、「[フェーズ 4](virtual-machines-windows-ps-sp-intranet-ph4.md)」を参照してください。

- フェーズ 5: AlwaysOn 可用性グループを作成する

	SharePoint データベースを準備し、AlwaysOn 可用性グループを作成し、SharePoint データベースを追加します。詳細な構成手順については、「[フェーズ 5](virtual-machines-windows-ps-sp-intranet-ph5.md)」を参照してください。

構成が完了したら、「[SharePoint 2013 用の Microsoft Azure アーキテクチャ](http://technet.microsoft.com/library/dn635309.aspx)」のガイドを参照して、この SharePoint ファームを拡張できます。

## 次のステップ

- 構成を開始する前に、実稼働ワークロードの[概要](virtual-machines-windows-sp-intranet-overview.md)を入手します。

<!---HONumber=AcomDC_0413_2016-->