<properties 
	pageTitle="基幹業務アプリケーションのデプロイ | Microsoft Azure" 
	description="Azure では、Web ベースの高可用な基幹業務アプリケーションと SQL Server AlwaysOn 可用性グループを 5 つのフェーズでデプロイできます。" 
	documentationCenter=""
	services="virtual-machines-windows" 
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
	ms.date="05/08/2016" 
	ms.author="josephd"/>

# Azure での高可用な基幹業務アプリケーションのデプロイ

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]クラシック デプロイ モデル。

この記事には、Azure インフラストラクチャ サービスに、高可用なイントラネット専用の Web ベース基幹業務アプリケーションと SQL Server AlwaysOn 可用性グループをデプロイするための詳細な手順へのリンクが含まれます。アプリケーションは、次のコンピューターでホストされます。

- 2 台の Web サーバー
- 2 台のデータベース サーバー
- 1 台のクラスター マジョリティ ノード サーバー
- 2 台のドメイン コントローラー

構成を次に示します。各サーバーの名前はプレースホルダーです。

![](./media/virtual-machines-windows-lob-overview/workload-lobapp-phase4.png)
 
ロールごとに最低 2 台のマシンを使用して、高可用性を保証します。すべての仮想マシンは、1 つの Azure の場所 (リージョンとも呼ばれます) に存在します。特定のロール用の仮想マシンの各グループは、専用の可用性セットに含まれます。

## 部品表

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

1. **[モジュール]** で、**[コンピューティング]** をクリックし、**[仮想マシン]** を必要なだけクリックして 7 つの仮想マシンを作成してください。
2. 各仮想マシンで次を選択してください。
	- 目的のリージョン
	- 種類には **Windows**
	- 価格レベルには **Standard**
	- **インスタンスのサイズ**には、上記の表での既定のサイズもしくは目的のサイズ

> [AZURE.NOTE] Azure 料金計算ツールでは、SQL Server 2014 Enterprise を実行している 2 つの仮想マシンの SQL Server ライセンスの追加のコストは含まれません。詳細については、「[Virtual Machines の価格](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql)」の SQL Server に関するセクションを参照してください。

## デプロイのフェーズ

この構成を次のフェーズでデプロイします。

- [フェーズ 1: Azure を構成する](virtual-machines-windows-ps-lob-ph1.md)。ストレージ アカウント、可用性セット、およびクロスプレミス仮想ネットワークを作成します。
- [フェーズ 2: ドメイン コントローラーを構成する](virtual-machines-windows-ps-lob-ph2.md)。レプリカ Active Directory ドメイン サービス (AD DS) ドメイン コントローラーを作成して構成します。
- [フェーズ 3: SQL Server インフラストラクチャを構成する](virtual-machines-windows-ps-lob-ph3.md)。SQL Server を実行する仮想マシンを作成して構成し、クラスターを作成して、SQL Server AlwaysOn 可用性グループを有効にします。
- [フェーズ 4: Web サーバーを構成する](virtual-machines-windows-ps-lob-ph4.md)。Web サーバー仮想マシンを 2 つ作成して構成します。
- [フェーズ 5: アプリケーション データベースを SQL Server AlwaysOn 可用性グループに追加する](virtual-machines-windows-ps-lob-ph5.md)。基幹業務アプリケーションのデータベースを準備し、SQL Server AlwaysOn 可用性グループに追加します。

このデプロイは、[基幹業務アプリケーションのアーキテクチャ ブループリント](http://msdn.microsoft.com/dn630664)が付属し、最新の推奨事項が組み込まれるように設計されています。

これは、規範となる、定義済みのアーキテクチャです。次の点に注意してください。

- Web ベースの基幹業務アプリケーションの実装に関する経験が豊富な場合は、フェーズ 3 ～ 5 の指示を自由に調整して、ニーズに最も適したアプリケーション インストラクチャを構築してください。 
- 既に Azure ハイブリッド クラウド実装がある場合は、フェーズ 1 および 2 の指示を自由に調整するかスキップし、適切なサブネットで新しいアプリケーション用の仮想マシンをホストしてください。
- すべてのサーバーは、Azure Virtual Network 内の 1 つのサブネット上の存在します。サブネットの分離に相当する追加のセキュリティを提供する場合は、[ネットワーク セキュリティ グループ](../virtual-network/virtual-networks-nsg.md)を使用できます。

この構成の開発/テスト環境または概念実証環境を構築するには、「[テスト用のハイブリッド クラウドでの Web ベース LOB アプリケーションの設定](virtual-machines-windows-ps-hybrid-cloud-test-env-lob.md)」を参照してください。

Azure の IT ワークロードの設計に関する詳細については、「[Azure インフラストラクチャ サービス実装ガイドライン](virtual-machines-linux-infrastructure-service-guidelines.md)」を参照してください。

## 次のステップ

- [フェーズ 1](virtual-machines-windows-ps-lob-ph1.md) を使用して、このワークロードの構成を開始します。

<!---HONumber=AcomDC_0518_2016-->