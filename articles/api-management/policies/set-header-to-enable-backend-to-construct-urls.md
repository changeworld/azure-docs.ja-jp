---
title: Azure API Management ポリシーのサンプル - 転送ヘッダーを追加する | Microsoft Docs
description: Azure API Management ポリシーのサンプル - 受信要求に転送ヘッダーを追加して、バックエンド API が適切な URL を構築できるようにする方法を説明します。
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
ms.openlocfilehash: efa64b4847ae2effe20090e3fcb962c0c9e56d8f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128647045"
---
# <a name="add-a-forwarded-header"></a>転送ヘッダーの追加

この記事では、受信要求に転送ヘッダーを追加して、バックエンド API が適切な URL を構築できるようにする方法を示す Azure API Management ポリシーのサンプルを紹介します。 ポリシー コードを設定または編集するには、[ポリシーの設定または編集](../set-edit-policies.md)に関するページで説明されている手順に従います。 他の例については、[ポリシーのサンプル](../policy-reference.md)に関するページをご覧ください。

## <a name="code"></a>コード

コードを **inbound** ブロックに貼り付けます。

[!code-xml[Main](../../../api-management-policy-samples/examples/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>次のステップ

APIM ポリシーの詳細については、以下をご覧ください。

+ [変換ポリシー](../api-management-transformation-policies.md)
+ [ポリシーのサンプル](../policy-reference.md)