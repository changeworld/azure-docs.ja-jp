---
title: サンプルの API Management ポリシー - Google OAuth トークンを使用してアクセスを承認する
titleSuffix: Azure API Management
description: Azure API Management ポリシーのサンプル - Google を OAuth トークン プロバイダーとして使用して、エンドポイントへのアクセスを承認する方法を示します。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: d606d29d84cd5917c74efe188ae02627ad55d4ab
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442368"
---
# <a name="authorize-access-using-google-oauth-token"></a>Google OAuth トークンを使用してアクセスを承認する

この記事では、Google を OAuth トークン プロバイダーとして使用して、エンドポイントへのアクセスを承認する方法を示す、Azure API Management ポリシーのサンプルを紹介します。 ポリシー コードを設定または編集するには、[ポリシーの設定または編集](../set-edit-policies.md)に関するページで説明されている手順に従います。 他の例については、[ポリシーのサンプル](../policy-samples.md)に関するページをご覧ください。

## <a name="policy"></a>ポリシー

コードを **inbound** ブロックに貼り付けます。

[!code-xml[Main](../../../api-management-policy-samples/examples/Simple Google OAuth validate-jwt.policy.xml)]

## <a name="next-steps"></a>次のステップ

APIM ポリシーの詳細については、以下をご覧ください。

+ [変換ポリシー](../api-management-transformation-policies.md)
+ [ポリシーのサンプル](../policy-samples.md)

