<properties
   pageTitle="Service Fabric クラスター リソース マネージャーでサービスを構成する"
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
   ms.date="03/03/2016"
   ms.author="masnider"/>


# サービスの構成
Service Fabric クラスター リソース マネージャーを使用すると、すべての名前付きサービスを個別に制御するルールを非常にきめ細かく制御できます。各サービス インスタンスでは、クラスターでの割り当て方法に関する具体的なルールを指定でき、報告を受ける必要があるメトリックのセットを、そのサービスにとってのメトリックの重要性も含めて定義できます。一般に、サービスの構成は 3 つの異なるタスクに分かれます。

1. 配置の制約の構成
2. メトリックの構成
3. 高度な配置ルールの構成 (一般的ではない)

これらについて説明します。

## 配置の制約
配置の制約は、サービスが実際に実行できるクラスター内のノードの制御に使用されます。通常は、特定の名前付きサービス インスタンス、または特定の種類のノード上で実行するように制約された種類のすべてのサービスが表示されますが、配置の制約には拡張性があります。ノードの種類ごとに任意のプロパティ セットを定義し、サービスを作成するときにプロパティに対する制約を選択します。サービスの有効期間中に配置の制約を動的に更新することもでき、クラスターでの変更に対応できます。

## メトリック
メトリックは、このサービスでバランスをとる必要があるリソースの一覧であり、サービスの各レプリカまたはインスタンスが既定で使用するリソースの量に関する情報が含まれます。また、メトリックには、トレードオフが必要な場合のために、サービスに対するメトリックの重要度を示す重みも含まれます。

## 配置に関するその他のルール
地理的に分散しているクラスターや、他のやや特殊なシナリオにおいて主に使用される、他の種類の配置ルールがあります。これらは、相関関係またはポリシーによって構成されます。このようなルールを使用するシナリオは多くありませんが、完全を期すために説明しておきます。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ
- [メトリックの詳細](service-fabric-cluster-resource-manager-metrics.md)
- [サービス アフィニティについて](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
- [サービス配置ポリシーについて](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
- [Service Fabric クラスター リソース マネージャーの概要](service-fabric-cluster-resource-manager-introduction.md)

<!---HONumber=AcomDC_0309_2016-->