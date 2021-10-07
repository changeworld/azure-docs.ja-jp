---
title: Availability Zones をまたがる Service Fabric マネージド クラスターのデプロイ
description: Availability Zones をまたがって Service Fabric マネージド クラスターをデプロイする方法と、ARM テンプレートで構成する方法について説明します。
ms.topic: how-to
ms.date: 5/10/2021
ms.openlocfilehash: b3c4e01ea66aa3a69b14806f2701020f1f529a5e
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129544377"
---
# <a name="deploy-a-service-fabric-managed-cluster-across-availability-zones"></a>Availability Zones をまたがる Service Fabric マネージド クラスターのデプロイ

Azure の Availability Zones は高可用性を備えたサービスで、アプリケーションとデータをデータセンターの障害から保護します。 可用性ゾーンは、Azure リージョン内に独立した電源、冷却手段、ネットワークを備えた一意の物理的な場所です。

Service Fabric マネージド クラスターは、ゾーンの回復性を提供するために、複数の Availability Zones にまたがるデプロイをサポートしています。 このように構成すると、重要なシステム サービスとアプリケーションの高可用性によって、単一障害点の発生を防ぐことができます。 Azure Availability Zones は一部のリージョンでのみ使用できます。 詳細については、「[Azure の Availability Zones の概要](../availability-zones/az-overview.md)」をご覧ください。

>[!NOTE]
>Availability Zone のスパニングは、Standard SKU クラスターでのみ使用できます。

サンプル テンプレートを使用できます。[Service Fabric クロス可用性ゾーン テンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommendations-for-zone-resilient-azure-service-fabric-managed-clusters"></a>ゾーンの回復性に優れた Azure Service Fabric マネージド クラスターに関する推奨事項
Availability Zones 間で分散された Service Fabric クラスターでは、クラスター状態の高可用性が確保されます。 

マネージド クラスターの推奨トポロジーには、以下のリソースが必要です。

* クラスター SKU は Standard である必要があります。
* 最適な回復性を得るには、プライマリ ノード タイプに少なくとも 9 つのノードが必要ですが、サポートされる最小数は 6 つです。
* 最適な回復性を得るには、セカンダリ ノード タイプに少なくとも 6 つのノードが必要ですが、サポートされる最小数は 3 つです。

>[!NOTE]
>サポートされている Availability Zone のデプロイは 3 つのみです。

>[!NOTE]
> スパンされていないクラスターからスパンされたクラスターへのマネージド クラスターの一括変更を実行することはできません。

Azure Service Fabric 可用性ゾーン Availability Zone のアーキテクチャを示す図![Azure Service Fabric 可用性ゾーン Availability Zone のアーキテクチャを示す図][sf-multi-az-arch]

ゾーンにまたがる仮想マシン スケール セット内の FD と UD の形式を示すサンプル ノード リスト

 ![ゾーンにまたがる仮想マシン スケール セット内の FD と UD の形式を示すサンプル ノード リスト。][sfmc-multi-az-nodes]

**ゾーン間でのサービス レプリカの分散**: ゾーンにまたがる nodeTypes にサービスがデプロイされている場合、レプリカが必ず別々のゾーンに存在するように配置されます。 これらの各 nodeTypes に存在するノード上の障害ドメインはゾーン情報を使用して構成されているため、この分離が保証されます (つまり、 FD = fd:/zone1/1... といった具合です)。 たとえば、サービスに 5 個のレプリカまたはインスタンスがある場合、分散は 2-2-1 となり、ランタイムは AZ 間での均等な分散を試みます。

**ユーザー サービス レプリカの構成**: クロス可用性ゾーン nodeTypes にデプロイされたステートフル ユーザー サービスは、レプリカ数のターゲット値 9、最小値 5 という構成値を使用して構成する必要があります。 この構成は、1 つのゾーンがダウンした場合でも 6 つのレプリカが他の 2 つのゾーンで稼働しているため、サービスを機能させることができます。 このようなシナリオでは、アプリケーションのアップグレードも実行できます。

**ゾーン ダウンのシナリオ**: ゾーンがダウンすると、そのゾーン内のすべてのノードがダウン状態として表示されます。 これらのノードのサービス レプリカもダウンします。 他のゾーンにレプリカがあるため、正常に動作しているゾーンにプライマリ レプリカがフェールオーバーすることで、サービスは引き続き応答します。 ターゲット レプリカ数にまだ到達しておらず、かつ VM 数が、定義されている最小ターゲット レプリカ サイズを上回っているため、サービスは警告状態として表示されます。 結果として、Service Fabric ロード バランサーは、構成されているターゲット レプリカの数と一致するように、作業ゾーンでレプリカを起動します。 この時点で、サービスは正常と表示されます。 ダウンしたゾーンが復帰すると、負荷分散によってすべてのサービス レプリカがすべてのゾーンに均等に分散されます。

## <a name="networking-configuration"></a>ネットワーク構成
詳細については、「[Service Fabric マネージド クラスターのネットワーク設定を構成する](./how-to-managed-cluster-networking.md)」を参照してください

## <a name="enabling-a-zone-resilient-azure-service-fabric-managed-cluster"></a>ゾーンの回復性に優れた Azure Service Fabric マネージド クラスターを有効にする
ゾーン回復性のある Azure Service Fabric マネージド クラスターを有効にするには、マネージド クラスターのリソース定義に次の情報を含める必要があります。

* **ZonalResiliency** プロパティ。クラスターにゾーン回復性があるかどうかを指定します。

```json
{
  "apiVersion": "2021-05-01",
  "type": "Microsoft.ServiceFabric/managedclusters",
  "zonalResiliency": "true"
}
```
[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
[sf-multi-az-arch]: ./media/service-fabric-cross-availability-zones/sf-multi-az-topology.png
[sfmc-multi-az-nodes]: ./media/how-to-managed-cluster-availability-zones/sfmc-multi-az-nodes.png
