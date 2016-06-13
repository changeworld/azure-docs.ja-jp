<properties 
	pageTitle="Azure の基幹業務アプリケーション | Microsoft Azure" 
	description="Azure の基幹業務アプリケーションの価値を説明し、テスト環境の設定および高可用性構成のデプロイを実行します。" 
	services="virtual-machines-windows" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines-windows" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/04/2016" 
	ms.author="josephd"/>

# Azure インフラストラクチャ サービスのワークロード: 高可用な基幹業務アプリケーション

Microsoft Azure で、最初または次の、Web ベースでイントラネット専用の基幹業務アプリケーションを設定し、簡単に構成できる機能と、新機能を追加するためにアプリケーションを迅速に拡張する機能を活用します。
 
Azure インフラストラクチャ サービスの Virtual Machines と Virtual Network を使用すると、組織のユーザーがアクセス可能な基幹業務アプリケーションをすばやくデプロイし、実行できるようになります。たとえば、次のようにセットアップできます。

![](./media/virtual-machines-windows-lob/workload-lobapp-phase4.png)
 
Azure Virtual Network は、すべての名前とトラフィック ルーティングを修正したオンプレミス ネットワークの拡張であるため、ユーザーは、オンプレミス データセンターにあるサーバーと同様に、基幹業務アプリケーションのサーバーにアクセスできます。

この構成では、新しい Azure 仮想マシンを追加することで、アプリケーションの機能を簡単に拡張できます。この場合、ハードウェアとメンテナンス両方の継続的なコストは、独自のデータセンターで同等のアプリケーションを実行するよりも低くなります。

次の手順では、Azure でホストされる開発/テスト基幹業務アプリケーションをセットアップします。

## Azure でホストされる開発/テスト基幹業務アプリケーションの作成

クロスプレミス仮想ネットワークは、サイト間 VPN または ExpressRoute 接続を使用して、オンプレミス ネットワークに接続されています。最終的な構成に似せた開発/テスト環境を作成し、VPN 接続でアプリケーションへのアクセスとリモート管理の実行を実験する場合は、「[テスト用のハイブリッド クラウドでの Web ベース LOB アプリケーションの設定](virtual-machines-windows-ps-hybrid-cloud-test-env-lob.md)」を参照してください。

![](./media/virtual-machines-windows-lob/CreateLOBAppHybridCloud_3.png)
 
[MSDN サブスクリプション](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/)または Azure サブスクリプションを持っている場合、このような開発/テスト環境を無料で作成できます。

次の手順では、Azure で高可用な基幹業務アプリケーションを作成します。

## Azure でホストされる開発/テスト基幹業務アプリケーションのデプロイ

Azure での高可用な基幹業務アプリケーションの基本的で代表的な構成は、次のようになります。

![](./media/virtual-machines-windows-lob/workload-lobapp-phase4.png)
 
構成は次のとおりです。

- Web層とデータベース層にサーバーが 2 つずつあるイントラネット専用基幹業務アプリケーション。
- クラスターに SQL Server を実行する 2 つの仮想マシンと 1 つのマジョリティ ノード コンピューターがある SQL Server AlwaysOn 構成。
- 仮想ネットワークに 2 つのレプリカ ドメイン コント ローラーがある Active Directory ドメイン サービス。

基幹業務アプリケーションの概要については、「[基幹業務アプリケーションのアーキテクチャ ブループリント](http://msdn.microsoft.com/dn630664)」を参照してください。

この構成をデプロイするには、次のプロセスを使用します。

- フェーズ 1: Azure を構成する 

	Azure PowerShell を使用して、ストレージ アカウント、可用性セット、およびクロスプレミス仮想ネットワークを作成します。詳細な構成手順については、「[フェーズ 1](virtual-machines-windows-ps-lob-ph1.md)」を参照してください。

- フェーズ 2: ドメイン コントローラーを構成する

	仮想ネットワークの 2 つの Active Directory レプリカ ドメイン コントローラーと DNS 設定を構成します。詳細な構成手順については、「[フェーズ 2](virtual-machines-windows-ps-lob-ph2.md)」を参照してください。

- フェーズ 3: SQL Server インフラストラクチャを構成する

	SQL Server を実行する仮想マシンと、クラスターを作成します。詳細な構成手順については、「[フェーズ 3](virtual-machines-windows-ps-lob-ph3.md)」を参照してください。

- フェーズ 4: Web サーバーを構成する

	Web サーバー仮想マシンを作成し、基幹業務アプリケーションをその仮想マシンに追加します。詳細な構成手順については、「[フェーズ 4](virtual-machines-windows-ps-lob-ph4.md)」を参照してください。

- フェーズ 5: SQL Server AlwaysOn 可用性グループを構成する

	アプリケーション データベースを準備し、SQL Server AlwaysOn 可用性グループを作成して、準備したデータベースをそのグループに追加します。詳細な構成手順については、「[フェーズ 5](virtual-machines-windows-ps-lob-ph5.md)」を参照してください。

構成が済むと、Web サーバー、または SQL Server を実行する仮想マシンをクラスターにさらに追加することで、この基幹業務アプリケーションを簡単に拡張できます。

## 次のステップ

- 構成を開始する前に、実稼働ワークロードの[概要](virtual-machines-windows-lob-overview.md)を入手します。

<!---HONumber=AcomDC_0601_2016-->