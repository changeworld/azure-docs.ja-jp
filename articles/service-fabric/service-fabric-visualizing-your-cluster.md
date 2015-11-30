<properties
   pageTitle="Service Fabric Explorer を使用したクラスターの視覚化 | Microsoft Azure"
   description="Service Fabric Explorer は、Microsoft Azure Service Fabric クラスター内のクラウド アプリケーションとノードを検査および管理するための便利な GUI ツールです。"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/30/2015"
   ms.author="jesseb"/>

# Service Fabric Explorer を使用したクラスターの視覚化

Service Fabric Explorer は、Service Fabric クラスター内のアプリケーションとノードを検査および管理するための Web ベースのツールです。Service Fabric Explorer は、Service Fabric クラスター内で直接ホストされるので、ローカル クラスターが実行されているかどうかに関係なく、いつでも使用できます。

## Service Fabric Explorer への接続

[開発環境を準備する](service-fabric-get-started.md)手順に従った場合は、http://localhost:19080/Explorer に移動することで、ローカル クラスター上で Service Fabric Explorer を起動できます。

>[AZURE.NOTE]Internet Explorer (IE) と Service Fabric Explorer を併用してリモート クラスターを管理する場合は、IE の一部の設定を構成する必要があります。**[ツール] -> [互換表示設定]** に移動し、**[イントラネット サイトを互換表示で表示する]** チェック ボックスをオフにして、すべての情報が正しく読み込まれるようにします。

## Service Fabric Explorer のレイアウトについて

左側のツリーを使用して、Service Fabric Explorer 内を移動できます。ツリーのルートでは、クラスター ダッシュボードにクラスターの概要 (アプリケーションとノードの正常性の概要など) が表示されます。

![Service Fabric Explorer のクラスター ダッシュボード][sfx-cluster-dashboard]

クラスターには、2 つのサブツリーが含まれています。1 つはアプリケーション、もう 1 つはノードのサブツリーです。

### アプリケーションとサービスの表示

アプリケーション ビューでは、Service Fabric の論理階層 (アプリケーション、サービス、パーティション、レプリカ) 内を移動できます。

次の例では、**MyApp** アプリケーションは、**MyStatefulService** と **WebSvcService** の 2 つのサービスで構成されています。**MyStatefulService** はステートフルであるため、1 つのプライマリ レプリカと 2 つのセカンダリ レプリカがあるパーティションが 1 つ含まれています。これに対して、WebSvcService はステートレスであり、インスタンスが 1 つだけ含まれています。

![Service Fabric Explorer のアプリケーション ビュー][sfx-application-tree]

ツリーの各レベルでは、メイン ウィンドウにその項目の関連情報が表示されます。たとえば、特定のサービスの正常性状態とバージョンを確認できます。

![Service Fabric Explorer のメイン ウィンドウ][sfx-service-essentials]

### クラスターのノードの表示

ノード ビューには、クラスターの物理的なレイアウトが表示されます。特定のノードについて、そのノードでコードがデプロイされているアプリケーション、正確に言うと、そこで現在実行されているレプリカを調べることができます。

## Service Fabric Explorer を使用した操作の実行

Service Fabric Explorer では、クラスター内のノード、アプリケーション、サービスに対する操作を簡単に呼び出すことができます。

たとえば、アプリケーション インスタンスを削除するには、左側のツリーでアプリケーションを選択し、[操作]、[アプリケーションの削除] の順に選択します。

![Service Fabric Explorer でのアプリケーションの削除][sfx-delete-application]

多くの操作は破壊的であるため、操作を実行する前に確認を求められます。

>[AZURE.NOTE]Service Fabric Explorer を使用して実行できるすべての操作は、PowerShell または REST API を使用して実行し、自動化することもできます。



## リモート Service Fabric クラスターへの接続

Service Fabric Explorer は Web ベースであり、クラスター内で実行されるので、クラスターのエンドポイントがわかっており、エンドポイントにアクセスするための十分なアクセス許可があれば、どのブラウザーからでもアクセスできます。

### リモート クラスターの Service Fabric Explorer エンドポイントの検出

クラスター エンドポイントは、Service Fabric ポータルから検出できます。特定のクラスターの Service Fabric Explorer に到達するには、次のようにポート 19007 でそのエンドポイントに接続します。

http://&lt;your-cluster-endpoint&gt;:19007

### セキュリティで保護されたクラスターへの接続

Service Fabric クラスターに接続するために、クライアントに証明書の提示を要求することによって、Service Fabric クラスターへのアクセスを制御できます。

セキュリティで保護されたクラスターで Service Fabric Explorer に接続しようとすると、アクセスを取得するために、ブラウザーから証明書の提示を求められます。

## 次のステップ

- [テスト可能性の概要](service-fabric-testability-overview.md)。
- [Visual Studio での Service Fabric アプリケーションの管理](service-fabric-manage-application-in-visual-studio.md)。
- [PowerShell を使用した Service Fabric アプリケーションのデプロイメント](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[applicationview]: ./media/service-fabric-visualizing-your-cluster/applicationview.png
[clustermap]: ./media/service-fabric-visualizing-your-cluster/clustermap.png
[connecttocluster]: ./media/service-fabric-visualizing-your-cluster/connecttocluster.png
[replicadetails]: ./media/service-fabric-visualizing-your-cluster/replicadetails.png
[servicefabricexplorer]: ./media/service-fabric-visualizing-your-cluster/servicefabricexplorer.png
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/SfxClusterDashboard.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/SfxApplicationTree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/SfxServiceEssentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/SfxDeleteApplication.png

<!---HONumber=Nov15_HO4-->