---
title: ナレッジ ベースの公開
titleSuffix: QnA Maker API - Azure Cognitive Services
description: QnA Maker API サービスを使用したナレッジ ベースの公開は、自分のナレッジ ベースを質問/回答のエンドポイントとして利用可能にする最後の手順です。 ナレッジ ベースを公開すると、ナレッジ ベースの QnA コンテンツが、Azure Search のテスト インデックスから実稼働インデックスへ移動されます。
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/11/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: c65654e7d6b2e66a07116ea9555ed316ace88912
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58091955"
---
# <a name="publish-a-knowledge-base-using-the-qna-maker-api-service-portal"></a>QnA Maker API サービス ポータルを使用してナレッジ ベースを公開する

QnA Maker API サービスを使用したナレッジ ベースの公開は、自分のナレッジ ベースを質問/回答のエンドポイントとして利用可能にする最後の手順です。 

ナレッジ ベースを公開すると、ナレッジ ベースの質問と回答コンテンツが、Azure Search のテスト インデックスから実稼働インデックスへ移動されます。

![実稼働へのテスト インデックスの公開](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。 

## <a name="publish-a-knowledge-base"></a>ナレッジ ベースの公開

1. ナレッジ ベースへの変更が完了したら、上部のナビゲーション バーにある **[公開]** を選択します。 Azure Search の割り当てられたナレッジ ベース番号に公開できます。 

    ![ナレッジ ベースを公開する](../media/qnamaker-how-to-publish-kb/publish.png)

2. もう一度 **[公開]** を選択して、アプリケーションまたはボット コードで使用できるエンドポイントの詳細を確認します。

    ![正常に公開されたナレッジ ベース](../media/qnamaker-how-to-publish-kb/publish-success.png)
    
## <a name="clean-up-resources"></a>リソースのクリーンアップ

ナレッジ ベースを作成できたら、QnA Maker ポータルで削除します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ナレッジ ベースに関する分析結果の取得](./get-analytics-knowledge-base.md)
