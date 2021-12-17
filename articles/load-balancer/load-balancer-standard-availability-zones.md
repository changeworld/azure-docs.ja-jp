---
title: Azure Load Balancer と可用性ゾーン
titleSuffix: Azure Load Balancer
description: このラーニング パスを使用して、Azure Standard Load Balancer と Availability Zones の使用を開始します。
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2020
ms.author: allensu
ms.openlocfilehash: d53ae804fa628c5fcf89fa61d5e9d153abd225cb
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130225758"
---
# <a name="load-balancer-and-availability-zones"></a>ロード バランサーと可用性ゾーン

Azure Load Balancer は、可用性ゾーンのシナリオをサポートしています。 Standard Load Balancer を使用することで、リソースをゾーンに一致させて、複数のゾーンに分散させることにより、シナリオ全体の可用性を向上させることができます。  このドキュメントを読んで、これらの概念と基本的なシナリオの設計ガイダンスを理解してください

ロード バランサーには、**ゾーン冗長、ゾーン、** または **非ゾーン** を指定できます。 ロード バランサーのゾーン関連プロパティ (前述) を構成するには、必要なフロントエンドの適切な種類を選択します。

## <a name="zone-redundant"></a>ゾーン冗長

可用性ゾーンのあるリージョンでは、Standard Load Balancer をゾーン冗長にすることができます。 このトラフィックは、1 つの IP アドレスによって行われます。

単一のフロントエンド IP アドレスは、ゾーンの障害が発生した後も存続します。 フロントエンド IP は、ゾーンに関係なく、すべての (影響を受けない) バックエンド プール メンバーに到達するために使用できます。 1 つまたは複数の可用性ゾーンで障害が発生しても対応可能であり、リージョン内に正常なゾーンが 1 つでも残っていれば、データ パスは存続します。

フロントエンドの IP アドレスは、複数の可用性ゾーンで複数の独立したインフラストラクチャ展開によって同時に処理されます。 再試行または再確立は、ゾーンの障害による影響を受けない他のゾーンで成功します。

<p align="center">
  <img src="./media/az-zonal/zone-redundant-lb-1.svg" alt="Figure depicts a zone-redundant standard load balancer directing traffic in three different zones to three different subnets in a zone redundant configuration." width="512" title="Virtual Network NAT">
</p>

*図:ゾーン冗長ロード バランサー*

## <a name="zonal"></a>ゾーン ベース

単一ゾーンを保証されたフロントエンドにすることができます。これは、"*ゾーン ベース*" と呼ばれます。  このシナリオは、すべての受信または送信フローが、リージョン内の 1 つのゾーンによって処理されることを意味します。  フロントエンドは、ゾーンの正常性に左右されます。  データ パスは、それが保証されたゾーン以外のゾーンのエラーによっては影響を受けません。 ゾーン ベースのフロントエンドを使って、可用性ゾーンごとに IP アドレスを公開することができます。  

さらに、各ゾーン内の負荷分散されたエンドポイントに対して、ゾーン フロントエンドを直接使用することもできます。 この構成を使ってゾーンごとに負荷分散されたエンドポイントを公開し、各ゾーンを個別に監視することができます。 パブリック エンドポイントの場合は、これらを [Traffic Manager](../traffic-manager/traffic-manager-overview.md) などの DNS 負荷分散製品と統合して、1 つの DNS 名を使用できます。


<p align="center">
  <img src="./media/az-zonal/zonal-lb-1.svg" alt="Figure depicts three zonal standard load balancers each directing traffic in a zone to three different subnets in a zonal configuration." width="512" title="Virtual Network NAT">
</p>

*図:ゾーン ロード バランサー*

パブリック ロード バランサー フロントエンドの場合は、パブリック IP に **zones** パラメーターを追加します。 このパブリック IP は、それぞれの規則で使用されるフロントエンド IP 構成によって参照されます。

内部ロード バランサー フロントエンドの場合は、内部ロード バランサーのフロントエンド IP 構成に **zones** パラメーターを追加します。 ゾーン フロントエンドは、サブネット内の IP アドレスを特定のゾーンに対して保証します。

## <a name="non-zonal"></a>非ゾーン

ロード バランサーは、"ゾーンなし" フロントエンド (パブリック IP またはパブリック IP プレフィックス) を使用して、非ゾーン構成で作成することもできます。  このオプションによって、冗長性の保証は提供されません。 [アップグレード](../virtual-network/ip-services/public-ip-upgrade-portal.md)されたパブリック IP アドレスはすべて、"ゾーンなし" の種類になることに注意してください。

