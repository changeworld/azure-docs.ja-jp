<properties 
	pageTitle="Azure ハイブリッド クラウド テスト環境" 
	description="Azure ハイブリッド クラウドの開発/テストまたは概念実証で使用できるテスト環境を構築する方法を説明する主要なトピックを確認します。" 
	documentationCenter="" 
	services="virtual-machines"
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/09/2015" 
	ms.author="josephd"/>

# Azure ハイブリッド クラウド テスト環境

開発/テスト環境または概念実証のハイブリッド クラウド テスト環境向けに、ローカルのインターネット接続といずれかのパブリック IP アドレスを使用して、実用的なクロスプレミス Azure Virtual Network (VNet) を段階的に設定します。設定が完了したら、アプリケーションの開発とテストの実行や、簡略化された IT ワークロードでの実験ができます。また、サイト間の仮想プライベート ネットワーク (VPN) 接続のパフォーマンスをインターネット上の場所を基準に測定することができます。

> [AZURE.NOTE]これらの記事では、サービス管理で仮想マシン、仮想ネットワーク、およびその他のリソースを作成します。

## ハイブリッド クラウドの基本構成

[ハイブリッド クラウドの基本構成](../virtual-network/virtual-networks-setup-hybrid-cloud-environment-testing.md) は次の要素で成り立っています。

- 4 つの仮想マシン (ドメイン コントローラー、アプリケーション サーバー、クライアント コンピューター、および Windows サーバーを実行してルーティングとリモート アクセスを行う VPN デバイス) を備えた、簡略化されたオンプレス ネットワーク
- レプリカ ドメイン コントローラーを備えた Azure 仮想ネットワーク
- サイト間 VPN 接続

## ハイブリッド クラウドでの SharePoint イントラネット ファーム

[ハイブリッド クラウド テスト環境における SharePoint イントラネット ファーム](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)では、SQL Server 2014 サーバーと SharePoint Server 2013 サーバーをハイブリッド クラウドの基本構成に追加します。これによって、簡略化されたオンプレミス ネットワーク上のクライアント コンピューターからアクセスできる、2 層の SharePoint ファームが作成されます。

## ハイブリッド クラウドでの Web ベースの基幹業務 (LOB) アプリケーション

[ハイブリッド クラウド テスト環境における Web ベースの LOB アプリケーション](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)では、SQL Server 2014 サーバーとインターネット インフォメーション サービス (IIS) サーバーをハイブリッド クラウドの基本構成に追加します。これによって、Web ベースの階層型 LOB アプリケーションをデプロイしてテストできるインフラストラクチャが作成されます。

## ハイブリッド クラウドでの Office 365 ディレクトリ同期 (DirSync) サーバー

[ハイブリッド クラウド テスト環境における Office 365 DirSync サーバー](../virtual-network/virtual-networks-setup-dirsync-hybrid-cloud-testing.md)では、DirSync サーバーをハイブリッド クラウドの基本構成に追加して、評価版の Office 365 サブスクリプションに対してパスワードを同期して Office 365 DirSync をデモンストレーションします。

## シミュレートされたハイブリッド クラウド テスト環境

[シミュレートされたハイブリッド テスト環境](../virtual-network/virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)は、インターネットへの直接接続やパブリック IP アドレスが容易に利用できない組織や個人のために、個別の Azure Virtual Network に簡略化されたオンプレミス ネットワークを構築して、VNet 間の VPN 通信に 2 つの仮想ネットワークを接続します。


## その他のリソース

[Azure インフラストラクチャ サービスでホストされる SharePoint ファーム](virtual-machines-sharepoint-infrastructure-services.md)

[基幹業務アプリケーション アーキテクチャの 3 次元設計図の PDF](http://download.microsoft.com/download/2/C/8/2C8EB75F-AC45-4A79-8A63-C1800C098792/MS_Arch_LOB_App_3D_pdf.pdf)

[Microsoft Azure での Office 365 ディレクトリ同期 (DirSync) のデプロイ](https://technet.microsoft.com/library/dn635310.aspx)

[Azure インフラストラクチャ サービス実装ガイドライン](virtual-machines-infrastructure-services-implementation-guidelines.md)


 

<!---HONumber=July15_HO4-->