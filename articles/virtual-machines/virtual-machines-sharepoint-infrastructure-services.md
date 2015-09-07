<properties
	pageTitle="Azure の SharePoint Server 2013 ファーム | Microsoft Azure"
	description="Microsoft Azure で開発/テスト環境または運用 SharePoint Server 2013 ファームをセットアップする方法について説明する記事を紹介します。"
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sharepoint"
	ms.devlang="na"
	ms.topic="index-page"
	ms.date="07/21/2015"
	ms.author="josephd"/>

# Azure インフラストラクチャ サービスでホストされる SharePoint ファーム

Microsoft Azure インフラストラクチャ サービスで、最初または次の開発/テスト環境または運用環境の SharePoint ファームを設定します。ここでは、簡単に構成できる機能と、新機能を追加したり主要な機能を最適化するためにファームを迅速に拡張する機能を活用することができます。

## 基本的な SharePoint 開発/テスト ファーム

サービス管理内で作成された仮想マシンについては、Azure プレビュー ポータルの [SharePoint Server ファーム](virtual-machines-sharepoint-farm-azure-preview.md)機能を使用することで、インターネットに接続した SharePoint Web サイトに対応した、基本的な開発/テスト ファームを作成することができます。

自動作成されるこの環境は、クラウド専用の Azure Virtual Network 内のドメイン コントローラー、SQL Server、および SharePoint サーバーの 3 つのサーバーで構成されています。

リソース マネージャーで作成された仮想マシンについて同様の構成を作成するには、テンプレートに使用します。「[Deploy a three-server SharePoint farm (3 つのサーバーで構成された SharePoint ファームのデプロイ)](virtual-machines-workload-template-sharepoint.md#deploy-a-three-server-sharepoint-farm)」参照してください。

## 高可用性 SharePoint 開発/テスト ファーム

サービス管理内で作成された仮想マシンについては、Azure プレビュー ポータルの [SharePoint Server ファーム](virtual-machines-sharepoint-farm-azure-preview.md)機能を使用することで、インターネットに接続した SharePoint Web サイトに対応した、高可用性 SharePoint 開発/テスト ファームを作成することができます。

自動作成されるこの環境は、クラウド専用の Azure Virtual Network 内の 9 つのサーバーで構成されています。内訳は、ドメイン コントローラー 2 つ、SQL サーバー クラスター 3 つ、アプリケーション層の SharePoint サーバー 2 つ、Web 層の SharePoint サーバー 2 つです。

リソース マネージャーで作成された仮想マシンについて同様の構成を作成するには、テンプレートに使用します。「[Deploy a nine-server SharePoint farm (9 つのサーバーで構成された SharePoint ファームのデプロイ)](virtual-machines-workload-template-sharepoint.md#deploy-a-nine-server-sharepoint-farm)」を参照してください。

## ハイブリッド クラウドの開発/テスト ファーム

[ハイブリッド クラウドの開発/テスト環境での SharePoint イントラネット ファーム](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)では、シミュレートされたハイブリッド クラウド構成を作成します。この構成はシンプルな 2 階層の SharePoint ファームをホストし、このファームを使用して、Azure でホストされているイントラネット SharePoint ファームをインターネット上の自分の場所からテストできます。

この構成では、サービス管理内で作成された仮想マシンが使用されます。

## 高可用性イントラネット SharePoint 運用ファーム

[SQL Server の AlwaysOn 可用性グループを備えた SharePoint 2013 を Azure でデプロイ](virtual-machines-workload-intranet-sharepoint-overview.md)することで、運用環境で利用できる、高可用性イントラネット SharePoint Server 2013 ファームを Azure で構築できます。

この構成では、サービス管理内で作成された仮想マシンが使用されます。

## その他のリソース

Azure における SharePoint の詳細と構成については、次のリソースを参照してください。

- [SharePoint 2013 用の Microsoft Azure アーキテクチャ](https://technet.microsoft.com/library/dn635309.aspx)

- [SharePoint Server 2013 を使用した Microsoft Azure のインターネット サイト](https://technet.microsoft.com/library/dn635307.aspx)

- [Microsoft Azure での SharePoint Server 2013 の障害復旧](https://technet.microsoft.com/library/dn635313.aspx)

- [SharePoint 2013 認証に Microsoft Azure Active Directory を使用する](https://technet.microsoft.com/library/dn635311.aspx)

- [Microsoft Azure での Office 365 ディレクトリ同期 (DirSync) のデプロイ](https://technet.microsoft.com/library/dn635310.aspx)

<!---HONumber=August15_HO9-->