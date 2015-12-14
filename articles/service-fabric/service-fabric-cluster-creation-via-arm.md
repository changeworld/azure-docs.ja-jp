<properties
   pageTitle="ARM テンプレートを使用した Service Fabric クラスターのセットアップ | Microsoft Azure"
   description="ARM テンプレートを使用した Service Fabric クラスターのセットアップ。"
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
   ms.date="11/19/2015"
   ms.author="chackdan"/>

# ARM テンプレートを使用した Service Fabric クラスターのセットアップ

このページは、ARM テンプレートを使用して Service Fabric クラスターをセットアップする際に役立ちます。サブスクリプションに、このクラスターを構成する IaaS VM をデプロイできるだけのコア数が割り当てられていることが前提となります。

## 前提条件

- セキュリティで保護されたクラスターをセットアップする場合は、x509 証明書が Key Vault にアップロードされていることを確認します。Source Vault URL、証明書 URL、証明書の拇印が必要になります。
-  方法の詳細については、[Service Fabric クラスターのセキュリティ](service-fabric-cluster-security.md)に関するページをご覧ください。

## サンプル ARM テンプレートの入手

1. サンプル ARM テンプレートは、[github の Azure クイック スタート テンプレート ギャラリー](https://github.com/Azure/azure-quickstart-templates)で入手できます。Service Fabric テンプレートはすべて、名前が "service-fabric-cluster-.." で始まります。リポジトリで "fabric" を検索するか、サンプル テンプレートのセットが表示されるまで下にスクロールします。
2. 探しているテンプレートをすばやく見つけることができるように、テンプレートには次のように名前が付けられています。
	1. [service-fabric-cluster-5-node-1-nodetype](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-cluster-5-node-1-nodetype): セキュリティで保護されていない 5 ノード単一ノード クラスター テンプレートを示しています。
	2. [service-fabric-cluster-5-node-1-nodetype-wad](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-cluster-5-node-1-nodetype-wad): WAD が有効化された、セキュリティで保護されていない 5 ノード単一ノード クラスター テンプレートを示しています。
	3. [service-fabric-cluster-5-node-secure-1-nodetype-wad](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-cluster-5-node-secure-1-nodetype-wad): WAD が有効化された、セキュリティで保護された 5 ノード単一ノード クラスター テンプレートを示しています。
	4. [service-fabric-cluster-10-node-secure-2-nodetype-wad](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-cluster-5-node-secure-1-nodetype-wad): WAD が有効化された、セキュリティで保護された 10 ノード 2 ノード クラスター テンプレートを示しています。
	

## カスタム ARM テンプレートの作成

2. ここでどちらかを選択します。 
	1. [github の Azure クイック スタート テンプレート ギャラリー](https://github.com/Azure/azure-quickstart-templates)からサンプル テンプレートを入手し、テンプレートに変更を加えます。
	2. Azure ポータルにログインし、Service Fabric ポータル ページを使用して、カスタマイズするテンプレートを生成します。このプロセスを次に示します。
3. Azure ポータル ([http://aka.ms/servicefabricportal](http://aka.ms/servicefabricportal)) にログオンします。
2. [ポータルを使用した Service Fabric クラスターの作成](service-fabric-cluster-creation-via-portal.md)に関するページの説明に従って、クラスターの作成プロセスを完了します。ただし、****作成**をクリックしないでください。代わりに、[概要] に移動し、テンプレートをダウンロードします。![DownloadTemplate][DownloadTemplate]

## Azure PS を使用した ARM テンプレートの Azure へのデプロイ

方法に関する詳しいガイダンスについては、「[PS を使用した Azure リソース マネージャーのテンプレートのデプロイ](resource-group-template-deploy.md)」をご覧ください。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ
- [Service Fabric クラスターのセキュリティ](service-fabric-cluster-security.md) 
- [Visual Studio での Service Fabric アプリケーションの管理](service-fabric-manage-application-in-visual-studio.md)。
- [Service Fabric の正常性モデルの概要](service-fabric-health-introduction.md)

<!--Image references-->
[DownloadTemplate]: ./media/service-fabric-cluster-creation-via-arm/DownloadTemplate.png

<!---HONumber=AcomDC_1203_2015-->