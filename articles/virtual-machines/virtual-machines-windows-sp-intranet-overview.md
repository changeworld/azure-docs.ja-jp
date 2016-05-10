<properties
	pageTitle="SharePoint Server 2013 ファームのデプロイ | Microsoft Azure"
	description="5 つのフェーズで、Azure の SQL Server AlwaysOn 可用性グループを使用して、高可用性 SharePoint Server 2013 ファームをデプロイします。"
	documentationCenter=""
	services="virtual-machines-windows"
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
	ms.date="04/25/2016"
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

![](./media/virtual-machines-windows-sp-intranet-overview/workload-spsqlao_05.png)

ロールごとに 2 台のマシンで高可用性を保証します。すべての仮想マシンは 1 つのリージョンに存在します。特定のロール用の仮想マシンの各グループは、専用の可用性セットに含まれます。

## 部品表

この基本的な構成には、次の一連の Azure サービスとコンポーネントが必要です。

- 9 つの仮想マシン
- 4 つの追加データ ディスク (ドメイン コント ローラーと、SQL サーバーを実行する仮想マシン用)
- 4 つの可用性セット
- 1 つのクロスプレミス仮想ネットワーク
- 1 つのストレージ アカウント
- 1 つの Azure サブスクリプション

こちらが仮想マシンとそれぞれのこの構成に対する既定のサイズになります。

項目 | 仮想マシンの説明 | ギャラリー イメージ | 既定サイズ
--- | --- | --- | ---
1\. | 最初のドメイン コントローラー | Windows Server 2012 R2 Datacenter | A2
2\. | 2 番目のドメイン コントローラー | Windows Server 2012 R2 Datacenter | A2
3\. | 最初のデータベース サーバー | Microsoft SQL Server 2014 Enterprise - Windows Server 2012 R2 | A5
4\. | 2 番目のデータベース サーバー | Microsoft SQL Server 2014 Enterprise - Windows Server 2012 R2 | A5
5\. | クラスターのマジョリティ ノード | Windows Server 2012 R2 Datacenter | A1
6\. | 最初の SharePoint アプリケーション サーバー | Microsoft SharePoint Server 2013 評価版 - Windows Server 2012 R2 | A4
7\. | 2 番目の SharePoint アプリケーション サーバー | Microsoft SharePoint Server 2013 評価版 - Windows Server 2012 R2 | A4
8\. | 最初の SharePoint Web サーバー | Microsoft SharePoint Server 2013 評価版 - Windows Server 2012 R2 | A4
9\. | 2 番目の SharePoint Web サーバー | Microsoft SharePoint Server 2013 評価版 - Windows Server 2012 R2 | A4

この構成の推定コストを計算するには、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を参照してください。

1. [**モジュール**] で、[**コンピューティング**] をクリックし、[**仮想マシン**] を必要なだけクリックして 9 つの仮想マシンを作成してください。
2. 各仮想マシンで次を選択してください。
	- 目的のリージョン
	- 種類には **Windows**
	- 価格レベルには **Standard**
	- **インスタンスのサイズ**には、上記の表での既定のサイズもしくは目的のサイズ

> [AZURE.NOTE] Azure 料金計算ツールでは、SQL Server 2014 Enterprise を実行している 2 つの仮想マシンの SQL Server ライセンスの追加のコストは含まれません。詳細については、「[Virtual Machines の価格](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql)」の SQL Server に関するセクションを参照してください。

## デプロイのフェーズ

この構成を次のフェーズでデプロイします。

- [フェーズ 1: Azure を構成する](virtual-machines-windows-ps-sp-intranet-ph1.md)。ストレージ アカウント、可用性セット、およびクロスプレミス仮想ネットワークを作成します。
- [フェーズ 2: ドメイン コントローラーを構成する](virtual-machines-windows-ps-sp-intranet-ph2.md)。レプリカ Active Directory ドメイン サービス (AD DS) ドメイン コントローラーを作成して構成します。
- [フェーズ 3: SQL Server インフラストラクチャを構成する](virtual-machines-windows-ps-sp-intranet-ph3.md)。SQL Server 仮想マシンを作成して構成し、SharePoint 用に準備して、クラスターを作成します。
- [フェーズ 4: SharePoint サーバーを構成する](virtual-machines-windows-ps-sp-intranet-ph4.md)。4 つの SharePoint 仮想マシンを作成して構成します。
- [フェーズ 5: 可用性グループを作成して SharePoint データベースを追加する](virtual-machines-windows-ps-sp-intranet-ph5.md)。データベースを準備し、SQL Server AlwaysOn 可用性グループを作成します。


この SharePoint と SQL Server AlwaysOn のデプロイは、[SharePoint と SQL Server AlwaysOn のインフォグラフィック](http://go.microsoft.com/fwlink/?LinkId=394788)が付属し、最新の推奨事項が組み込まれるように設計されています。

この構成は、Azure インフラストラクチャ サービスに機能する高可用性のイントラネット SharePoint ファームを作成するための、事前に定義されたアーキテクチャの規範的なフェーズ単位のガイドです。Azure への SharePoint 2013 の実装に関する他のアーキテクチャ ガイダンスについては、「[SharePoint 2013 用の Microsoft Azure アーキテクチャ](https://technet.microsoft.com/library/dn635309.aspx)」を参照してください。

次の点に注意してください。

- SharePoint の実装に関する経験が豊富な場合は、フェーズ 3 ～ 5 の指示を自由に調整して、ニーズに最も適したファームを構築してください。
- 既存の Azure ハイブリッド クラウド デプロイが既にある場合は、フェーズ 1 および 2 の指示を自由に調整するかスキップし、適切なサブネットで新しい SharePoint ファームをホストしてください。
- すべてのサーバーは、Azure Virtual Network 内の 1 つのサブネット上の存在します。サブネットの分離に相当する追加のセキュリティを提供する場合は、[ネットワーク セキュリティ グループ](../virtual-network/virtual-networks-nsg.md)を使用できます。

この構成の開発/テスト環境または概念実証環境を構築する場合は、「[テスト用のハイブリッド クラウドでの SharePoint イントラネット ファームの設定](virtual-machines-windows-ps-hybrid-cloud-test-env-sp.md)」を参照してください。

SharePoint と SQL Server AlwaysOn 可用性グループの追加情報については、[SQL Server 2012 の AlwaysOn 可用性グループを SharePoint 2013 用に構成する](https://technet.microsoft.com/library/jj715261.aspx)を参照してください。

## 次のステップ

- [フェーズ 1](virtual-machines-windows-ps-sp-intranet-ph1.md) を使用して、このワークロードの構成を開始します。

<!---HONumber=AcomDC_0427_2016-->