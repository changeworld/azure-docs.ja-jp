---
title: Azure Event Grid パートナー トピック
description: Azure Event Grid を使用して、サードパーティの Event Grid SaaS および PaaS パートナーから直接 Azure サービスにイベントを送信します。
services: event-grid
author: femila
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: femila
ms.openlocfilehash: 5327efea8af734c723ba76d1a00b72c08f5c88b9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84560269"
---
# <a name="partner-topics-in-azure-event-grid-preview"></a>Azure Event Grid でのパートナー トピック (プレビュー)
パートナー トピックを使用することで、サードパーティのイベント ソースを Azure Event Grid に直接接続できます。 この統合により、Azure サービスからのイベントをサブスクライブするのと同じ方法で、パートナーからのイベントをサブスクライブすることができます。 

## <a name="available-partners"></a>使用可能なパートナー
Event Grid パートナー トピックを介して使用できる最初のパートナーは、Auth0 です。 [Auth0 パートナー トピック](auth0-overview.md)を使用して、Auth0 アカウントと Azure アカウントを接続できます。 この統合によって、リアルタイムで Auth0 イベントに反応し、ログを記録し、監視することができます。

[試してみる](auth0-how-to.md)には、Auth0 アカウントにサインインし、Event Grid 統合を作成します。 Auth0 で **[作成]** をクリックすると、Azure アカウントに保留中の Auth0 トピックが表示されます。 **[アクティブ化]** を選択すると、他のイベント ソースの場合と同様に、ルーティング、フィルター処理、およびイベントの配信を行うための Event Grid イベント サブスクリプションを作成できます。

## <a name="pricing"></a>価格
パートナー トピックは、システム トピックと同じ稼働率で課金されます。

## <a name="limits"></a>制限
パートナー トピックは、パブリック プレビュー段階にあります。 パブリック プレビューの段階では、システム トピックおよびカスタム トピックと[同じ制限](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#event-grid-limits)がパートナー トピックにも適用されます。

## <a name="how-do-i-become-an-event-grid-partner"></a>Event Grid パートナーになるには
開始のサポートのために作成されたインフラストラクチャを使用すると、新しいパートナーはそのイベント機能を Event Grid と短時間で簡単に統合できます。 詳細については、[パートナーのオンボードに関するドキュメント](partner-onboarding-overview.md)を参照してください。

## <a name="next-steps"></a>次のステップ

- [Auth0 パートナー トピック](auth0-overview.md)
- [Auth0 パートナー トピックの使用方法](auth0-how-to.md)
- [Event Grid パートナーになる](partner-onboarding-overview.md)