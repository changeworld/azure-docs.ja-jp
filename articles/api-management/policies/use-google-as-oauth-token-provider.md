---
title: サンプルの API Management ポリシー - Google OAuth トークンを使用してアクセスを承認する
titleSuffix: Azure API Management
description: Azure API Management ポリシーのサンプル - Google を OAuth トークン プロバイダーとして使用して、エンドポイントへのアクセスを承認する方法を示します。
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
ms.openlocfilehash: 6d80c416bd8dd8e1a8af1e7ea2228e71884a80c3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601028"
---
# <a name="authorize-access-using-google-oauth-token"></a>Google OAuth トークンを使用してアクセスを承認する

この記事では、Google を OAuth トークン プロバイダーとして使用して、エンドポイントへのアクセスを承認する方法を示す、Azure API Management ポリシーのサンプルを紹介します。 ポリシー コードを設定または編集するには、[ポリシーの設定または編集](../set-edit-policies.md)に関するページで説明されている手順に従います。 他の例については、[ポリシーのサンプル](../policy-reference.md)に関するページをご覧ください。

## <a name="policy"></a>ポリシー

コードを **inbound** ブロックに貼り付けます。

[!code-xml[Main](../../../api-management-policy-samples/examples/Simple Google OAuth validate-jwt.policy.xml)]

## <a name="next-steps"></a>次のステップ

APIM ポリシーの詳細については、以下をご覧ください。

+ [変換ポリシー](../api-management-transformation-policies.md)
+ [ポリシーのサンプル](../policy-reference.md)