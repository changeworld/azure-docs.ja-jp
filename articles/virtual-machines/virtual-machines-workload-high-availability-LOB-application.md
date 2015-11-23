<properties 
	pageTitle="Azure の基幹業務アプリケーション | Microsoft Azure" 
	description="Azure の基幹業務アプリケーションの価値を説明し、テスト環境の設定および高可用性構成のデプロイを実行します。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/09/2015" 
	ms.author="josephd"/>

# Azure インフラストラクチャ サービスのワークロード: 高可用な基幹業務アプリケーション

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]クラシック デプロイ モデル。


Microsoft Azure で、最初または次の、Web ベースでイントラネット専用の基幹業務アプリケーションを設定し、簡単に構成できる機能と、新機能を追加するためにアプリケーションを迅速に拡張する機能を活用します。
 
Azure インフラストラクチャ サービスの Virtual Machines と Virtual Network を使用すると、組織のユーザーがアクセス可能な基幹業務アプリケーションをすばやくデプロイし、実行できるようになります。たとえば、次のようにセットアップできます。

![](./media/virtual-machines-workload-high-availability-LOB-application/workload-lobapp-phase4.png)
 
Azure Virtual Network は、すべての名前とトラフィック ルーティングを修正したオンプレミス ネットワークの拡張であるため、ユーザーは、オンプレミス データセンターにあるサーバーと同様に、基幹業務アプリケーションのサーバーにアクセスできます。

この構成では、新しい Azure 仮想マシンを追加することで、アプリケーションの機能を簡単に拡張できます。この場合、ハードウェアとメンテナンス両方の継続的なコストは、独自のデータセンターで同等のアプリケーションを実行するよりも低くなります。

次の手順では、Azure でホストされる開発/テスト基幹業務アプリケーションをセットアップします。

## Azure でホストされる開発/テスト基幹業務アプリケーションの作成

クロスプレミス仮想ネットワークは、サイト間 VPN または ExpressRoute 接続を使用して、オンプレミス ネットワークに接続されています。最終的な構成に似せた開発/テスト環境を作成し、VPN 接続でアプリケーションへのアクセスとリモート管理の実行を実験する場合は、「[テスト用のハイブリッド クラウドでの Web ベース LOB アプリケーションの設定](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)」を参照してください。

![](./media/virtual-machines-workload-high-availability-LOB-application/CreateLOBAppHybridCloud_3.png)
 
