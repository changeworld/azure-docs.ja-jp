<properties 
	pageTitle="基幹業務アプリケーションのデプロイ | Microsoft Azure" 
	description="Azure では、Web ベースの高可用な基幹業務アプリケーションと SQL Server AlwaysOn 可用性グループを 5 つのフェーズでデプロイできます。" 
	documentationCenter=""
	services="virtual-machines" 
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
	ms.date="08/11/2015" 
	ms.author="josephd"/>

# Azure での高可用な基幹業務アプリケーションのデプロイ

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]この記事では、リソース マネージャーのデプロイメント モデルを使用したリソースの作成について説明します。

この記事には、Azure インフラストラクチャ サービスに、高可用なイントラネット専用のWeb ベース基幹業務アプリケーションと SQL Server AlwaysOn 可用性グループをデプロイするための詳細な手順へのリンクが含まれます。アプリケーションは、次のコンピューターでホストされます。

- 2 台の Web サーバー
- 2 台のデータベース サーバー
- 1 台のクラスター マジョリティ ノード サーバー
- 2 台のドメイン コントローラー

構成を次に示します。各サーバーの名前はプレースホルダーです。

![](./media/virtual-machines-workload-high-availability-LOB-application-overview/workload-lobapp-phase4.png)
 
ロールごとに最低 2 台のマシンを使用して、高可用性を保証します。すべての仮想マシンは、1 つの Azure の場所 (リージョンとも呼ばれます) に存在します。特定のロール用の仮想マシンの各グループは、専用の可用性セットに含まれます。

この構成を次のフェーズでデプロイします。

- [フェーズ 1: Azure を構成する](virtual-machines-workload-high-availability-LOB-application-phase1.md)。ストレージ アカウント、可用性セット、およびクロスプレミス仮想ネットワークを作成します。
- [フェーズ 2: ドメイン コントローラーを構成する](virtual-machines-workload-high-availability-LOB-application-phase2.md)。レプリカ Active Directory ドメイン サービス (AD DS) ドメイン コントローラーを作成して構成します。
- [フェーズ 3: SQL Server インフラストラクチャを構成する](virtual-machines-workload-high-availability-LOB-application-phase3.md)。SQL Server を実行する仮想マシンを作成して構成し、クラスターを作成して、SQL Server AlwaysOn 可用性グループを有効にします。
- [フェーズ 4: Web サーバーを構成する](virtual-machines-workload-high-availability-LOB-application-phase4.md)。Web サーバー仮想マシンを 2 つ作成して構成します。
- [フェーズ 5: アプリケーション データベースを SQL Server AlwaysOn 可用性グループに追加する](virtual-machines-workload-high-availability-LOB-application-phase5.md)。基幹業務アプリケーションのデータベースを準備し、SQL Server AlwaysOn 可用性グループに追加します。

このデプロイメントは、[基幹業務アプリケーションのアーキテクチャ ブループリント](http://msdn.microsoft.com/dn630664)が付属し、最新の推奨事項が組み込まれるように設計されています。

これは、規範となる、定義済みのアーキテクチャです。次の点に注意してください。

- Web ベースの基幹業務アプリケーションの実装に関する経験が豊富な場合は、フェーズ 3 ～ 5 の指示を自由に調整して、ニーズに最も適したアプリケーション インストラクチャを構築してください。 
- 既に Azure ハイブリッド クラウド実装がある場合は、フェーズ 1 および 2 の指示を自由に調整するかスキップし、適切なサブネットで新しいアプリケーション用の仮想マシンをホストしてください。
- すべてのサーバーは、Azure Virtual Network 内の 1 つのサブネット上の存在します。サブネットの分離に相当する追加のセキュリティを提供する場合は、[ネットワーク セキュリティ グループ](../virtual-networks/virtual-networks-nsg.md)を使用できます。

この構成の開発/テスト環境または概念実証環境を構築するには、「[テスト用のハイブリッド クラウドでの Web ベース LOB アプリケーションの設定](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)」を参照してください。

Azure の IT ワークロードの設計に関する詳細については、「[Azure インフラストラクチャ サービス実装ガイドライン](virtual-machines-infrastructure-services-implementation-guidelines.md)」を参照してください。

## 次のステップ

このワークロードの構成を開始するには、「[フェーズ 1: Azure を構成する](virtual-machines-workload-high-availability-LOB-application-phase1.md)」に進んでください。

## その他のリソース

[基幹業務アプリケーションのアーキテクチャ ブループリント](http://msdn.microsoft.com/dn630664)

[テスト用のハイブリッド クラウドでの Web ベース LOB アプリケーションの設定](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Azure インフラストラクチャ サービス実装ガイドライン](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Azure インフラストラクチャ サービスのワークロード: SharePoint Server 2013 ファーム](virtual-machines-workload-intranet-sharepoint-farm.md)

<!---HONumber=Sept15_HO3-->