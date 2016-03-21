<properties
   pageTitle="リソース バランサーのアーキテクチャ | Microsoft Azure"
   description="Service Fabric リソース バランサーのアーキテクチャの概要。"
   services="service-fabric"
   documentationCenter=".net"
   authors="GaugeField"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/03/2015"
   ms.author="masnider"/>

# リソース バランサー アーキテクチャの概要

![リソース バランサーのアーキテクチャ][Image1]

Azure Service Fabric の リソース バランサーは、単一の一元化されたリソース分散サービスとすべてのノードに存在するコンポーネントにより構成されます。これらは概念的には、それぞれ Service Fabric Failover Manager とローカルの Service Fabric ノードと配置されます。

ローカル エージェントは、ローカル ノードで実行されているサービスからの負荷レポートの収集とバッファリングを担当します。これは、リソース バランサー サービスにこのレポートを送信し、Failover Manager とリソース バランサー (上記の 1 および 2) にエラーやその他のイベントをレポートすることを目的としています。リソース バランサーと Failover Manager は連動して、負荷イベントなどのシステム内のイベントに応答します。これらのイベントには、レプリカまたはノードの障害、負荷レポート、作成および削除されるサービスやアプリケーションが含まれる場合があります。

たとえば、レプリカに障害が発生した場合、Failover Manager は、異なるノードからのノード データに基づく代替場所の提案をリソース バランサーに要求します。同様に、新しいサービス要求を受信した場合、Failover Manager は、そのサービスを配置する場所について、リソース バランサーから推奨を要求します。これらすべてのケースで、リソース バランサーはさまざまなサービス構成の変更で応答します (3)。これらの変更は、Failover Manager によって実施されます。前の例で、Failover Manager は、最適な全体的バランスで、ノードに新しいレプリカを作成します (4)。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ

リソース分散機能には、次のものが含まれます。

- [クラスターの説明](service-fabric-resource-balancer-cluster-description.md)
- [サービスの説明](service-fabric-resource-balancer-service-description.md)
- [レポートの動的読み込み](service-fabric-resource-balancer-dynamic-load-reporting.md)
- [プロアクティブ メトリックのパッキング](service-fabric-resource-balancer-proactive-metric-packing.md)
- [ノード バッファーの比率](service-fabric-resource-balancer-node-buffer-percentage.md)

[Image1]: media/service-fabric-resource-balancer-architecture/Service-Fabric-Resource-Balancer-Architecture.png

<!---HONumber=AcomDC_1223_2015-->