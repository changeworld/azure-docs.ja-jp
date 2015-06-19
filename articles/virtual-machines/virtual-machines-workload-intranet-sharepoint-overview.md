<properties 
	pageTitle="Azure での SharePoint と SQL Server AlwaysOn 可用性グループのデプロイ" 
	description="5 つのフェーズで、Azure に SharePoint と SQL Server AlwaysOn 可用性グループをデプロイできます。" 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/05/2015" 
	ms.author="josephd"/>

# Azure での SharePoint と SQL Server AlwaysOn 可用性グループのデプロイ

このトピックには、Azure インフラストラクチャ サービスにイントラネットのみの SharePoint 2013 ファームと SQL Server AlwaysOn 可用性グループをデプロイするための詳細な手順へのリンクが含まれます。ファームには次のコンピューターが含まれます。

- 2 台の SharePoint Web サーバー
- 2 台の SharePoint アプリケーション サーバー
- 2 台のデータベース サーバー
- 1 台のクラスター マジョリティ ノード サーバー
- 2 台のドメイン コントローラー

構成を次に示します。各サーバーの名前はプレースホルダーです。

![](./media/virtual-machines-workload-intranet-sharepoint-overview/workload-spsqlao_05.png)
 
ロールごとに 2 台のマシンで高可用性を保証します。すべての仮想マシンは 1 つのリージョンに存在します。特定のロール用の仮想マシンの各グループは、専用の可用性セットに含まれます。

この構成を次のフェーズでデプロイします。

- [フェーズ 1: Azure を構成する](virtual-machines-workload-intranet-sharepoint-phase1.md)。ストレージ アカウント、クラウド サービス、およびクロスプレミス仮想ネットワークを作成します。
- [フェーズ2: ドメイン コントローラーを構成する](virtual-machines-workload-intranet-sharepoint-phase2.md)。レプリカ Active Directory ドメイン サービス (AD DS) ドメイン コントローラーを作成して構成します。
- [フェーズ 3: SQL Server インフラストラクチャを構成する](virtual-machines-workload-intranet-sharepoint-phase3.md)。SQL Server 仮想マシンを作成して構成し、SharePoint 用に準備して、クラスターを作成します。
- [フェーズ 4: SharePoint サーバーを構成する](virtual-machines-workload-intranet-sharepoint-phase4.md)。4 つの SharePoint 仮想マシンを作成して構成します。
- [フェーズ 5: 可用性グループを作成して SharePoint データベースを追加する](virtual-machines-workload-intranet-sharepoint-phase5.md)。データベースを準備し、SQL Server AlwaysOn 可用性グループを作成します。

この SharePoint と SQL Server AlwaysOn のデプロイメントは、[SharePoint と SQL Server AlwaysOn のインフォグラフィック](http://go.microsoft.com/fwlink/?LinkId=394788)が付属し、最新の推奨事項が組み込まれるように設計されています。

この構成は、Azure インフラストラクチャ サービスに機能する高可用性のイントラネット SharePoint ファームを作成するための、事前に定義されたアーキテクチャの規範的なフェーズ単位のガイドです。Azure への SharePoint 2013 の実装に関する他のアーキテクチャ ガイダンスについては、「[SharePoint 2013 用の Microsoft Azure アーキテクチャ](https://technet.microsoft.com/library/dn635309.aspx)」を参照してください。

次の点に注意してください。

- SharePoint の実装に関する経験が豊富な場合は、フェーズ 3 ～ 5 の指示を自由に調整して、ニーズに最も適したファームを構築してください。 
- 既存の Azure ハイブリッド クラウド実装が既にある場合は、フェーズ 1 および 2 の指示を自由に調整するかスキップし、適切なサブネットで新しい SharePoint ファームをホストしてください。
- すべてのサーバーは、Azure Virtual Network 内の 1 つのサブネット上の存在します。サブネットの分離に相当する追加のセキュリティを提供する場合は、[ネットワーク セキュリティ グループ](https://msdn.microsoft.com/library/azure/dn848316.aspx)を使用できます。

この構成の開発/テスト環境または概念実証環境を構築する場合は、「[テスト用のハイブリッド クラウドでの SharePoint イントラネット ファームの設定](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)」を参照してください。

SharePoint と SQL Server AlwaysOn 可用性グループの追加情報については、「[SQL Server 2012 の AlwaysOn 可用性グループを SharePoint 2013 用に構成する](https://technet.microsoft.com/library/jj715261.aspx)」を参照してください。

## 次の手順

このワークロードの構成を開始するには、「[フェーズ 1: Azure を構成する](virtual-machines-workload-intranet-sharepoint-phase1.md)」に進んでください。


## その他のリソース

[SharePoint と SQL Server AlwaysOn のインフォグラフィック](http://go.microsoft.com/fwlink/?LinkId=394788)

[SharePoint 2013 用の Microsoft Azure アーキテクチャ](https://technet.microsoft.com/library/dn635309.aspx)

[Azure インフラストラクチャ サービスでホストされる SharePoint ファーム](virtual-machines-sharepoint-infrastructure-services.md)

<!--HONumber=54--> 