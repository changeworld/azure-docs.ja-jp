<properties 
	pageTitle="Azure インフラストラクチャ サービスでホストされる SharePoint ファーム" 
	description="Azure インフラストラクチャ サービスで開発/テストまたは運用 SharePoint 2013 ファームをセットアップする方法について説明する主要トピックを紹介します。" 
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
	ms.date="04/06/2015" 
	ms.author="josephd"/>

# Azure インフラストラクチャ サービスでホストされる SharePoint ファーム

Microsoft Azure インフラストラクチャ サービスで、最初または次の開発/テスト環境または運用環境の SharePoint ファームを設定します。ここでは、簡単に構成できる機能と、新機能を追加したり主要な機能を最適化するためにファームを迅速に拡張する機能を活用することができます。

## 基本的な SharePoint 開発/テスト ファーム 

Azure プレビュー ポータルで [SharePoint Server ファーム](virtual-machines-sharepoint-farm-azure-preview.md) テンプレートを使用すると、インターネットに接続した SharePoint Web サイトに対応した、基本的な開発/テスト ファームを作成することができます。

自動作成されるこの環境は、クラウド専用の Azure Virtual Network 内のドメイン コントローラー、SQL Server、および SharePoint サーバーの 3 つのサーバーで構成されています。

## 可用性の高い SharePoint 開発/テスト ファーム

Azure プレビュー ポータルで [SharePoint Server ファーム](virtual-machines-sharepoint-farm-azure-preview.md) テンプレートを使用すると、インターネットに接続した SharePoint Web サイトに対応した、高可用性 SharePoint 開発/テスト ファームを作成することもできます。

自動作成されるこの環境は、クラウド専用の Azure Virtual Network 内の 9 つのサーバーで構成されています。内訳は、ドメイン コントローラー 2 つ、SQL サーバー クラスター 3 つ、アプリケーション層の SharePoint サーバー 2 つ、Web 層の SharePoint サーバー 2 つです。

## ハイブリッド クラウドの開発/テスト ファーム

[ハイブリッド クラウドの開発/テスト環境での SharePoint イントラネット ファーム](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)では、シミュレートされたハイブリッド クラウド構成を作成します。この構成はシンプルな 2 階層の SharePoint ファームをホストし、このファームを使用して、Azure でホストされているイントラネット SharePoint ファームをインターネット上の自分の場所からテストできます。

## 可用性の高いイントラネット SharePoint 運用ファーム

[SQL Server の AlwaysOn 可用性グループを備えた SharePoint 2013 を Azure でデプロイ](https://msdn.microsoft.com/library/dn275959.aspx)することで、運用環境で利用できる、可用性の高いイントラネット SharePoint Server 2013 ファームを Azure で構築できます。

## その他のリソース

[Azure インフラストラクチャ サービスでの SharePoint Server](https://msdn.microsoft.com/library/dn275955.aspx)

[Azure インフラストラクチャ サービスでの SharePoint 2013 の計画](https://msdn.microsoft.com/library/dn275958.aspx)

[SharePoint 2013 用の Microsoft Azure アーキテクチャ](https://technet.microsoft.com/library/dn635309.aspx)

<!--HONumber=52-->
