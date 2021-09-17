---
title: Azure Front Door Standard/Premium サブスクリプション プランと帯域幅調整
description: 特定のサブスクリプションの種類での Azure Front Door Standard/Premium の可用性について説明します。
services: front-door
author: duongau
manager: kumud
ms.service: frontdoor
ms.topic: troubleshooting
ms.date: 08/20/2021
ms.author: duau
ms.openlocfilehash: d98d69e616910d421b2b8bed5ea305039a0f13b2
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2021
ms.locfileid: "122635215"
---
# <a name="azure-front-door-standardpremium-subscription-offers-and-bandwidth-throttling"></a>Azure Front Door Standard/Premium サブスクリプション プランと帯域幅調整

Azure Front Door Standard/Premium プロファイルには、サブスクリプションの種類に基づいて、帯域幅調整が適用されます。

## <a name="free-and-trial-subscription"></a>無料および試用版のサブスクリプション

この種類のサブスクリプションには、帯域幅調整が適用されます。

## <a name="pay-as-you-go"></a>従量課金制

帯域幅は、サブスクリプションが良好な状態であり、十分な支払い履歴があると判断されるまで調整されます。 最初の支払いが受領されると、サブスクリプションの状態を判断し、調整を削除するプロセスが自動的に行われます。

支払いをしても調整が削除されない場合には、[サポートに連絡](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)して調整の削除を要求できます。

## <a name="enterprise-agreements"></a>Enterprise Agreement

Enterprise Agreement サブスクリプションには、帯域幅の制限はありません。

## <a name="other-offer-types"></a>その他のプランの種類

従量課金制と同じ機能が、次の種類の契約に適用されます。

* Visual Studio
* MSDN
* 学生
* CSP

## <a name="next-steps"></a>次のステップ

[Azure Front Door の Standard および Premium プロファイルを作成する](create-front-door-portal.md)方法を学習します。
