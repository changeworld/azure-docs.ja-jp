---
title: "Azure API Management ポリシーのサンプル - バックエンド サービスへの機能の追加 | Microsoft Docs"
description: "Azure API Management ポリシーのサンプル - バックエンド サービスに機能を追加する方法を示します。 たとえば、天気予報 API で、緯度と経度ではなく場所の名前を受け入れます。"
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: d500df0f0e48134ac9c1397795d65706d2e56ff9
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2017
---
# <a name="add-capabilities-to-a-backend-service"></a>バックエンド サービスへの機能の追加

この記事では、バックエンド サービスに機能を追加する方法を示す Azure API Management ポリシーのサンプルを示します。 たとえば、天気予報 API で、緯度と経度ではなく場所の名前を受け入れます。 ポリシー コードを設定または編集するには、「[ポリシーの設定または編集](../set-edit-policies.md)」で説明されている手順に従います。 他の例を参照するには、[ポリシーのサンプル](../policy-samples.md)に関するページをご覧ください。

## <a name="policy"></a>[ポリシー]

コードを **inbound** ブロックに貼り付けます。

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>次のステップ

APIM ポリシーの詳細:

+ [変換ポリシー](../api-management-transformation-policies.md)
+ [ポリシーのサンプル](../policy-samples.md)