[MSDN サブスクリプション](http://azure.microsoft.com/pricing/member-offers/msdn-benefits/)または [Azure 試用サブスクリプション](http://azure.microsoft.com/pricing/free-trial/)を持っている場合、このような開発/テスト環境を無料で作成できます。

次の手順では、Azure で高可用な基幹業務アプリケーションを作成します。

## Azure でホストされる開発/テスト基幹業務アプリケーションのデプロイ

Azure での高可用な基幹業務アプリケーションの基本的で代表的な構成は、次のようになります。

![](./media/virtual-machines-workload-high-availability-LOB-application/workload-lobapp-phase4.png)
 
構成は次のとおりです。

- Web層とデータベース層にサーバーが 2 つずつあるイントラネット専用基幹業務アプリケーション。
- クラスターに SQL サーバーを実行する 2 つの仮想マシンと 1 つのマジョリティ ノード コンピューターがある SQL Server AlwaysOn 構成。
- 仮想ネットワークに 2 つのレプリカ ドメイン コント ローラーがある Active Directory ドメイン サービス。

基幹業務アプリケーションの概要については、「[基幹業務アプリケーションのアーキテクチャ ブループリント](http://msdn.microsoft.com/dn630664)」を参照してください。

### 部品表

この基本的な構成には、次の一連の Azure サービスとコンポーネントが必要です。

- 7 つの仮想マシン
- 4 つの追加データ ディスク (ドメイン コント ローラーと、SQL サーバーを実行する仮想マシン用)
- 3 つの可用性セット
- 1 つのクロスプレミス仮想ネットワーク
- 2 つのストレージ アカウント

こちらが仮想マシンとそれぞれのこの構成に対する既定のサイズになります。

項目 | 仮想マシンの説明 | ギャラリー イメージ | 既定サイズ 
--- | --- | --- | --- 
1\. | 最初のドメイン コントローラー | Windows Server 2012 R2 Datacenter | D1
2\. | 2 番目のドメイン コントローラー | Windows Server 2012 R2 Datacenter | D1
3\. | プライマリ データベース サーバー | Microsoft SQL Server 2014 Enterprise - Windows Server 2012 R2 | D4
4\. | セカンダリ データベース サーバー | Microsoft SQL Server 2014 Enterprise - Windows Server 2012 R2 | D4
5\. | クラスターのマジョリティ ノード | Windows Server 2012 R2 Datacenter | D1
6\. | 最初の Web サーバー | Windows Server 2012 R2 Datacenter | D3
7\. | 2 番目の Web サーバー | Windows Server 2012 R2 Datacenter | D3

この構成の推定コストを計算するには、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を参照してください。

1. [**モジュール**] で、[**コンピューティング**] をクリックし、[**仮想マシン**] を必要なだけクリックして 7 つの仮想マシンを作成してください。
2. 各仮想マシンで次を選択してください。
	- 目的のリージョン
	- 種類には **Windows**
	- 価格レベルには**標準**
	- **インスタンス サイズ**には、上記の表でのデフォルト サイズもしくは目的のサイズ

> [AZURE.NOTE]Azure 料金計算ツールでは、SQL Server 2014 Enterprise を実行している 2 つの仮想マシンの SQL Server ライセンスの追加のコストは含まれません。詳細については、「[Virtual Machines Pricing-SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql)」を参照してください。

### デプロイ フェーズ

この構成をデプロイするには、次のプロセスを使用します。

- フェーズ 1: Azure を構成する 

	Azure PowerShell を使用して、ストレージ アカウント、可用性セット、およびクロスプレミス仮想ネットワークを作成します。詳細な構成手順については、「[フェーズ 1](virtual-machines-workload-high-availability-LOB-application-phase1.md)」を参照してください。

- フェーズ2: ドメイン コントローラーを構成する

	仮想ネットワークの 2 つの Active Directory レプリカ ドメイン コントローラーと DNS 設定を構成します。詳細な構成手順については、「[フェーズ 2](virtual-machines-workload-high-availability-LOB-application-phase2.md)」を参照してください。

- フェーズ 3: SQL Server インフラストラクチャを構成する

	SQL Server を実行する仮想マシンと、クラスターを作成します。詳細な構成手順については、「[フェーズ 3](virtual-machines-workload-high-availability-LOB-application-phase3.md)」を参照してください。

- フェーズ 4: Web サーバーを構成する

	Web サーバー仮想マシンを作成し、基幹業務アプリケーションをその仮想マシンに追加します。詳細な構成手順については、「[フェーズ 4](virtual-machines-workload-high-availability-LOB-application-phase4.md)」を参照してください。

- フェーズ 5: SQL Server AlwaysOn 可用性グループを構成する

	アプリケーション データベースを準備し、SQL Server AlwaysOn 可用性グループを作成して、準備したデータベースをそれに追加します。詳細な構成手順については、「[フェーズ 5](virtual-machines-workload-high-availability-LOB-application-phase5.md)」を参照してください。

構成が済むと、Web サーバー、または SQL Server を実行する仮想マシンをクラスターにさらに追加することで、この基幹業務アプリケーションを簡単に拡張できます。

## その他のリソース

[Azure での高可用な基幹業務アプリケーションのデプロイ](virtual-machines-workload-high-availability-LOB-application-overview.md)

[基幹業務アプリケーションのアーキテクチャ ブループリント](http://msdn.microsoft.com/dn630664)

[テスト用のハイブリッド クラウドでの Web ベース LOB アプリケーションの設定](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Azure インフラストラクチャ サービス実装ガイドライン](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Azure インフラストラクチャ サービスのワークロード: SharePoint Server 2013 ファーム](virtual-machines-workload-intranet-sharepoint-farm.md)

<!---HONumber=Nov15_HO3-->