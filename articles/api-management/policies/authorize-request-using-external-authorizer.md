---
title: Azure API Management ポリシーのサンプル - 既存の承認者を使用して要求を承認する | Microsoft Docs
description: Azure API Management ポリシーのサンプル - カスタムまたは従来の認証/承認ロジックをカプセル化する外部承認者を使用して、要求を承認する方法を示します。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: apimpm
ms.openlocfilehash: 7d172a40f2aad65b595026fc656634060a1f7193
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2018
ms.locfileid: "36284874"
---
# <a name="authorize-requests-using-external-authorizer"></a>外部承認者を使用して要求を承認する

この記事では、カスタムの認証/承認ロジックをカプセル化する外部承認者を使用して、API アクセスをセキュリティで保護する方法を説明する Azure API Management ポリシーのサンプルを示します。 ポリシー コードを設定または編集するには、[ポリシーの設定または編集](../set-edit-policies.md)に関するページで説明されている手順に従います。 他の例については、[ポリシーのサンプル](../policy-samples.md)に関するページをご覧ください。

## <a name="policy"></a>ポリシー

コードを **inbound** ブロックに貼り付けます。

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>次の手順

APIM ポリシーの詳細については、以下をご覧ください。

+ [アクセス制限ポリシー](../api-management-access-restriction-policies.md)
+ [ポリシーのサンプル](../policy-samples.md)