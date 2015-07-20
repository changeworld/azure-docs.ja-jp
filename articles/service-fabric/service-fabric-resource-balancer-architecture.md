<properties
   pageTitle="リソース バランサーのアーキテクチャ"
   description="Service Fabric のリソース バランサーのアーキテクチャの概要"
   services="service-fabric"
   documentationCenter=".net"
   authors="abhic"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/27/2015"
   ms.author="abhic"/>

# リソース バランサー アーキテクチャの概要

![リソース バランサーのアーキテクチャ][Image1]

Service Fabric リソース バランサーは、1 つの一元的なリソース分散サービスとすべてのノード上に存在するコンポーネントで構成されます。これは、それぞれ Service Fabric Failover Manager およびローカル Service Fabric ノードと概念的に併置されます。

ローカル エージェントは、ローカル ノードで実行されているサービスからのロード レポートの収集とバッファリングを担当します。これは、リソース バランサー サービスにこのレポートを送信し、Failover Manager とリソース バランサー (上記の 1 および 2) にエラーやその他のイベントをレポートすることを目的としています。リソース バランサー サービスと Failover Manager は連携して、システムでイベントおよびその他のイベントを読み込みます (レプリカまたはノードの障害、ロード レポート、作成または削除されるサービスおよびアプリケーションなど)。たとえば、レプリカに障害が発生した場合、Failover Manager は、異なるノードからのノード データに基づく代替場所の提案を Service Fabric リソース バランサーに要求します。同様に、新しいサービス要求を受信した場合、Failover Manager は、そのサービスを配置する場所について、リソース バランサーから推奨を要求します。これらのすべてのケースで、リソース バランサーは、さまざまなサービス構成の変更によって応答し (3)、この変更は Failover Manager によって適用されます。前の例で、Failover Manager は、最適な全体的バランスで、ノードに新しいレプリカを作成します (4)。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ

リソース分散の機能:

- [クラスターの説明](service-fabric-resource-balancer-cluster-description.md)
- [サービスの説明](service-fabric-resource-balancer-service-description.md)
- [レポートの動的読み込み](service-fabric-resource-balancer-dynamic-load-reporting.md)
- [プロアクティブ メトリックのパッキング](service-fabric-resource-balancer-proactive-metric-packing.md)
- [ノード バッファーの比率](service-fabric-resource-balancer-node-buffer-percentage.md)

[Image1]: media/service-fabric-resource-balancer-architecture/Service-Fabric-Resource-Balancer-Architecture.png
 

<!---HONumber=July15_HO2-->