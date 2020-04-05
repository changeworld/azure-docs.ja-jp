---
title: サンプルの API Management ポリシー - バックエンド サービスへの機能の追加
titleSuffix: Azure API Management
description: Azure API Management ポリシーのサンプル - バックエンド サービスに機能を追加する方法を示します。 たとえば、天気予報 API で、緯度と経度ではなく場所の名前を受け入れます。
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
ms.openlocfilehash: e286f77009a17870332ed5caf1c66c18dd21d020
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442483"
---
# <a name="add-capabilities-to-a-backend-service"></a>バックエンド サービスへの機能の追加

この記事では、バックエンド サービスに機能を追加する方法を示す Azure API Management ポリシーのサンプルを示します。 たとえば、天気予報 API で、緯度と経度ではなく場所の名前を受け入れます。 ポリシー コードを設定または編集するには、[ポリシーの設定または編集](../set-edit-policies.md)に関するページで説明されている手順に従います。 他の例については、[ポリシーのサンプル](../policy-samples.md)に関するページをご覧ください。

## <a name="policy"></a>ポリシー

コードを **inbound** ブロックに貼り付けます。

[!code-xml[Main](../../../api-management-policy-samples/examples/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>次のステップ

APIM ポリシーの詳細については、以下をご覧ください。

+ [変換ポリシー](../api-management-transformation-policies.md)
+ [ポリシーのサンプル](../policy-samples.md)

