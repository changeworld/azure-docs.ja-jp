---
title: 'チュートリアル: Azure Bot Service を使用して複数ドメイン用の FAQ ボットを作成する'
description: このチュートリアルでは、QnA Maker と Azure Bot Service を使用して運用ユース ケース用の FAQ ボットをコーディングなしで作成します。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.author: diagarw
ms.date: 03/31/2021
ms.openlocfilehash: d79eed22d441949810cfc1738f3af2c0f8703adc
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110116448"
---
# <a name="add-multiple-domains-to-your-faq-bot"></a>FAQ ボットに複数のドメインを追加する

FAQ ボットを構築するときに、複数のドメインにまたがるクエリに対応する必要があるユース ケースが発生する場合があります。 Microsoft のマーケティング チームが、複数の Surface 製品に関するユーザーの一般的な質問に回答するカスタマー サポート ボットを構築しようとしているものとします。 わかりやすくするため、ここでは、[Surface ペン](https://support.microsoft.com/surface/how-to-use-your-surface-pen-8a403519-cd1f-15b2-c9df-faa5aa924e98)と [Surface Earbuds](https://support.microsoft.com/surface/use-surface-earbuds-aea108c3-9344-0f11-e5f5-6fc9f57b21f9) についての FAQ URL を使用して、ナレッジ ベースを作成します。

次のようにすると、QnA Maker を使用して複数のドメインを対象とする質問を処理するボットを設計できます。

* 1 つのナレッジ ベースを作成し、メタデータを使用して QnA ペアに個別のドメインへのタグを付ける。
* ドメインごとに個別のナレッジ ベースを作成する。
* ドメインごとに個別の QnA Maker リソースを作成する。

## <a name="create-a-single-knowledge-base-and-tag-qna-pairs-into-distinct-domains-with-metadata"></a>1 つのナレッジ ベースを作成し、メタデータを使用して QnA ペアに個別のドメインへのタグを付ける。

コンテンツ作成者は、ドキュメントを使用して QnA を抽出したり、ナレッジ ベースにカスタム QnA を追加したりできます。 これらの QnA を特定のドメインまたはカテゴリにグループ化するには、[メタデータ](../How-To/query-knowledge-base-with-metadata.md)を QnA ペアに追加できます。

Surface 製品についてのボットの場合、次の手順のようにして、両方の製品の種類に関する質問に答えるボットを作成できます。

1. KB の作成ページのステップ 3 で Surface 製品についての次の FAQ URL を追加し、[Create your KB]\(KB の作成\) をクリックします。 これらのソースから QnA ペアが抽出された後、新しいナレッジ ベースが作成されます。 
   
   [Surface ペンの FAQ](https://support.microsoft.com/surface/how-to-use-your-surface-pen-8a403519-cd1f-15b2-c9df-faa5aa924e98)<br>[Surface Earbuds の FAQ](https://support.microsoft.com/surface/use-surface-earbuds-aea108c3-9344-0f11-e5f5-6fc9f57b21f9)
 
2. KB を作成したら、 **[View Options]\(オプションの表示\)** に移動して、 **[Show metadata]\(メタデータの表示\)** をクリックします。 これにより、QnA に関するメタデータの列が開きます。

   >[!div class="mx-imgBorder"]
   >[![メタデータを表示する]( ../media/qnamaker-tutorial-updates/show-metadata.png) ]( ../media/qnamaker-tutorial-updates/expand/show-metadata.png#lightbox)


3. このナレッジ ベースには 2 つの製品についての QnA が含まれ、それらを区別して、QnA の中から特定の製品についての応答を検索できるようにする必要があります。 そのためには、そうできるように QnA ペアのメタデータ フィールドを更新する必要があります。 

   次の例を見るとわかるように、キーが **product** で、値が **surface_pen** または **surface_earbuds** であるメタデータを、該当するすべての場所に追加しています。 この例を拡張して、複数の製品についてのデータを抽出し、製品ごとに異なる値を追加することができます。

   >[!div class="mx-imgBorder"]
   >[![メタデータ]( ../media/qnamaker-tutorial-updates/metadata-example-2.png) ]( ../media/qnamaker-tutorial-updates/expand/metadata-example-2.png#lightbox)

4. 次に、システムによる検索を特定の製品についての応答に制限するため、回答生成 API の厳密なフィルターとして、その製品を渡す必要があります。

    [GenerateAnswer API の使用方法](../How-To/metadata-generateanswer-usage.md)を確認してください。 GenerateAnswer の URL は次の形式になります。
    ```
    https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
    ```

    API 呼び出しの JSON 本文で、メタデータ *product* の値として *surface_pen* を渡しています。 そのため、システムでは、QnA ペアの中から同じメタデータを持つ応答のみが検索されます。 

    ```json
    {
        "question": "What is the price?",
        "top": 6,
        "isTest": true,
        "scoreThreshold": 30,
        "rankerType": ""  // values: QuestionOnly
        "strictFilters": [
        {
            "name": "product",
            "value": "surface_pen"
        }],
        "userId": "sd53lsY="
    }
    ```

    ユーザー入力に基づいてメタデータの値を取得するには、次のような方法があります。 

    * ボット クライアントを介して、ユーザーからの入力として明示的にドメインを取得します。 たとえば、次に示すように、会話を始めるときに、製品カテゴリをユーザーからの入力として取得できます。

      ![メタデータの入力を取得する](../media/qnamaker-tutorial-updates/expand/explicit-metadata-input.png)

    * ボットのコンテキストに基づいて、ドメインを暗黙的に識別します。 たとえば、前の質問が特定の Surface 製品についてのものであった場合、クライアントでコンテキストとしてそれを保存できます。 ユーザーが次の質問で製品を指定しない場合は、ボットのコンテキストをメタデータとして GenerateAnswer API に渡すことができます。

      ![コンテキストを渡す]( ../media/qnamaker-tutorial-updates/expand/extract-metadata-from-context.png)

    * ユーザーの質問からエンティティを抽出して、メタデータ フィルターに使用するドメインを識別します。 Text Analytics や LUIS などの他の Cognitive Services を、エンティティの抽出に使用できます。

      ![質問からメタデータを抽出する]( ../media/qnamaker-tutorial-updates/expand/extract-metadata-from-query.png)

### <a name="how-large-can-our-knowledge-bases-be"></a>ナレッジ ベースはどのくらい大きくできるか 

1 つのナレッジ ベースに、最大で 50,000 の QnA ペアを追加できます。 データが 50,000 QnA ペアを超える場合は、ナレッジ ベースの分割を検討する必要があります。

## <a name="create-a-separate-knowledge-base-for-each-domain"></a>ドメインごとに個別のナレッジ ベースを作成する

また、ドメインごとに異なるナレッジ ベースを作成し、ナレッジ ベースを個別に管理することもできます。 すべての API では、ナレッジ ベースを更新したり、ユーザーの質問に対する回答をフェッチしたりするために、ユーザーはナレッジ ベース ID を渡す必要があります。  

ユーザーの質問をサービスが受け取るときに、関連するナレッジ ベースから応答をフェッチするため、上記の Generate Answer エンドポイントで KB ID を渡す必要があります。 次に示すように、 **[発行]** ページ セクションでナレッジ ベース ID を見つけることができます。

>[!div class="mx-imgBorder"]
>![KB ID をフェッチする](../media/qnamaker-tutorial-updates/fetch-kb-id.png)

## <a name="create-a-separate-qna-maker-resource-for-each-domain"></a>ドメインごとに個別の QnA Maker リソースを作成する。

Microsoft のマーケティング チームが、Surface 製品と Xbox 製品に関するユーザーの質問に回答するカスタマー サポート ボットを構築しようとしているものとします。 Surface と Xbox のナレッジ ベースにアクセスするために、個別のチームを割り当てる予定です。 この場合は、Surface 用と Xbox 用に 2 つの QnA Maker リソースを作成する必要があります。 ただし、同じリソースにアクセスするユーザーに対して個別のロールを定義できます。 詳細については、[ロールベースのアクセス制御](../How-To/manage-qna-maker-app.md)に関する記事を参照してください。 
