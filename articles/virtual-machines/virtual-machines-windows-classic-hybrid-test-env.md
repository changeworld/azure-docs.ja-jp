<properties
	pageTitle="Azure のハイブリッド クラウド テスト環境 | Microsoft Azure"
	description="Azure ベースのハイブリッド クラウドに対する開発/テストまたは概念実証を行う IT プロフェッショナル向けの環境を構築する方法が説明されている記事を示します。"
	documentationCenter=""
	services="virtual-machines-windows"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="index-page"
	ms.date="01/12/2016"
	ms.author="josephd"/>

# Azure ハイブリッド クラウド テスト環境

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]リソース マネージャー モデル。


開発/テスト環境または概念実証のハイブリッド クラウド テスト環境向けに、ローカルのインターネット接続といずれかのパブリック IP アドレスを使用して、実用的なクロスプレミス Azure Virtual Network (VNet) を段階的に設定します。設定が完了したら、アプリケーションの開発とテストの実行や、簡略化された IT ワークロードでの実験ができます。また、サイト間の仮想プライベート ネットワーク (VPN) 接続のパフォーマンスをインターネット上の場所を基準に測定することができます。

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


## 次のステップ

- Azure インフラストラクチャ サービスでカスタムの開発/テスト デプロイメントまたは運用環境デプロイメントを設計するための[実装ガイドライン](virtual-machines-linux-infrastructure-service-guidelines.md)を確認します。

<!---HONumber=AcomDC_0323_2016-->