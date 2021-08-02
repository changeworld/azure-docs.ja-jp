---
title: Azure CDN サブスクリプション プランと帯域幅調整
description: 特定のサブスクリプションの種類で使用できる Azure CDN について説明します。
services: cdn
author: duongau
manager: kumud
ms.service: azure-cdn
ms.topic: troubleshooting
ms.date: 06/10/2021
ms.author: duau
ms.openlocfilehash: 9c8cf3576075b2854b14d83d126e9f790d47b74d
ms.sourcegitcommit: 190658142b592db528c631a672fdde4692872fd8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2021
ms.locfileid: "112008164"
---
# <a name="azure-cdn-subscription-offers-and-bandwidth-throttling"></a>Azure CDN サブスクリプション プランと帯域幅調整

一部の Azure CDN プランは、特定の種類のサブスクリプションでのみ利用できます。 帯域幅調整も、サブスクリプションの種類によって適用される場合があります。

## <a name="free-and-trial-subscription"></a>無料および試用版のサブスクリプション

Microsoft の Azure CDN は、試用版サブスクリプションで利用できる唯一の CDN オファリングです。 この種類のサブスクリプションには、帯域幅調整が適用されます。  
 
## <a name="pay-as-you-go"></a>従量課金制

従量課金制サブスクリプションでは、Akamai からの Azure CDN は使用できません。   
 
Microsoft と Verizon からの Azure CDN の帯域幅は、サブスクリプションが良好な状態であり、十分な支払い履歴があると判断されるまで調整されます。 最初の支払いが受領されると、サブスクリプションの状態を判断し、調整を削除するプロセスが自動的に行われます。   
 
支払いをしても調整が削除されない場合には、[サポートに連絡](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)して調整の削除を要求できます。 
  
## <a name="enterprise-agreements"></a>Enterprise Agreement

すべての Azure CDN は、Enterprise Agreement サブスクリプションで使用できます。 Enterprise Agreement サブスクリプションには、帯域幅の制限はありません。  
 
## <a name="other-offer-types"></a>その他のプランの種類
 
従量課金制と同じ機能が、次の種類の契約に適用されます。

* Visual Studio 
* MSDN
* 学生
* CSP

## <a name="next-steps"></a>次のステップ

* [Azure CDN プロファイルを作成](cdn-create-new-endpoint.md)する方法について学習します。
