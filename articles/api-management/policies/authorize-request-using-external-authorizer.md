---
title: API Management ポリシーのサンプル - 外部承認者を使用して要求を承認する
titleSuffix: Azure API Management
description: Azure API Management ポリシーのサンプル - カスタムまたは従来の認証/承認ロジックをカプセル化する外部承認者を使用して、要求を承認する方法を示します。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2018
ms.author: apimpm
ms.openlocfilehash: 99bf1068042eb7ab0c43e2a683ca7116d2e426f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442497"
---
# <a name="authorize-requests-using-external-authorizer"></a>外部承認者を使用して要求を承認する

この記事では、カスタムの認証/承認ロジックをカプセル化する外部承認者を使用して、API アクセスをセキュリティで保護する方法を説明する Azure API Management ポリシーのサンプルを示します。 ポリシー コードを設定または編集するには、[ポリシーの設定または編集](../set-edit-policies.md)に関するページで説明されている手順に従います。 他の例については、[ポリシーのサンプル](../policy-samples.md)に関するページをご覧ください。

## <a name="policy"></a>ポリシー

コードを **inbound** ブロックに貼り付けます。

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>次のステップ

APIM ポリシーの詳細については、以下をご覧ください。

+ [アクセス制限ポリシー](../api-management-access-restriction-policies.md)
+ [ポリシーのサンプル](../policy-samples.md)
