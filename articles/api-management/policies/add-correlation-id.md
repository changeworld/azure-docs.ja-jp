---
title: API Management ポリシーのサンプル - 関連付け ID を含むヘッダーを追加する
titleSuffix: Azure API Management
description: Azure API Management ポリシーのサンプル - 関連付け ID を含むヘッダーを受信要求に追加する方法を示します。
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: danlep
ms.openlocfilehash: 421c4e0e2011f14ac076e4fffd334052e7f16800
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128670521"
---
# <a name="add-a-header-containing-a-correlation-id"></a>関連付け ID を含むヘッダーの追加

この記事では、関連付け ID を含むヘッダーを受信要求に追加する方法を示す Azure API Management ポリシーのサンプルを示します。 ポリシー コードを設定または編集するには、[ポリシーの設定または編集](../set-edit-policies.md)に関するページで説明されている手順に従います。 他の例については、[ポリシーのサンプル](../policy-reference.md)に関するページをご覧ください。

## <a name="policy"></a>ポリシー

コードを **inbound** ブロックに貼り付けます。

[!code-xml[Main](../../../api-management-policy-samples/examples/Add correlation id to inbound request.policy.xml)]

## <a name="next-steps"></a>次のステップ

APIM ポリシーの詳細については、以下をご覧ください。

+ [変換ポリシー](../api-management-transformation-policies.md)
+ [ポリシーのサンプル](../policy-reference.md)