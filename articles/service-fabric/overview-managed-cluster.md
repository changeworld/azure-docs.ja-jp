---
title: Service Fabric マネージド クラスター (プレビュー)
description: Service Fabric マネージド クラスターは、デプロイとクラスター管理を効率化する Azure Service Fabric クラスター リソース モデルの進化版です。
ms.topic: overview
ms.date: 09/28/2020
ms.openlocfilehash: 3d26a92126491662d5c51b3c4e8900ffa547f830
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2020
ms.locfileid: "91410399"
---
# <a name="service-fabric-managed-clusters-preview"></a>Service Fabric マネージド クラスター (プレビュー)

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

Service Fabric マネージド クラスターには、従来のクラスターと比べて優れている点が数多くあります。

**クラスターのデプロイと管理が簡素化されている**
- デプロイおよび管理の対象となる Azure リソースが 1 つ
- 証明書の管理と自動ローテーション
- 簡素化されたスケーリング操作

**運用エラーを防止**
- 基になるリソースとの構成の不整合を防止
- 安全でない操作をブロック (シード ノードを削除するなど)

**ベスト プラクティスを既定で採用**
- 信頼性と持続性の設定を簡素化

Service Fabric マネージド クラスターには、そのクラスターに必要な、基になるリソースのコストを超える追加負担はありません。

## <a name="service-fabric-managed-cluster-skus"></a>Service Fabric マネージド クラスターの SKU

Service Fabric マネージド クラスターには、Basic SKU と Standard SKU があります。

| 機能 | Basic | Standard |
| ------- | ----- | -------- |
| ネットワーク リソース ([ロード バランサー](../load-balancer/skus.md)、[パブリック IP](../virtual-network/public-ip-addresses.md) の SKU) | Basic | Standard |
| 最小ノード (VM インスタンス) 数 | 3 | 5 |
| ノード タイプごとの最大ノード数 | 100 | 100 |
| 最大ノード タイプ数 | 1 | 20 |
| ノード タイプの追加と削除 | いいえ | はい |
| ゾーン冗長性 | いいえ | はい |

## <a name="service-fabric-managed-cluster-feature-roadmap"></a>Service Fabric マネージド クラスターの機能ロードマップ
Service Fabric マネージド クラスターの早期プレビュー バージョンであるため、まだ実装されていない機能がいくつかあります。 以下の機能は今後のリリースで使用できるようになる予定です。 

* Visual Studio から直接クラスターにアプリケーションを発行
* マネージド ID 
* ARM アプリケーション デプロイ 
* 可用性ゾーン 
* リバース プロキシ 
* 自動スケーリング 
* NSG ルールの更新 
* OS の自動アップグレード

## <a name="next-steps"></a>次の手順

Service Fabric マネージド クラスターを体験するには、次のクイックスタートをお試しください。

> [!div class="nextstepaction"]
> [Service Fabric マネージド クラスター (プレビュー) を作成する](quickstart-managed-cluster-template.md)


[sf-composition]: ./media/overview-managed-cluster/sfrp-composition-resource.png
[sf-encapsulation]: ./media/overview-managed-cluster/sfrp-encapsulated-resource.png