## <a name="design-considerations"></a><a name="design"></a> 設計上の考慮事項

Standard Load Balancer のゾーン関連プロパティについて理解したうえで、次の設計上の考慮事項について理解すると、高可用性を実現するための設計に役立ちます。

### <a name="tolerance-to-zone-failure"></a>ゾーン障害の許容範囲

- **ゾーン冗長** のロード バランサーは、1 つの IP アドレスを持つ任意のゾーン内のゾーン リソースに対してサービスを提供できます。  IP は、リージョン内に正常なゾーンが少なくとも 1 つ残っている限り、1 つまたは複数のゾーンで障害があっても存続できます。
- **ゾーン** フロントエンドは、単一のゾーンへのサービスの縮小であり、対応するゾーンの正常性に依存します。 デプロイ先のゾーンがダウンした場合、デプロイでその障害を回避することはできません。

運用ワークロードにはゾーン冗長ロード バランサーを使用することをお勧めします。

### <a name="multiple-frontends"></a>複数のフロントエンド

複数のフロントエンドを使用すると、複数のポートまたは IP アドレスでトラフィックを負荷分散できます。  アーキテクチャを設計する場合は、ゾーン冗長性と複数のフロントエンドが対話できる方法を考慮することが重要です。  常にすべてのフロントエンドが障害に対する回復性を備えることが目標である場合は、フロントエンドとして割り当てられた使用されるすべての IP アドレスがゾーン冗長である必要があることに注意してください。   フロントエンドのセットが 1 つのゾーンに関連付けられることが目的である場合は、そのセットのすべての IP アドレスをその特定のゾーンに関連付ける必要があります。  ゾーンごとにロード バランサーを用意する必要はありません。代わりに、各ゾーン フロントエンド (またはゾーン フロントエンドのセット) を、その特定の可用性ゾーンの一部であるバックエンド プール内の仮想マシンに関連付けることができます。

### <a name="transition-between-regional-zonal-models"></a>リージョンのゾーン モデル間での移行

リージョンが[可用性ゾーン](../availability-zones/az-overview.md)を含むように拡張された場合、すべて非ゾーンのままになります。 アーキテクチャで新しいゾーンを確実に利用できるようにするために、新しいフロントエンド IP を作成し、これらの新しいパブリック IP を利用するように適切なルールと構成をレプリケートすることをお勧めします。

### <a name="control-vs-data-plane-implications"></a>コントロール プレーンとデータプレーンについての注意点

ゾーンの冗長性は、データ プレーンやコントロール プレーンがヒットレスであることを意味するものではありません。 ゾーン冗長なフローは任意のゾーンを使うことができ、ユーザーのフローはリージョン内で正常なすべてのゾーンを使います。 ゾーンで障害が発生した場合、正常なゾーンを使用しているトラフィック フローは影響を受けません。

ゾーンで障害が発生したときにゾーンを使用しているトラフィック フローは影響を受ける可能性がありますが、アプリケーションは復旧できます。 Azure がゾーンの障害に対して収束した場合、トラフィックは、再送信時にリージョン内の正常なゾーンで継続されます。

障害シナリオに対するアプリケーションの回復性を向上させるには、[Azure クラウド設計パターン](/azure/architecture/patterns/)に関する記事を参照してください。

## <a name="limitations"></a>制限事項

* 作成済みのリソースに対して、ゾーンを変更、更新、作成することはできません。
* 作成済みのリソースをゾーン ベースからゾーン冗長に、またはその逆に更新することはできません。

## <a name="next-steps"></a>次のステップ
- [可用性ゾーン](../availability-zones/az-overview.md)の詳細を確認する
- [Standard Load Balancer](./load-balancer-overview.md) の詳細を確認する
- [ゾーン ベースの Standard Load Balancer を使用して、単一のゾーン内での VM の負荷分散](./quickstart-load-balancer-standard-public-cli.md)を確認する
- [ゾーン フロントエンドの Standard Load Balancer を使用したゾーン間での VM の負荷分散](./quickstart-load-balancer-standard-public-cli.md)を確認する
- 障害シナリオに対するアプリケーションの回復性を向上させるために、[Azure クラウド設計パターン](/azure/architecture/patterns/)についての記事を確認する。