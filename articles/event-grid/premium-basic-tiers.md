---
title: Azure Event Grid の Premium レベルと Basic レベル
description: この記事では、Azure Event Grid の Premium レベルと Basic レベルの違いと、それぞれを使用するタイミングについて説明します。
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: overview
ms.date: 03/11/2020
ms.author: babanisa
ms.openlocfilehash: 7ae73337ccccc2b7d2402cfa2a54ae93552d2465
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2020
ms.locfileid: "79300336"
---
# <a name="azure-event-grid-premium-and-basic-tiers"></a>Azure Event Grid の Premium レベルと Basic レベル
Azure Event Grid には、次の 2 つのレベルがあります。**Premium** レベルと **Basic** レベルです。 Basic レベルでは、従量課金制の料金が使用されます。 これには、イベント ドリブン プログラミング モデルに Event Grid を使用するために必要な、すべての基本的なパブリッシュ/サブスクライブ ツールが用意されています。 Premium レベルは、エンタープライズを対象とするセキュリティ機能により、これをさらに一歩進めたものです。 Premium レベルは、その機能の多くが開発中で、**プレビュー**の初期段階にあります。

## <a name="overview"></a>概要
Event Grid 内のすべてのカスタム トピックとドメインは、Basic レベルまたは Premium レベルのいずれかに属しています。 `2020-04-01-preview` API バージョン以降では、トピックまたはドメインの作成の一環として目的のレベルを選択できます。 既定値は Basic レベルです。 以前のバージョンの API を使用して作成されたトピックとドメインは、既定の Basic レベルになります。 トピックとドメインの価格レベルを変更するには、[トピックとドメインのレベルを更新する方法](update-tier.md)に関する記事を参照してください。

## <a name="capabilities-and-features"></a>機能と特徴

次の表では、レベルの違いについて説明しています。

|       &nbsp;                                           | Basic           | Premium        |
| ------------------------------------------------------ | --------------- | -------------- |
| 受信用の IP ファイアウォール規則                          | プレビュー  | プレビュー |
| 送信用のサービス タグ                                | プレビュー  | プレビュー |
| 受信時のプライベート エンドポイント VNet 統合          | 使用不可   | プレビュー |

## <a name="availability"></a>可用性
初期プレビュー時に、プライベート エンドポイント統合を備えた Premium レベルのトピックとドメインを使用できるリージョンは、次のとおりです。

- 米国東部
- 米国西部 2
- 米国中南部

## <a name="pricing-and-quotas"></a>価格とクォータ
Basic レベルの使用料金の詳細については、「[Event Grid の価格](https://azure.microsoft.com/pricing/details/event-grid/)」を参照してください。 Premium レベルの価格はまだ発表されておらず、価格が発表されるまでは無料です。

トピックとドメインの数の既存のクォータとスループットは、Premium レベルの価格が発表されるまで、Premium レベルと Basic レベルの両方のリソースに適用されます。

## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。

- Basic レベルから Premium レベルにアップグレードするには、「[価格レベルの更新](update-tier.md)」の記事を参照してください。 
- Event Grid リソースに IP ファイアウォールを構成して、パブリック インターネット経由のアクセスを、IP アドレスまたは IP アドレス範囲の選択されたセットからのみに制限することができます。 詳細な手順については、[ファイアウォールの構成](configure-firewall.md)に関する記事を参照してください。
- 選択した仮想ネットワークからのアクセスのみに制限するようにプライベート エンドポイントを構成できます。 詳細な手順については、[プライベート エンドポイントの構成](configure-private-endpoints.md)に関する記事を参照してください。