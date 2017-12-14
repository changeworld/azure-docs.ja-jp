---
title: "Azure API Management ポリシーのサンプル - 応答のキャッシュ期間を設定する |Microsoft Docs"
description: "Azure API Management ポリシーのサンプル - バックエンドによって送信された Cache-Control ヘッダーの maxAge の値を使用して、応答のキャッシュ期間を設定する方法を示します。"
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
ms.openlocfilehash: 8471b51999159c8f6233abbbce998b122163efd8
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2017
---
# <a name="set-response-cache-duration"></a>応答のキャッシュ期間を設定する

この記事では、Azure API Management ポリシーのサンプルを示します。このサンプルでは、バックエンドによって送信された Cache-Control ヘッダーの maxAge の値を使用して、応答のキャッシュ期間を設定する方法が示されます。 ポリシー コードを設定または編集するには、[ポリシーの設定または編集](../set-edit-policies.md)に関するページで説明されている手順に従います。 他の例については、[ポリシーのサンプル](../policy-samples.md)に関するページをご覧ください。

## <a name="policy"></a>[ポリシー]

コードを **inbound** ブロックに貼り付けます。

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Set cache duration using response cache control header.policy.xml)]

## <a name="next-steps"></a>次のステップ

APIM ポリシーの詳細については、以下をご覧ください。

+ [変換ポリシー](../api-management-transformation-policies.md)
+ [ポリシーのサンプル](../policy-samples.md)

