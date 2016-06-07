<properties
   pageTitle="Service Fabric クラスター リソース マネージャーでサービスを構成する | Microsoft Azure"
   description="メトリック、配置の制約、およびその他の配置ポリシーを指定することによる Service Fabric サービスの記述。"
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/20/2016"
   ms.author="masnider"/>


# Service Fabric サービスのためにクラスター リソース マネージャー設定を構成する
Service Fabric クラスター リソース マネージャーを使用すると、すべての名前付きサービスを個別に制御するルールを非常にきめ細かく制御できます。各サービス インスタンスでは、クラスターでの割り当て方法に関する具体的なルールを指定でき、報告を受ける必要があるメトリックのセットを、そのサービスにとってのメトリックの重要性も含めて定義できます。一般に、サービスの構成は 3 つの異なるタスクに分かれます。

1. 配置の制約の構成
2. メトリックの構成
3. 高度な配置ルールの構成 (一般的ではない)

これらについて説明します。

## Placement constraints
配置の制約は、サービスが実際に実行できるクラスター内のノードの制御に使用されます。通常は、特定の名前付きサービス インスタンス、または特定の種類のノード上で実行するように制約された種類のすべてのサービスが表示されますが、配置の制約には拡張性があります。ノードの種類ごとに任意のプロパティ セットを定義し、サービスを作成するときにプロパティに対する制約を選択します。サービスの有効期間中に配置の制約を動的に更新することもでき、クラスターでの変更に対応できます。配置の制約の詳細とその構成方法については、[この記事](service-fabric-cluster-resource-manager-cluster-description.md#placement-constraints-and-node-properties)を参照してください。

## メトリック
メトリックは、このサービスでバランスをとる必要があるリソースの一覧であり、サービスの各レプリカまたはインスタンスが既定で使用するリソースの量に関する情報が含まれます。また、メトリックには、トレードオフが必要な場合のために、サービスに対するメトリックの重要度を示す重みも含まれます。

## 配置に関するその他のルール
地理的に分散しているクラスターや、他のやや特殊なシナリオにおいて主に使用される、他の種類の配置ルールがあります。これらは、相関関係またはポリシーによって構成されます。このようなルールを使用するシナリオは多くありませんが、完全を期すために説明しておきます。

## 次のステップ
- メトリックは、Service Fabric クラスター リソース マネージャーが管理するクラスターの利用量と容量を表します。メトリックの詳細とその構成方法については、[この記事](service-fabric-cluster-resource-manager-metrics.md)を参照してください。
- アフィニティは、サービスに構成できるモードの 1 つです。一般的ではありませんが、必要な場合、[こちら](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)で詳細を確認できます。
- さまざまな配置ルールをサービスで構成し、追加のシナリオに対処できます。さまざまな配置ルールの詳細は[ここ](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)にあります。
- 最初から開始して、[Service Fabric クラスター リソース マネージャーの概要を確認するにはこちらを参照してください](service-fabric-cluster-resource-manager-introduction.md)。
- クラスター リソース マネージャーでクラスターの負荷を管理し、分散するしくみについては、[負荷分散](service-fabric-cluster-resource-manager-balancing.md)に関する記事を参照してください。
- クラスター リソース マネージャーには、クラスターを記述するためのさまざまなオプションがあります。オプションの詳細については、[Service Fabric クラスターの記述](service-fabric-cluster-resource-manager-cluster-description.md)に関する記事を参照してください。

<!---HONumber=AcomDC_0525_2016-->