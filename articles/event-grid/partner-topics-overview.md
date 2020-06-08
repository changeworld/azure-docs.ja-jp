---
title: Azure Event Grid パートナー トピック
description: Azure Event Grid を使用して、サードパーティの Event Grid SaaS および PaaS パートナーから直接 Azure サービスにイベントを送信します。
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: babanisa
ms.openlocfilehash: 4546874b8a6cd8a7e45c3e6957a5181d66c7433f
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2020
ms.locfileid: "83690038"
---
# <a name="partner-topics-in-azure-event-grid-preview"></a>Azure Event Grid でのパートナー トピック (プレビュー)
パートナー トピックを使用すると、サードパーティのイベント ソースを Event Grid に直接接続できます。 この統合により、Azure サービスからのイベントをサブスクライブするのと同じ方法で、パートナーからのイベントをサブスクライブすることができます。 

## <a name="available-partners"></a>使用可能なパートナー
Event Grid パートナー トピックを介して使用できる最初のパートナーは、Auth0 です。 [Auth0 パートナー トピック](auth0-overview.md)を使用すると、Auth0 アカウントと Azure アカウントを接続することができます。 リアルタイムで Auth0 イベントに反応し、ログを記録し、監視するための統合です。

Auth0 アカウントにログインして Event Grid 統合を作成して、今すぐ[試してみてください](auth0-how-to.md)。 Auth0 で [作成] をクリックすると、Azure アカウントに保留中の Auth0 トピックが表示されます。 [アクティブ化] をクリックすると、他のイベント ソースの場合と同様に、イベント サブスクリプションの作成、ルーティング、フィルター処理、およびイベントの配信を行うことができます。

## <a name="pricing"></a>価格
パートナー トピックは、システム トピックと同じ稼働率で課金されます。

## <a name="limits"></a>制限
パートナー トピックは、パブリック プレビュー段階にあります。 パートナー トピックはパブリック プレビューの段階では、システム トピックおよびカスタム トピックと[同じ制限](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#event-grid-limits)が適用されます。

## <a name="how-do-i-become-an-event-grid-partner"></a>Event Grid パートナーになるには
開始のサポートのために作成されたインフラストラクチャを使用すると、新しいパートナーはそのイベント機能を Event Grid と短時間で簡単に統合できます。 詳細については、[パートナーのオンボードに関するドキュメント](partner-onboarding-overview.md)を参照してください。

## <a name="next-steps"></a>次のステップ

- [Auth0 パートナー トピック](auth0-overview.md)
- [Auth0 パートナー トピックの使用方法](auth0-how-to.md)
- [Event Grid パートナーになる](partner-onboarding-overview.md)