---
title: "Azure API Management ポリシーのサンプル - X-CSRF パターンの実装 | Microsoft Docs"
description: "Azure API Management ポリシーのサンプル - 多くの API で使用される X CSRF パターンを実装する方法を示します。 この例は SAP ゲートウェイに固有です。"
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
ms.openlocfilehash: a99ff25f7f8a3851936ba8d28e76eff98b1b0458
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2017
---
# <a name="implement-x-csrf-pattern"></a>X CSRF パターンの実装

この記事では、多くの API で使用される X CSRF パターンを実装する方法を示す Azure API Management ポリシーのサンプルを示します。 この例は SAP ゲートウェイに固有です。 ポリシー コードを設定または編集するには、「[ポリシーの設定または編集](../set-edit-policies.md)」で説明されている手順に従います。 他の例を参照するには、[ポリシーのサンプル](../policy-samples.md)に関するページをご覧ください。

## <a name="policy"></a>[ポリシー]

コードを **inbound** ブロックに貼り付けます。

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Get X-CSRF token from SAP gateway using send request policy.xml)]

## <a name="next-steps"></a>次のステップ

APIM ポリシーの詳細:

+ [変換ポリシー](../api-management-transformation-policies.md)
+ [ポリシーのサンプル](../policy-samples.md)

