---
title: ナレッジ ベースを公開する - QnA Maker
titleSuffix: Azure Cognitive Services
description: ナレッジ ベースの公開は、自分のナレッジ ベースを質問/回答のエンドポイントとして利用可能にする最後の手順です。 ナレッジ ベースを公開すると、ナレッジ ベースの QnA コンテンツが、Azure Search のテスト インデックスから実稼働インデックスへ移動されます。
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: eceb0554d35935f1aee77c4c054cb7e65b327845
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033417"
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
