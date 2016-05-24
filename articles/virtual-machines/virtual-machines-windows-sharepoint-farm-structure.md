<properties
	pageTitle="Azure の SharePoint Server 2013 ファーム | Microsoft Azure"
	description="Microsoft Azure で開発/テスト環境または運用 SharePoint Server 2013 ファームをセットアップする方法について説明する記事を紹介します。"
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
	ms.topic="index-page"
	ms.date="04/25/2016"
	ms.author="josephd"/>

# Azure インフラストラクチャ サービスでホストされる SharePoint ファーム

[AZURE.INCLUDE [learn-about-deployment-models-both-include](../../includes/learn-about-deployment-models-both-include.md)]

Microsoft Azure インフラストラクチャ サービスで、最初または次の開発/テスト環境または運用環境の SharePoint Server 2013 ファームを設定します。ここでは、簡単に構成できる機能と、新機能を追加したり主要な機能を最適化するためにファームを迅速に拡張する機能を活用することができます。

## 基本的な SharePoint 開発/テスト ファーム

自動作成されるこの環境は、クラウド専用の Azure Virtual Network 内のドメイン コントローラー、SQL Server、および SharePoint サーバーの 3 つのサーバーで構成されています。

Azure ポータルの Azure Marketplace の「[SharePoint 2013 非 HA ファーム](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/)」項目を参照してください。これは、インターネットに接続された SharePoint Web サイトの基本的な開発/テスト ファームを作成します。詳細については、「[SharePoint サーバー ファームの作成](virtual-machines-windows-sharepoint-farm.md)」を参照してください。

Azure リソース マネージャーのテンプレートを使用することもできます。[SharePoint](virtual-machines-linux-app-frameworks.md) に関するページをご覧ください。

> [AZURE.NOTE] Azure ポータルの Azure Marketplace の **SharePoint サーバー ファーム**項目は削除されました。

## 高可用性 SharePoint 開発/テスト ファーム

この自動作成されるこの環境は、クラウド専用の Azure Virtual Network 内の 9 つのサーバーで構成されています。内訳は、ドメイン コントローラー 2 つ、SQL サーバー クラスター 3 つ、アプリケーション層の SharePoint サーバー 2 つ、Web 層の SharePoint サーバー 2 つです。

Azure ポータルの Azure Marketplace の「[SharePoint 2013 HA ファーム](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/)」項目を参照してください。これは、インターネットに接続された SharePoint Web サイトの高可用性の開発/テスト ファームを作成します。詳細については、「[SharePoint サーバー ファームの作成](virtual-machines-windows-sharepoint-farm.md)」を参照してください。

Azure リソース マネージャーのテンプレートを使用することもできます。「[Deploy a nine-server SharePoint farm (9 つのサーバーで構成された SharePoint ファームのデプロイ)](virtual-machines-windows-app-frameworks.md#deploy-a-nine-server-sharepoint-farm)」を参照してください。

> [AZURE.NOTE] Azure ポータルの Azure Marketplace の **SharePoint サーバー ファーム**項目は削除されました。

## ハイブリッド クラウドの開発/テスト ファーム

[ハイブリッド クラウドの開発/テスト環境での SharePoint イントラネット ファーム](virtual-machines-windows-ps-hybrid-cloud-test-env-sp.md)では、シミュレートされたハイブリッド クラウド構成を作成します。この構成はシンプルな 2 階層の SharePoint ファームをホストし、このファームを使用して、Azure でホストされているイントラネット SharePoint ファームをインターネット上の自分の場所からテストできます。

## 高可用性イントラネット SharePoint 運用ファーム

[Azure で SharePoint 2013 と SQL Server AlwaysOn 可用性グループ](virtual-machines-windows-sp-intranet-overview.md)をデプロイすることで、運用環境で利用できる、高可用性のイントラネット SharePoint Server 2013 ファームを Azure で構築できます。

## 次のステップ

- Azure インフラストラクチャ サービスで、追加の [SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx) 構成を検出します。

<!---HONumber=AcomDC_0511_2016-->