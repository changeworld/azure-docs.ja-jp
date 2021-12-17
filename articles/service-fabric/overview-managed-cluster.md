---
title: Service Fabric の管理対象クラスター
description: Service Fabric マネージド クラスターは、デプロイとクラスター管理を効率化する Azure Service Fabric クラスター リソース モデルの進化版です。
ms.topic: overview
ms.date: 10/22/2021
ms.openlocfilehash: 5c94254e470e625a844b345c452e252fcd69b626
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132308126"
---
# <a name="service-fabric-managed-clusters"></a>Service Fabric の管理対象クラスター

Service Fabric マネージド クラスターは、デプロイとクラスター管理エクスペリエンスを効率化する Azure Service Fabric クラスター リソース モデルの進化版です。

従来の Service Fabric クラスターに使用される Azure Resource Model (ARM) テンプレートでは、クラスター リソースを多数の関連リソースと共に定義する必要があります。クラスターとサービスが適切に機能するためには、それらすべてが (デプロイ時からクラスターのライフサイクルの終了まで) 正しく "関連付け" られていなければなりません。 これに対して、Service Fabric マネージド クラスターのカプセル化モデルは、単一の "*Service Fabric マネージド クラスター*" リソースから成ります。 クラスターの基になるリソースはすべて抽象化され、Azure が皆さんの代わりに管理します。

**Service Fabric の従来のクラスター モデル**
![Service Fabric の従来のクラスター モデル][sf-composition]

**Service Fabric マネージド クラスター モデル**
![Service Fabric カプセル化クラスター モデル][sf-encapsulation]

サイズと複雑さの点で言えば、Service Fabric マネージド クラスターに使用される ARM テンプレートの JSON は約 100 行であるのに対し、通常の Service Fabric クラスターを定義するために必要な行数は約 1,000 行となります。

| Service Fabric のリソース | Service Fabric マネージド クラスター リソース |
|----------|-----------|
| Service Fabric クラスター | Service Fabric マネージド クラスター |
| 仮想マシン スケール セット | |
| Load Balancer | |
| パブリック IP アドレス | |
| ストレージ アカウント | |
| 仮想ネットワーク | |

## <a name="service-fabric-managed-cluster-advantages"></a>Service Fabric マネージド クラスターの利点
Service Fabric マネージド クラスターには、次のように、従来のクラスターと比べて優れている点が数多くあります。

**クラスターのデプロイと管理が簡素化されている**
- デプロイおよび管理の対象となる Azure リソースが 1 つ
- クラスター証明書の管理と 90 日間の自動ローテーション
- 簡素化されたスケーリング操作
- OS イメージの自動アップグレードのサポート
- インプレースの OS SKU 変更のサポート

**運用エラーを防止**
- 基になるリソースとの構成の不整合を防止
- 安全でない操作をブロック (シード ノードを削除するなど)

**ベスト プラクティスを既定で採用**
- 信頼性と持続性の設定を簡素化

Service Fabric マネージド クラスターには、そのクラスターに必要な、基になるリソースのコストを超える追加負担はなく、マネージド クラスターにも同じ Service Fabric SLA が適用されます。

> [!NOTE]
> 既存の Service Fabric クラスターからマネージド クラスターへの移行パスは存在しません。 この新しいリソースの種類を使用するには、新しい Service Fabric マネージド クラスターを作成する必要があります。

## <a name="service-fabric-managed-cluster-skus"></a>Service Fabric マネージド クラスターの SKU

Service Fabric マネージド クラスターには、Basic SKU と Standard SKU があります。

| 機能 | Basic | Standard |
| ------- | ----- | -------- |
| ネットワーク リソース ([ロード バランサー](../load-balancer/skus.md)、[パブリック IP](../virtual-network/ip-services/public-ip-addresses.md) の SKU) | Basic | Standard |
| 最小ノード (VM インスタンス) 数 | 3 | 5 |
| ノード タイプごとの最大ノード数 | 100 | 1000 |
| 最大ノード タイプ数 | 1 | 20 |
| ノード タイプの追加と削除 | いいえ | はい |
| ゾーン冗長性 | いいえ | はい |

## <a name="feature-support"></a>機能のサポート

マネージド クラスターの機能は今後も引き続き拡張されます。 詳細については、[マネージド クラスターの構成オプションに関するドキュメント](how-to-managed-cluster-configuration.md)、または managedClusters の [Bicep および ARM テンプレート](/azure/templates/microsoft.servicefabric/allversions)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Service Fabric マネージド クラスターでの作業を開始するには、次のクイックスタートをお試しください。

> [!div class="nextstepaction"]
> [Service Fabric の管理対象クラスターの作成](quickstart-managed-cluster-template.md)

[管理対象クラスターの構成方法](how-to-managed-cluster-configuration.md)のリファレンス

[sf-composition]: ./media/overview-managed-cluster/sfrp-composition-resource.png
[sf-encapsulation]: ./media/overview-managed-cluster/sfrp-encapsulated-resource.png