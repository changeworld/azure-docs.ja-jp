---
title: Microsoft Azure Maps の適切な価格レベルの選択
description: Azure Maps の価格レベルについて説明します。 どの機能がどのレベルで提供されているかを確かめ、価格レベルの選択に関する重要な考慮事項を確認します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 98e2e54ee91a398f02da798ef44eeb42c798fbc7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747760"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Azure Maps での適切な価格レベルの選択

Azure Maps では、Gen 1 と Gen 2 の 2 つの価格レベルが提供されるようになりました。 Gen 2 の新しい価格レベルには、すべての Azure Maps 機能が QPS (1 秒あたりのクエリ数) の制限なしで含まれており、Azure Maps トランザクションの増加伴うコストを削減できます。 この記事は、ニーズにあった適切な価格レベルを選択するのに役立ちます。

## <a name="pricing-tier-targeted-customers"></a>価格レベルの対象のお客様

Gen 1 と Gen 2 の価格レベルについて理解を深めるには、次の **価格レベルの対象のお客様** に関する表を参照してください。  詳細については、「[Azure Maps の価格](https://azure.microsoft.com/pricing/details/azure-maps/)」をご覧ください。 現在 Azure Maps をご利用の場合は、[こちら](how-to-manage-pricing-tier.md)で Gen 1 から Gen 2 の価格に変更する方法をご確認ください。

| Pricing tier  | SKU | 対象となるお客様|
|-----------------|----| -----------------|
| **Gen 1** | S0          |    S0 価格レベルは、概念実証開発や早い段階のテストからアプリケーションの運用やデプロイまで、すべての運用ステージのアプリケーションで利用できます。 ただし、このレベルは、小規模開発、同時ユーザー数の少ないお客様、またはその両方向けに設計されています。
|        |S1           |    S1 価格レベルは、大規模なエンタープライズ アプリケーション、ミッションクリティカルなアプリケーション、または多数の同時実行ユーザーが含まれているお客様に適しています。 また、高度な地理空間サービスを必要とするお客様にも適しています。
| **Gen 2** | Maps/Location Insights | Gen 2 の価格は、新規および既存の Azure Maps のお客様向けです。 Gen 2 には、Azure Maps のテストおよび構築に使用できる、無料レベルのトランザクションが 1 か月分付属しています。 Maps と Location Insights SKU には、Azure Maps の機能がすべて含まれています。 また、QPS (1 秒あたりのクエリ数) の制限もありません。これはほとんどのサービスに該当するため、Azure Maps トランザクションの増加と共にコストを削減できます。  
|     |  |

## <a name="next-steps"></a>次のステップ

価格レベルを表示および変更する方法について詳しく学びます。

> [!div class="nextstepaction"]
> [価格レベルを管理する](how-to-manage-pricing-tier.md)
