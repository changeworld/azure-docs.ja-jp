<properties
   pageTitle="Service Fabric Explorer を使用したクラスターの視覚化"
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
   ms.date="08/05/2015"
   ms.author="jesseb"/>

# Service Fabric Explorer を使用したクラスターの視覚化

Service Fabric Explorer は、Microsoft Azure Service Fabric クラスター内のクラウド アプリケーションとノードを検査および管理するための視覚的なツールです。Service Fabric Explorer は、ローカル デプロイメント クラスターと Azure クラスターの両方に接続できます。Service Fabric PowerShell コマンドレットについては、「**次のステップ**」を参照してください。

> [AZURE.NOTE]Azure での Service Fabric クラスターの作成機能は、まだ用意されていません。

## Service Fabric Explorer の概要

ローカル デプロイメント環境は、「[set up your Service Fabric development environment (Service Fabric デプロイメント環境の設定)](service-fabric-get-started.md)」の手順に従って設定されている必要があります。

ローカルのインストール パス (%Program Files%\\Microsoft SDKs\\Service Fabric\\Tools\\ServiceFabricExplorer\\ServiceFabricExplorer.exe) から Service Fabric Explorer を実行します。ツールはローカル デプロイメント クラスター (存在する場合) に自動的に接続します。クラスターに関する次のような情報が表示されます。

- クラスターで実行されているアプリケーション
- クラスターのノードに関する情報
- アプリケーションとノードで発生した正常性イベント
- クラスター内のアプリケーションの負荷
- アプリケーションのアップグレード状態の監視

![Service Fabric クラスターとデプロイ済みアプリケーションの視覚的な表現][servicefabricexplorer]

重要な視覚化の 1 つに、クラスター マップがあります。クラスター マップは、(たとえば **[Onebox/ローカル クラスター]** をクリックすると) クラスターのダッシュボードに表示されます。クラスター マップには、一連のアップグレード ドメインと障害ドメインが表示され、どのノードがどのドメインにマッピングされているかが示されます。Service Fabric の主要な概念については、「[Service Fabric の技術概要](service-fabric-technical-overview.md)」を参照してください。

![クラスター マップには、各ノードが所属しているアップグレード ドメインと障害ドメインが表示されます。][clustermap]


## アプリケーションとサービスの表示

Service Fabric Explorer を使用して、クラスターで実行されているアプリケーションについて確認することができます。**[アプリケーション ビュー]** を展開して、アプリケーション、サービス、パーティション、レプリカに関する詳細情報を表示します。

以下の図は、**"fabric:/Stateful1Application"** という名前のアプリケーションに、**"fabric:/Stateful1Application/MyFrontEnd"** という 1 つのステートレス サービスと、**"fabric:/Stateful1Application/Stateful1"** という 1 つのステートフル サービスがあることを示しています。ステートレス サービスにはパーティションが 1 つあり、1 つのレプリカが **Node.4** で実行されています。ステートフル サービスにはパーティションが 2 つあり、それぞれに 3 つずつレプリカがあって、複数の異なるノードで実行されています。

![Service Fabric クラスターで実行jされているアプリケーションの表示][applicationview]

アプリケーション、サービス、パーティション、またはレプリカをクリックすると、そのエンティティの詳細情報が表示されます。以下の図は、ステートフル サービスのいずれかのプライマリ レプリカを表すサービス レプリカの正常性ダッシュボードを示しています。表示される情報は、レプリカのロール、レプリカが実行されているノード、リッスンしているアドレス、ディスク上のファイルの場所、正常性イベントなどです。

![Service Fabric レプリカに関する詳細情報][replicadetails]


## リモート Service Fabric クラスターへの接続

リモートの Service Fabric クラスターを表示するには、**[接続]** をクリックして **[Service Fabric Cluster への接続]** ダイアログを表示します。クラスターの **ServiceFabric エンドポイント**を入力し、**[接続]** をクリックします。Service Fabric エンドポイントは一般的に、ポート 19000 でリッスンしているクラスター サービスの公開名です。

![リモート Service Fabric クラスターへの接続の設定][connecttocluster]


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
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

<!---HONumber=August15_HO6-->