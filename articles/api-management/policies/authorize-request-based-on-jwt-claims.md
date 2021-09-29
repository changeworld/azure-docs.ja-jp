---
title: API Management ポリシーのサンプル - JWT クレームに基づいてアクセスを承認する
titleSuffix: Azure API Management
description: Azure API Management ポリシーのサンプル - API の特定の HTTP メソッドに対して、JWT クレームに基づいてアクセスを承認する方法を示します。
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
ms.openlocfilehash: 1df5f4b874ff3e5e05ab6b72a786364916ae0f53
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128618820"
---
# <a name="authorize-access-based-on-jwt-claims"></a>JWT クレームに基づいてアクセスを承認する

この記事では、API の特定の HTTP メソッドに対して、JWT クレームに基づくアクセスを承認する方法を示す Azure API Management ポリシーのサンプルを説明します。 ポリシー コードを設定または編集するには、[ポリシーの設定または編集](../set-edit-policies.md)に関するページで説明されている手順に従います。 他の例については、[ポリシーのサンプル](../policy-reference.md)に関するページをご覧ください。

## <a name="policy"></a>ポリシー

コードを **inbound** ブロックに貼り付けます。

[!code-xml[Main](../../../api-management-policy-samples/examples/Pre-authorize requests based on HTTP method with validate-jwt.policy.xml)]

## <a name="next-steps"></a>次のステップ

APIM ポリシーの詳細については、以下をご覧ください。

+ [変換ポリシー](../api-management-transformation-policies.md)
+ [ポリシーのサンプル](../policy-reference.md)