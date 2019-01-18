---
title: リソースとキーの管理 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker サービスは、サブスクリプション キーとエンドポイント キーの 2 種類のキーを扱います。
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 11/26/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 3984ac84271728b9066aba15a582065ea3121c76
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2018
ms.locfileid: "53581761"
---
# <a name="how-to-manage-keys-in-qna-maker"></a>QnA Maker でキーを管理する方法

QnA Maker サービスは、**サブスクリプション キー**と**エンドポイント キー**の 2 種類のキーを扱います。

![キー管理](../media/qnamaker-how-to-key-management/key-management.png)

1. **サブスクリプション キー**: これらのキーは、[QnA Maker 管理サービス API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) へのアクセスに使用されます。 これらの API を使用して、ナレッジ ベースを編集できます。  

2. **エンドポイント キー**: これらのキーは、ナレッジ ベースのエンドポイントにアクセスして、ユーザーの質問に対する回答を取得する目的で使用されます。 通常、このエンドポイントは、QnA Maker サービスを利用するチャット ボットまたはクライアント アプリ コードで使用します。
 
## <a name="subscription-keys"></a>サブスクリプション キー
サブスクリプション キーは、QnA Maker リソースを作成した Azure portal で表示したりリセットしたりすることができます。 
1. Azure portal の QnA Maker リソースに移動します。

    ![QnA Maker のリソース リスト](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. **[キー]** に移動します。

    ![サブスクリプション キー](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="endpoint-keys"></a>エンドポイント キー

エンドポイント キーは、[QnA Maker ポータル](https://qnamaker.ai)から管理することができます。

1. [QnA Maker ポータル](https://qnamaker.ai)にログインし、**[サービスの設定]** に移動します。

    ![エンドポイント キー](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. キーを表示またはリセットします。

    ![エンドポイント キー マネージャー](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >鍵漏えいのおそれがある場合は、キーを更新してください。 それに合わせて、クライアント アプリまたはボットのコードの変更が必要になることがあります。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [別の言語によるナレッジ ベースの作成](./language-knowledge-base.md)
