<properties
   pageTitle="Azure リソース マネージャー テンプレートを使用して Service Fabric クラスターをセットアップする | Microsoft Azure"
   description="Azure リソース マネージャー テンプレートを使用して Service Fabric クラスターをセットアップします。"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/29/2016"
   ms.author="chackdan"/>


# Azure リソース マネージャー テンプレートを使用して Service Fabric クラスターをセットアップする

ここでは、Azure リソース マネージャー テンプレートを使用して Azure Service Fabric クラスターをセットアップする方法について説明します。セットアップするには、サブスクリプションに、このクラスターを構成する IaaS VM をデプロイできるだけのコア数が割り当てられている必要があります。

## 前提条件

- セキュリティで保護されたクラスターをセットアップするには、まず X.509 証明書を Key Vault にアップロードする必要があります。ソース コンテナーの URL、証明書の URL、証明書の拇印が必要になります。
- セキュリティで保護されたクラスターをセットアップする方法の詳細については、「[Service Fabric クラスターのセキュリティ](service-fabric-cluster-security.md)」を参照してください。

## サンプル リソース マネージャー テンプレートを取得する

サンプル リソース マネージャー テンプレートは、[GitHub の Azure クイック スタート テンプレート ギャラリー](https://github.com/Azure/azure-quickstart-templates)で入手できます。すべての Service Fabric テンプレート名の先頭には "service-fabric.." と付きます。リポジトリを検索するには、"fabric" を検索するか、サンプル テンプレートのセットまでスクロールします。探しているテンプレートをすばやく見つけることができるように、テンプレートには次のように名前が付けられています。

- [service-fabric-unsecure-cluster-5-node-1-nodetype](http://go.microsoft.com/fwlink/?LinkId=716923): セキュリティで保護されていない 5 ノード単一ノード クラスター テンプレートを示しています。

- [service-fabric-secure-cluster-5-node-1-nodetype-wad](http://go.microsoft.com/fwlink/?LinkID=716924): WAD が有効化された、セキュリティで保護された 5 ノード単一ノード クラスター テンプレートを示しています。

- [service-fabric-secure-cluster-10-node-2-nodetype-wad](http://go.microsoft.com/fwlink/?LinkId=716925): WAD が有効化された、セキュリティで保護された 10 ノード 2 ノード クラスター テンプレートを示しています。

## カスタム リソース マネージャー テンプレートを作成する

カスタム リソース マネージャー テンプレートを作成するには、2 つの方法があります。

1. [GitHub の Azure クイック スタート テンプレート ギャラリー](https://github.com/Azure/azure-quickstart-templates)からサンプル テンプレートを入手し、テンプレートに変更を加えます。

2. Azure ポータルにサインインし、Service Fabric ポータル ページを使用して、カスタマイズするテンプレートを生成します。そのためには、次の手順に従います。

    a.[Azure ポータル](https://portal.azure.com/)にサインインします。

    b.「[Azure ポータルからの Service Fabric クラスターのセットアップ](service-fabric-cluster-creation-via-portal.md)」を参照し、クラスターの作成プロセスを実行します。ただし、**[作成]** をクリックせずに、**[概要]** をクリックし、次のスクリーン ショットのようにテンプレートをダウンロードします。

 ![リソース マネージャー テンプレートをダウンロードするリンクが表示された Service Fabric クラスター ページのスクリーン ショット][DownloadTemplate]

## Azure PowerShell を使用してリソース マネージャー テンプレートを Azure にデプロイする

PowerShell を使用してテンプレートをデプロイする詳細な手順については、「[PowerShell を使用してテンプレートをデプロイする](resource-group-template-deploy.md)」を参照してください。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ
- [Service Fabric クラスターのセキュリティ](service-fabric-cluster-security.md)
- [Visual Studio での Service Fabric アプリケーションの管理](service-fabric-manage-application-in-visual-studio.md)。
- [Service Fabric の正常性モデルの概要](service-fabric-health-introduction.md)

<!--Image references-->
[DownloadTemplate]: ./media/service-fabric-cluster-creation-via-arm/DownloadTemplate.png

<!---HONumber=AcomDC_0204_2016-->