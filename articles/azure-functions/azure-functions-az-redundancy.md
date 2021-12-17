---
title: Azure Functions の Elastic Premium プランでの可用性ゾーンのサポート
description: Elastic Premium プランの高可用性関数アプリケーションで、Azure Functions の可用性ゾーン冗長を使用する方法について説明します。
ms.topic: conceptual
ms.author: johnguo
ms.date: 09/07/2021
ms.custom: references_regions
ms.openlocfilehash: 796f4db37429e73dd1c3d50dc71b0a599977d861
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699794"
---
# <a name="azure-functions-support-for-availability-zone-redundancy"></a>Azure Functions の可用性ゾーン冗長のサポート

Azure Functions における可用性ゾーン (AZ) のサポートが、Elastic Premium および Dedicated (App Service) プランで利用できるようになりました。 ゾーン冗長 Azure 関数アプリケーションでは、高可用性を実現するために、可用性ゾーン間でインスタンスのバランスが自動的に調整されます。 このドキュメントでは、Elastic Premium Function プランのゾーン冗長性のサポートに焦点を当てています。 Dedicated プランのゾーン冗長性については、[こちら](../app-service/how-to-zone-redundancy.md)を参照してください。

## <a name="overview"></a>概要

[可用性ゾーン](../availability-zones/az-overview.md#availability-zones)とは高可用性を提供するサービスで、アプリケーションとデータをデータセンターの障害から保護します。 可用性ゾーンは、Azure リージョン内の一意の物理的な場所です。 それぞれのゾーンは、独立した電源、冷却手段、ネットワークを備えた 1 つまたは複数のデータセンターで構成されています。 回復性を確保するために、有効になっているリージョンにはいずれも少なくとも 3 つの個別のゾーンが存在します。 高可用性をアプリケーションに構築するには、コンピューティング、ストレージ、ネットワーク、およびデータ リソースを 1 つのゾーン内に併置し、他のゾーンでレプリケートします。

ゾーン冗長関数アプリは、アプリが実行されているインスタンスをリージョン内の可用性ゾーン間で自動的に分散します。 ゾーン冗長 Elastic Premium アプリの場合、アプリがスケールインおよびスケールアウトしても、アプリが実行されているインスタンスは可用性ゾーン間で均等に分散されます。

## <a name="requirements"></a>必要条件

> [!IMPORTANT]
> 関数アプリの[ストレージ アカウント](storage-considerations.md#storage-account-requirements)を選択する際に、[ゾーン冗長ストレージ アカウント (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage) を使用するようにします。 そうしないと、Functions はストレージに依存しているため、ゾーン障害が発生した場合に予想外の動作が発生する可能性があります。 

- Windows と Linux はどちらもサポートされています。
- [Elastic Premium](functions-premium-plan.md) または Dedicated ホスティング プランでホストする必要があります。 Dedicated (App Service) ホスティング プランを使用したゾーン冗長性の手順については、[こちら](../app-service/how-to-zone-redundancy.md)を参照してください。
  - 現在、[Consumption](consumption-plan.md) プランの関数アプリでは、可用性ゾーン (AZ) のサポートは利用できません。
- ゾーン冗長性があるプランでは、最小のインスタンス数を 3 に指定する必要があります。
- また、Elastic Premium プランの関数アプリでは、[常時使用可能なインスタンス](functions-premium-plan.md#always-ready-instances)の数が 3 以上である必要があります。
- 次のいずれかのリージョンで有効にすることが可能:
  - 米国西部 2
  - 米国西部 3
  - 米国中部
  - 米国東部
  - 米国東部 2
  - カナダ中部
  - ブラジル南部
  - 北ヨーロッパ
  - 西ヨーロッパ
  - ドイツ中西部
  - フランス中部
  - 英国南部
  - 東日本
  - 東南アジア
  - オーストラリア東部
- 現時点では、[ARM テンプレート](../azure-resource-manager/templates/index.yml)を使用して作成する必要があります。

## <a name="how-to-deploy-a-function-app-on-a-zone-redundant-premium-plan"></a>ゾーン冗長 Premium プランに関数アプリをデプロイする方法

ゾーン冗長 Elastic Premium Functions プランの初期作成を行う場合は、[ARM テンプレート](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md)を使用してデプロイする必要があります。 正常に作成できた後は、Azure portal や CLI ツールを介して Function プランを表示したり、操作したりすることができます。 ARM テンプレートは、Function プランの初期作成を行う場合にのみ必要になります。 Premium プランで Function をホストするためのガイドについては、[こちら](functions-infrastructure-as-code.md#deploy-on-premium-plan)をご覧ください。 ゾーン冗長プランが作成およびデプロイされると、新しいプランでホストされている関数アプリはゾーン冗長になります。 

ゾーン冗長 Function プランの作成時に注意する必要があるプロパティは、新しい **zoneRedundant** プロパティと Function プランのインスタンス数 (**capacity**) フィールドのみです。 **zoneRedundant** プロパティは **true** に設定し、**capacity** プロパティはワークロード要件に基づいて設定する必要がありますが、3 未満にしないようにします。 選択すべき適切な容量は、いくつかの要因と高可用性やフォールト トレランス戦略によって異なります。 大まかに言えば、インスタンスのゾーンが 1 つ失われても予想される負荷を処理するのに十分な容量が残っているように、アプリケーションに十分なインスタンスを確保することが大切です。

> [!IMPORTANT]
> Elastic Premium のゾーン冗長 Function プランでホストされている Azure 関数アプリでは、[常時使用可能なインスタンス](functions-premium-plan.md#always-ready-instances)の数が 3 以上である必要があります。 これは、ゾーン冗長関数アプリに、常にゾーンごとに少なくとも 1 つのワーカーを満足させるのに十分なインスタンスを持つことを強制するためです。

以下は、新しい **zoneRedundant** フィールドと **capacity** の仕様を示している、ゾーン冗長の Premium Function プランの ARM テンプレートのスニペットです。

```
    "resources": [
        {
            "type": "Microsoft.Web/serverfarms",
            "apiVersion": "2021-01-15",
            "name": "your_plan_name_here",
            "location": "Central US",
            "sku": {
                "name": "EP3",
                "tier": "ElasticPremium",
                "size": "EP3",
                "family": "EP", 
                "capacity": 3
            },
            "kind": "elastic",
            "properties": {
                "perSiteScaling": false,
                "elasticScaleEnabled": true,
                "maximumElasticWorkerCount": 20,
                "isSpot": false,
                "reserved": false,
                "isXenon": false,
                "hyperV": false,
                "targetWorkerCount": 0,
                "targetWorkerSizeId": 0, 
                "zoneRedundant": true
            }
        }
    ]
```

詳細については、「[Azure Functions の関数アプリのリソース デプロイを自動化](functions-infrastructure-as-code.md)」を参照してください。
