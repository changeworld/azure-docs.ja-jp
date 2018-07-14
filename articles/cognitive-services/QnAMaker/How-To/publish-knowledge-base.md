---
title: ナレッジ ベースの公開方法 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: ナレッジ ベースの公開方法
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: e9dbeacfb0df98c6b8f084c263690c05fe966cdc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376168"
---
# <a name="publish-a-knowledge-base"></a>ナレッジ ベースの公開

ナレッジ ベースの公開は、自分のナレッジ ベースを質問/回答のエンドポイントとして利用可能にする最後の手順です。 

ナレッジ ベースを公開すると、ナレッジ ベースの QnA コンテンツが、Azure Search のテスト インデックスから実稼働インデックスへ移動されます。

![実稼働へのテスト インデックスの公開](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

## <a name="publish-a-knowledge-base"></a>ナレッジ ベースの公開

1. ナレッジ ベースへの変更が完了したら、上部のナビゲーション バーにある **[公開]** を選択します。 Azure Search の割り当てられたナレッジ ベース番号に公開できます。 

    ![ナレッジ ベースを公開する](../media/qnamaker-how-to-publish-kb/publish.png)

2. もう一度 **[公開]** を選択して、アプリケーションまたはボット コードで使用できるエンドポイントの詳細を確認します。

    ![ナレッジ ベースを公開する](../media/qnamaker-how-to-publish-kb/publish-success.png)
    
## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ナレッジ ベースに関する分析結果の取得](./get-analytics-knowledge-base.md)
