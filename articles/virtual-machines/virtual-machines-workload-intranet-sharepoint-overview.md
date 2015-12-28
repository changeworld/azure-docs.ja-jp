<properties
	pageTitle="SharePoint Server 2013 ファームのデプロイ | Microsoft Azure"
	description="5 つのフェーズで、Azure の SQL Server AlwaysOn 可用性グループを使用して、高可用性 SharePoint Server 2013 ファームをデプロイします。"
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
	ms.date="12/11/2015"
	ms.author="josephd"/>

# Azure での SharePoint と SQL Server AlwaysOn 可用性グループのデプロイ

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]クラシック デプロイ モデル。

このトピックには、イントラネットのみの SharePoint 2013 ファームと SQL Server AlwaysOn 可用性グループをデプロイするための詳細な手順へのリンクが含まれます。ファームには次のコンピューターが含まれます。

- 2 台の SharePoint Web サーバー
- 2 台の SharePoint アプリケーション サーバー
- 2 台のデータベース サーバー
- 1 台のクラスター マジョリティ ノード サーバー
- 2 台のドメイン コントローラー

構成を次に示します。各サーバーの名前はプレースホルダーです。

![](./media/virtual-machines-workload-intranet-sharepoint-overview/workload-spsqlao_05.png)

ロールごとに 2 台のマシンで高可用性を保証します。すべての仮想マシンは 1 つのリージョンに存在します。特定のロール用の仮想マシンの各グループは、専用の可用性セットに含まれます。

この構成を次のフェーズでデプロイします。

- [フェーズ 1: Azure を構成する](virtual-machines-workload-intranet-sharepoint-phase1.md)。ストレージ アカウント、可用性セット、およびクロスプレミス仮想ネットワークを作成します。
- [フェーズ 2: ドメイン コントローラーを構成する](virtual-machines-workload-intranet-sharepoint-phase2.md)。レプリカ Active Directory ドメイン サービス (AD DS) ドメイン コントローラーを作成して構成します。
- [フェーズ 3: SQL Server インフラストラクチャを構成する](virtual-machines-workload-intranet-sharepoint-phase3.md)。SQL Server 仮想マシンを作成して構成し、SharePoint 用に準備して、クラスターを作成します。
- [フェーズ 4: SharePoint サーバーを構成する](virtual-machines-workload-intranet-sharepoint-phase4.md)。4 つの SharePoint 仮想マシンを作成して構成します。
- [フェーズ 5: 可用性グループを作成して SharePoint データベースを追加する](virtual-machines-workload-intranet-sharepoint-phase5.md)。データベースを準備し、SQL Server AlwaysOn 可用性グループを作成します。

この SharePoint と SQL Server AlwaysOn のデプロイは、[SharePoint と SQL Server AlwaysOn のインフォグラフィック](http://go.microsoft.com/fwlink/?LinkId=394788)が付属し、最新の推奨事項が組み込まれるように設計されています。

この構成は、Azure インフラストラクチャ サービスに機能する高可用性のイントラネット SharePoint ファームを作成するための、事前に定義されたアーキテクチャの規範的なフェーズ単位のガイドです。Azure への SharePoint 2013 の実装に関する他のアーキテクチャ ガイダンスについては、「[SharePoint 2013 用の Microsoft Azure アーキテクチャ](https://technet.microsoft.com/library/dn635309.aspx)」を参照してください。

次の点に注意してください。

- SharePoint の実装に関する経験が豊富な場合は、フェーズ 3 ～ 5 の指示を自由に調整して、ニーズに最も適したファームを構築してください。
- 既存の Azure ハイブリッド クラウド デプロイが既にある場合は、フェーズ 1 および 2 の指示を自由に調整するかスキップし、適切なサブネットで新しい SharePoint ファームをホストしてください。
- すべてのサーバーは、Azure Virtual Network 内の 1 つのサブネット上の存在します。サブネットの分離に相当する追加のセキュリティを提供する場合は、[ネットワーク セキュリティ グループ](../virtual-network/virtual-networks-nsg.md)を使用できます。

この構成の開発/テスト環境または概念実証環境を構築する場合は、「[テスト用のハイブリッド クラウドでの SharePoint イントラネット ファームの設定](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)」を参照してください。

SharePoint と SQL Server AlwaysOn 可用性グループの追加情報については、「[SQL Server 2012 の AlwaysOn 可用性グループを SharePoint 2013 用に構成する](https://technet.microsoft.com/library/jj715261.aspx)」を参照してください。

> [AZURE.NOTE]Microsoft は、SharePoint Server 2016 IT Preview をリリースしました。SharePoint Server 2016 IT Preview とその前提条件があらかじめインストールされた Azure 仮想マシン ギャラリー イメージを使用すると、このプレビュー版を簡単にインストールしてテストすることができます。詳細については、「[SharePoint Server 2016 IT Preview を Azure でテストする](http://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/)」を参照してください。

## 次のステップ

- [フェーズ 1](virtual-machines-workload-intranet-sharepoint-phase1.md) を使用して、このワークロードの構成を開始します。

<!---HONumber=AcomDC_1217_2015-->