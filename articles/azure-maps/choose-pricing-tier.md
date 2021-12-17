---
title: Microsoft Azure Maps の適切な価格レベルの選択
description: Azure Maps の価格レベルについて説明します。 どの機能がどのレベルで提供されているかを確かめ、価格レベルの選択に関する重要な考慮事項を確認します。
author: stevemunk
ms.author: v-munksteve
ms.date: 11/11/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: ef61b0c8a52e37b5eb73f38ab63cc97e66bf7e4e
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398917"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Azure Maps での適切な価格レベルの選択

Azure Maps では、Gen 1 と Gen 2 の 2 つの価格レベルが提供されるようになりました。 新しい Gen 2 の価格レベルには、すべての Azure Maps 機能が含まれており、Gen 1 より QPS (1 秒あたりのクエリ数) 制限が増加しています。これを使用すると、Azure Maps トランザクションの増加に伴うコストを削減できます。 この記事は、ニーズにあった適切な価格レベルを選択するのに役立ちます。

## <a name="pricing-tier-targeted-customers"></a>価格レベルの対象のお客様

Gen 1 と Gen 2 の価格レベルについて理解を深めるには、次の **価格レベルの対象のお客様** に関する表を参照してください。  詳細については、「[Azure Maps の価格](https://aka.ms/CreatorPricing)」をご覧ください。 現在 Azure Maps をご利用の場合は、[価格レベルの管理](how-to-manage-pricing-tier.md)に関する記事で、Gen 1 から Gen 2 価格に変更する方法を確認できます。

| Pricing tier  | SKU | 対象となるお客様|
|---------------|:---:| ------------------|
|**Gen 1**|S0| S0 価格レベルは、概念実証開発や早い段階のテストからアプリケーションの運用やデプロイまで、すべての運用ステージのアプリケーションで利用できます。 ただし、このレベルは、小規模開発、同時ユーザー数の少ないお客様、またはその両方向けに設計されています。 S0 には、すべてのサービスを組み合わせて 50 QPS という制限があります。
|         |S1| S1 価格レベルは、大規模なエンタープライズ アプリケーション、ミッションクリティカルなアプリケーション、または多数の同時実行ユーザーが含まれているお客様に適しています。 また、高度な地理空間サービスを必要とするお客様にも適しています。
| **Gen 2** | Maps/Location Insights | Gen 2 の価格は、新規および既存の Azure Maps のお客様向けです。 Gen 2 には、Azure Maps のテストおよび構築に使用できる、無料レベルのトランザクションが 1 か月分付属しています。 Maps と Location Insights SKU には、Azure Maps の機能がすべて含まれています。 これにより、Azure Maps トランザクションが増加したときにコストの削減を実現できます。 さらに、Gen 1 よりも QPS 制限が高くなります。 「[フロアガイド用 Creator](creator-indoor-maps.md)」を使用する場合は、Gen 2 価格レベルが必要となります。
|     |  |

QPS 制限の詳細については、[Azure Maps のQPS レート制限](azure-maps-qps-rate-limits.md)に関するページを参照してください。

「[フロアガイド用 Creator](creator-indoor-maps.md)」の価格に関するその他の情報については、「[Azure Maps の価格](https://aka.ms/CreatorPricing)」の *Creator* セクションをご覧ください。

## <a name="next-steps"></a>次のステップ

価格レベルを表示および変更する方法について詳しく学びます。

> [!div class="nextstepaction"]
> [価格レベルを管理する](how-to-manage-pricing-tier.md)
