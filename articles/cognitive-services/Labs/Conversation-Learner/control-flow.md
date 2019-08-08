---
title: Conversation Learner 制御フロー - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner 制御フローについて説明します。
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 3ec839c1a930ffbe73989149360f1b02866a3c50
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705310"
---
## <a name="control-flow"></a>制御フロー

このドキュメントでは、次の図に示すように Conversation Learner (CL) の制御フローについて説明します。

![](media/controlflow.PNG)

1. ユーザーはボットに、「シアトルはどんな天気ですか?」のような用語や句を入力します
1. CL は、エンティティを抽出する機械学習モデルにユーザー入力を渡します
   - このモデルは、Conversation Learner によってビルドされ、 www.luis.ai によってホストされています
1. 抽出されたすべてのエンティティとユーザーの入力テキストは、ボットのコードで Entity Detection Callback メソッドに渡されます。
    - このコードは、エンティティ値を設定/クリア/操作できます
1. CL ニューラル ネットワークが続いて、エンティティ抽出の出力とユーザー入力を受け取り、ボットに定義されているすべてのアクションを点数化します
   - この例では、最も可能性の高いアクションは、天気予報を提供することになります。

     ![](media/controlflow_forecast.PNG)

1. この場合、選択したアクションは、API 呼び出しに天気予報を取得するように要求します。 
1. CL.AddCallback メソッドを使用して登録されたこの API が続いて呼び出されます。  次にこの API の結果が、「晴れ、華氏 67 度」のようなメッセージとしてユーザーに返されます。
1. この呼び出しは続いて、ニューラル ネットワークにされ、前の手順に基づいて次のアクションを決定します。
1. ニューラル ネットワークは続いて、次の一連の可能なアクションを予測し、この場合は「他に何かありますか?」という選択したアクションがユーザーに提示されます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Conversation Learner での学習方法](./how-to-teach-cl.md)
