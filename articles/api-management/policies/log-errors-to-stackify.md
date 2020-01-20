---
title: サンプルの API 管理ポリシー - ログ記録のために Stackify にエラーを送信する
titleSuffix: Azure API Management
description: Azure API Management ポリシーのサンプル - エラー ログ ポリシーを追加して、ログ記録のために Stackify にエラーを送信する　方法を示します。
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
ms.openlocfilehash: 6662761df005211729dffb16282b8e0a8e2a8444
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442438"
---
# <a name="send-errors-to-stackify-for-logging"></a>ログ記録のために Stackify にエラーを送信する

この記事では、Azure API Management ポリシーのサンプルを示し、エラー ログ ポリシーを追加して、ログ記録のために Stackify にエラーを送信する　方法を説明します。 ポリシー コードを設定または編集するには、[ポリシーの設定または編集](../set-edit-policies.md)に関するページで説明されている手順に従います。 他の例については、[ポリシーのサンプル](../policy-samples.md)に関するページをご覧ください。

## <a name="policy"></a>ポリシー

コードを **on-error** ブロックに貼り付けます。

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>次のステップ

APIM ポリシーの詳細については、以下をご覧ください。

+ [変換ポリシー](../api-management-transformation-policies.md)
+ [ポリシーのサンプル](../policy-samples.md)

