---
title: Azure API Management の可用性ゾーンのサポート
description: ゾーン冗長を有効にすることで、リージョン内の Azure API Management サービス インスタンスの回復性を向上させる方法について説明します。
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 04/28/2021
ms.author: apimpm
ms.custom: references_regions
ms.openlocfilehash: 0ec2c0a270c721d1d706b696f5b8ba5b2ccd7ece
ms.sourcegitcommit: 43be2ce9bf6d1186795609c99b6b8f6bb4676f47
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2021
ms.locfileid: "108278868"
---
# <a name="availability-zone-support-for-azure-api-management"></a>Azure API Management の可用性ゾーンのサポート 

この記事では、Azure portal を使用して、API Management インスタンスのゾーン冗長を有効にする方法について説明します。 [ゾーン冗長](../availability-zones/az-overview.md#availability-zones)を使用すると、特定の Azure リージョン (場所) 内のサービス インスタンスに対して回復性と高可用性が提供されます。 ゾーン冗長を使用すると、API Management インスタンス (Management API、開発者ポータル、Git 構成) のゲートウェイとコントロール プレーンが、物理的に分離されたゾーン内のデータセンター間でレプリケートされ、ゾーン障害に対する回復性が向上します。 

API Management では、[複数リージョン デプロイ](api-management-howto-deploy-multi-region.md)もサポートされています。これにより、地理的に分散した API コンシューマーが認識する要求の待機時間が短縮され、1 つのリージョンがオフラインになった場合のゲートウェイ コンポーネントの可用性が向上します。 リージョン内の冗長性のための可用性ゾーンと、リージョン障害が発生した場合にゲートウェイの可用性を向上させる複数リージョン デプロイの組み合わせにより、API Management インスタンスの信頼性とパフォーマンスの両方を向上させることができます。

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="supported-regions"></a>サポートされているリージョン

ゾーン冗長のための API Management の構成は、現在、次の Azure リージョンでサポートされています。

* オーストラリア東部
* ブラジル南部
* カナダ中部
* インド中部
* 米国中部
* 米国東部
* 米国東部 2
* フランス中部
* 東日本
* 北ヨーロッパ
* 米国中南部
* 東南アジア
* 英国南部
* 米国西部 2
* 米国西部 3

## <a name="prerequisites"></a>前提条件

* API Management サービス インスタンスをまだ作成していない場合は、[API Management サービス インスタンスの作成](get-started-create-service-instance.md)に関するページを参照してください。 Premium サービス レベルを選択します。
* API Management インスタンスが[仮想ネットワーク](api-management-using-with-vnet.md)にデプロイされている場合、ゾーン冗長を有効にする予定の新しい場所に仮想ネットワーク、サブネット、パブリック IP アドレスを設定したことを確認します。

## <a name="enable-zone-redundancy---portal"></a>ゾーン冗長を有効にする - ポータル

ポータルで、API Management サービスに場所を追加するとき、または既存の場所の構成を更新するときに、必要に応じてゾーン冗長を有効にします。

1. Azure portal で、API Management サービスに移動し、メニューの **[場所]** を選択します。
1. 既存の場所を選択するか、上部バーで **[+ Add]\(+ 追加\)** を選択します。 この場所は、[可用性ゾーンをサポート](#supported-regions)している必要があります。
1. その場所のスケール **[ユニット](upgrade-and-scale.md)** の数を選択します。
1. **[可用性ゾーン]** で、1 つまたは複数のゾーンを選択します。 選択した数のユニットを、可用性ゾーン全体に均等に分散させる必要があります。 たとえば、3 つのユニットを選択した場合、3 つのゾーンを選択すると、各ゾーンが 1 つのユニットをホストします。
1. API Management インスタンスが[仮想ネットワーク](api-management-using-with-vnet.md)にデプロイされている場合、その場所の仮想ネットワーク設定を構成します。 その場所で使用できる既存の仮想ネットワーク、サブネット、パブリック IP アドレスを選択します。
1. **[適用]** を選択し、次に **[保存]** を選択します。

:::image type="content" source="media/zone-redundancy/add-location-zones.png" alt-text="ゾーン冗長性を有効にする":::

> [!IMPORTANT]
> 可用性ゾーンを有効化、追加、または削除すると、その場所のパブリック IP アドレスが変更されます。 ネットワーク設定を使用してリージョンの可用性ゾーンを更新する場合、以前に設定したものとは異なるパブリック IP アドレス リソースを構成する必要があります。

> [!NOTE]
> API Management インスタンスに変更を適用するのに 15 分から 45 分かかります。

## <a name="next-steps"></a>次のステップ

* [複数の Azure リージョンへの Azure API Management サービス インスタンスのデプロイ](api-management-howto-deploy-multi-region.md)について、さらに確認する。
* [Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-api-management-simple-zones)を使用してゾーン冗長を有効にすることもできます。
* [可用性ゾーンをサポートする Azure サービス](../availability-zones/az-region.md)について、さらに確認する。
* Azure での[信頼性](/azure/architecture/framework/resiliency/overview)の構築について、さらに確認する。