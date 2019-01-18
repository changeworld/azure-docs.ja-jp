---
title: キーの管理
titleSuffix: Language Understanding - Azure Cognitive Services
description: Azure portal で LUIS エンドポイント キーを作成したら、そのキーを LUIS アプリに割り当てて、適切なエンドポイント URL を取得します。 このエンドポイント URL を使用して LUIS の予測を取得します。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 0a278ded7ce290347645f345e4eee0b15972787f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53088104"
---
# <a name="add-an-azure-luis-resource-to-app"></a>Azure の LUIS リソースをアプリに追加する

Azure portal で LUIS リソースを作成したら、そのリソースを LUIS アプリに割り当てて、適切なエンドポイント URL を取得します。 このエンドポイント URL を使用して LUIS の予測を取得します。

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
<a name="key-concepts"></a>
<a name="authoring-key"></a>
<a name="create-and-use-an-endpoint-key"></a>
<a name="assign-endpoint-key"></a>
<a name="assign-resource"></a>


## <a name="assign-resource-in-luis-portal"></a>LUIS ポータルでリソースを割り当てる

1. [Azure portal](https://portal.azure.com) で LUIS キーを作成します。 詳細な手順については、[Azure を使用したエンドポイント キーの作成](luis-how-to-azure-subscription.md)に関するページを参照してください。
 
2. 右上のメニューの **[管理]** を選択し、**[Keys and endpoints]\(キーとエンドポイント)** を選択します。

    [![[Keys and endpoints]\(キーとエンドポイント) ページ](./media/luis-manage-keys/keys-and-endpoints.png)](./media/luis-manage-keys/keys-and-endpoints.png#lightbox)

3. LUIS を追加するには、**[Assign Resource +]\(リソースの割り当て +)** を選択します。

    ![アプリにリソースを割り当てる](./media/luis-manage-keys/assign-key.png)

4. ダイアログ ボックスで、LUIS Web サイトへのログインで使用したメール アドレスに関連付けられているテナントを選択します。  

5. 追加する Azure リソースに関連付けられている**サブスクリプション名**を選択します。

6. **LUIS リソース名**を選択します。 

7. **[Assign resource]\(リソースの割り当て)** を選択します。 

8. テーブルで新しい行を見つけて、そのエンドポイント URL をコピーします。 予測のために LUIS エンドポイントに対して HTTP GET 要求を行えれば、正しく構築されています。 

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

### <a name="unassign-resource"></a>リソースの割り当てを解除する
エンドポイント キーは、割り当てを解除しても Azure からは削除されません。 LUIS からのリンクが解除されるだけです。 

エンドポイント キーが割り当て解除されたり、アプリに割り当てられていないときには、エンドポイント URL に対するどの要求でもエラー `401 This application cannot be accessed with the current subscription` が返されます。 

### <a name="include-all-predicted-intent-scores"></a>予測されたすべての意図スコアを含める
**[Include all predicted intent scores]\(予測されたすべての意図スコアを含める\)** チェック ボックスをオンにすると、エンドポイントのクエリ応答に各意図の予測スコアを含めることができます。 

この設定を使用すると、チャットボットまたは LUIS 呼び出し元のアプリケーションは、返される意図のスコアに基づいてプログラムによる意思決定を行うことができます。 一般に、最も興味深いのは最上位の 2 つの意図です。 最高スコアが None 意図の場合、チャットボットは None 意図と他の高スコア意図をはっきり選択するためのフォローアップ質問を行うことができます。 

意図とそのスコアには、エンドポイントのログにも含まれています。 これらのログを[エクスポート](luis-how-to-start-new-app.md#export-app)して、スコアを分析できます。 

```JSON
{
  "query": "book a flight to Cairo",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.5223427
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.5223427
    },
    {
      "intent": "BookFlight",
      "score": 0.372391433
    }
  ],
  "entities": []
}
```

### <a name="enable-bing-spell-checker"></a>Bing のスペル チェック機能を有効にする 
**[Endpoint url settings]\(エンドポイント URL 設定)** では、**[Bing spell checker]\(Bing のスペル チェック機能)** トグルによって、LUIS での予測前にスペルミスがある単語を収集できます。 **[Bing Spell Check キー](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)** を作成します。 

**spellCheck=true** querystring パラメーターと **bing-spell-check-subscription-key={YOUR_BING_KEY_HERE}** を追加します。 `{YOUR_BING_KEY_HERE}` を実際の Bing スペル チェック機能キーに置き換えます。

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```


### <a name="publishing-regions"></a>公開リージョン

公開[リージョン](luis-reference-regions.md) ([ヨーロッパ](luis-reference-regions.md#publishing-to-europe)および[オーストラリア](luis-reference-regions.md#publishing-to-australia)での公開を含む) の詳細を確認してください。 公開リージョンは、オーサリング リージョンとは異なります。 アプリの作成は、エンドポイントのクエリに使用する公開リージョンに対応するオーサリング リージョンで行います。

## <a name="assign-resource-without-luis-portal"></a>LUIS ポータルを使用せずにリソースを割り当てる

CI/CD パイプラインなどの自動化を目指す場合、LUIS アプリへの LUIS リソースの割り当てを自動化する必要があります。 そのためには、次の手順を実行する必要があります。

1. こちらの [Web サイト](https://resources.azure.com/api/token?plaintext=true)から Azure Resource Manager トークンを取得します。 このトークンには有効期限があるため、速やかに使用してください。 この要求からは、Azure Resource Manager トークンが返されます。

    ![Azure Resource Manager トークンを要求して Azure Resource Manager トークンを受け取る](./media/luis-manage-keys/get-arm-token.png)

1. ご使用のユーザー アカウントで利用できるサブスクリプション全体で、[LUIS Azure アカウントの取得 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c) からこのトークンを使用して LUIS リソースを要求します。 

    この POST API には、次の設定が必要です。

    |ヘッダー|値|
    |--|--|
    |`Authorization`|`Authorization` の値は `Bearer {token}` です。 トークンの値の前に、`Bearer` という単語とスペースを 1 つ入力する必要があることに注意してください。| 
    |`Ocp-Apim-Subscription-Key`|実際の[オーサリング キー](luis-how-to-account-settings.md)。|

    この API は、LUIS サブスクリプションの JSON オブジェクトの配列を返します。この配列に、サブスクリプション ID、リソース グループ、リソース名 (アカウント名として返される) が含まれます。 LUIS アプリに割り当てる LUIS リソースである要素を配列から 1 つ見つけてください。 

1. [アプリケーションへの LUIS Azure アカウントの割り当て](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) API を使用して、LUIS リソースにトークンを割り当てます。 

    この POST API には、次の設定が必要です。

    |type|Setting|値|
    |--|--|--|
    |ヘッダー|`Authorization`|`Authorization` の値は `Bearer {token}` です。 トークンの値の前に、`Bearer` という単語とスペースを 1 つ入力する必要があることに注意してください。|
    |ヘッダー|`Ocp-Apim-Subscription-Key`|実際の[オーサリング キー](luis-how-to-account-settings.md)。|
    |ヘッダー|`Content-type`|`application/json`|
    |Querystring|`appid`|LUIS アプリ ID。 
    |本文||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    この API は、成功した場合、201 - created 状態を返します。 

## <a name="next-steps"></a>次の手順

ご自身のキーを使用して、**[Publish app]\(アプリの公開\)** ページでご自身のアプリを公開します。 公開の手順については、[アプリの公開](luis-how-to-publish-app.md)に関するページをご覧ください。

LUIS のオーサリングおよびエンドポイント キーの概念については、「[LUIS でのキー](luis-concept-keys.md)」を参照してください。
