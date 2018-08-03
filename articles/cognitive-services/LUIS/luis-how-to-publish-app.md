---
title: LUIS アプリを発行する | Microsoft Docs
description: Language Understanding (LUIS) を使用してアプリを作成してテストした後、アプリを Web サービスとして Azure で発行します。
services: cognitive-services
titleSuffix: Azure
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: diberry;
ms.openlocfilehash: b827f157ccd2919c959385eefd0e0223a8e23607
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225542"
---
# <a name="publish-your-trained-app"></a>トレーニング済みのアプリを発行する
LUIS アプリの作成とテストが完了したら、アプリを発行します。 アプリを発行すると、[発行] ページに関連するすべての HTTP [エンドポイント](luis-glossary.md#endpoint)が表示されます。 これらのエンドポイントは[リージョン](luis-reference-regions.md)ごと、および[キー](luis-how-to-manage-keys.md)ごとであり、任意のクライアント、チャットボット、バックエンド アプリケーションに統合されます。 

アプリを発行する前に、常にアプリを[テスト](luis-interactive-test.md)できます。 

## <a name="production-and-staging-slots"></a>運用スロットとステージング スロット
アプリは、**ステージング スロット**または**運用スロット**に発行できます。 2 つの発行スロットを使うことにより、2 つの異なるエンドポイントで異なるバージョンまたは同じバージョンを発行できます。 

<!-- TBD: what is the technical difference? log files, endpoint quota? -->

## <a name="settings-configuration-requires-publishing-model"></a>設定の構成には発行モデルが必要
次の設定を変更した後、エンドポイントに発行します。 

## <a name="external-services-settings"></a>外部サービスの設定
外部サービスの設定には、**[感情分析](#enable-sentiment-analysis)** と**[音声認識の準備](#enable-speech-priming)** が含まれます。

### <a name="enable-sentiment-analysis"></a>感情分析を有効にする
**[External services settings]\(外部サービスの設定\)** の **[Enable Sentiment Analysis]\(感情分析を有効にする\)** チェック ボックスを使用すると、LUIS は[テキスト分析](https://azure.microsoft.com/services/cognitive-services/text-analytics/)と統合して感情分析とキーフレーズ分析を提供できます。 Text Analytics キーを指定する必要はなく、Azure アカウントに対するこのサービスの課金はありません。 この設定を確認した後、設定は保持されます。 

センチメント データは 1 と 0 の間のスコアで、1 に近いほどポジティブな感情を示し、0 に近いほどネガティブな感情を示します。

感情分析での JSON エンドポイントの応答の詳細については、「[Sentiment analysis](luis-concept-data-extraction.md#sentiment-analysis)」(感情分析) をご覧ください。

### <a name="enable-speech-priming"></a>音声認識の準備を有効にする 
**[External services settings]\(外部サービスの設定\)** の **[Enable Speech Priming]\(音声認識の準備を有効にする\)** チェック ボックスをオンにすると、単一のエンドポイントでチャットボットまたは LUIS 呼び出しアプリケーションから話された発話を取得し、LUIS 予測の応答を受け取ることができます。 音声認識の準備は、Cognitive Service の [Speech API](../Speech-Service/rest-apis.md) を使用します。 

![音声認識の準備の確認ダイアログ ボックスの画像](./media/luis-how-to-publish-app/speech-prime-modal.png)

この機能を有効にした後、アプリを発行します。 LUIS アプリを発行すると、アプリ モデルが独自の音声認識サービスに送信されて、音声認識サービスが準備されます。 モデルの情報は、独自のサービスの外部では使用され**ません**。 

音声認識の準備を完了するには、[Speech SDK](../speech-service/speech-sdk-reference.md) で次の情報を使用する必要があります。
* LUIS エンドポイント キー。
* LUIS アプリ ID。
* エンドポイント ドメイン。Speech SDK では "Hostname" と参照されます (例: "westus.api.cognitive.microsoft.com")。最初のサブドメインは、アプリが発行されるリージョンです。

詳細については、[音声意図判定](http://aka.ms/speechsdk)に関するサンプルをご覧ください。

LUIS アプリまたは音声認識サービスが削除されると、モデル データが削除されます。 

## <a name="endpoint-url-settings"></a>エンドポイント URL の設定
エンドポイント URL のサービス設定には、**[タイム ゾーン](#set-timezone-offset)** オフセット、**[すべての予測された意図スコア](#include-all-predicted-intent-scores)**、および **[Bing スペル チェック機能](#enable-bing-spell-checker)** が含まれます。

### <a name="set-timezone-offset"></a>タイム ゾーン オフセットを設定する 
スロット選択の一部は、タイム ゾーンの選択です。 このタイム ゾーン設定により、LUIS は、返されるエンティティ データが選択されているタイム ゾーンに合わせて正しくなるように、作成済みのすべての datetimeV2 時間値を予測の間に[変更](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)できます。 

### <a name="include-all-predicted-intent-scores"></a>予測されたすべての意図スコアを含める
**[Include all predicted intent scores]\(予測されたすべての意図スコアを含める\)** チェック ボックスをオンにすると、エンドポイントのクエリ応答に各意図の予測スコアを含めることができます。 

この設定を使用すると、チャットボットまたは LUIS 呼び出し元のアプリケーションは、返される意図のスコアに基づいてプログラムによる意思決定を行うことができます。 一般に、最も興味深いのは最上位の 2 つの意図です。 最高スコアが None 意図の場合、チャットボットは None 意図と他の高スコア意図をはっきり選択するためのフォローアップ質問を行うことができます。 

意図とそのスコアには、エンドポイントのログにも含まれています。 これらのログを[エクスポート](luis-how-to-start-new-app.md#export-app)して、スコアを分析できます。 

```
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
**[Endpoint url settings]\(エンドポイント URL 設定\)** の **[Enable Bing spell checker]\(Bing のスペル チェック機能を有効にする\)** チェック ボックスをオンにすると、LUIS は予測の前にスペルミスがある単語を収集できます。 **[Bing Spell Check キー](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)** を作成します。 キーが作成されると、2 つの querystring パラメーターが発行ページのエンドポイント URL に追加されます。 

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

## <a name="publish-your-trained-app-to-an-http-endpoint"></a>トレーニング済みアプリを HTTP エンドポイントに発行する
**[マイ アプリ]** ページで名前をクリックしてアプリを開き、上部のパネルで **[発行]** をクリックします。 

![発行ページ](./media/luis-how-to-publish-app/publish-to-production.png)
 
アプリが正常に発行されると、ブラウザーの上部に緑色の成功通知が表示されます。 

* **[スロットの選択]** の下にあるドロップダウン メニューから、発行先として **[本番環境]** または **[ステージング環境]** を選択します。 

## <a name="assign-key"></a>キーを割り当てる

示されている無料の Starter_Key 以外のキーを使いたい場合は、**[キーの追加]** ボタンをクリックします。 この操作で開くダイアログでは、アプリに割り当てる既存のエンドポイント キーを選択できます。 エンドポイント キーを作成して LUIS アプリに追加する方法の詳細については、[キーの管理](luis-how-to-manage-keys.md)に関するページをご覧ください。

他のリージョンと関連付けられているエンドポイントおよびキーを確認するには、ラジオ ボタンを使ってリージョンを切り替えます。 **[Resources and Keys]\(リソースとキー\)** テーブルの各行には、アカウントに関連付けられている Azure リソースおよびそのリソースに関連付けられているエンドポイント キーが一覧表示されます。

## <a name="endpoint-url-construction"></a>エンドポイント URL の構成
エンドポイント URL は、エンドポイント キーに関連付けられている Azure リージョンに対応します。

次の表は、URL エンドポイントでの発行構成とルート選択およびクエリ文字列の値を反映しており便利です。 LUIS 呼び出しアプリケーションのエンドポイント URL を作成している場合は、これらと同じルートとクエリ文字列の値が、使用されるエンドポイントに設定されていることを確認します (設定したい場合)。

URL ルートは、リージョンとアプリ ID で構成されます。 他のリージョンまたは他のアプリに発行する場合は、リージョンとアプリ ID の値を変更することによってエンドポイント URL を作成できます。 

* [本番環境] スロットを選択し、**[発行]** ボタンを選択します。 発行が成功したら、表示されるエンドポイント URL を使って LUIS アプリにアクセスします。 

### <a name="optional-query-string-parameters"></a>オプションのクエリ文字列パラメーター
次のクエリ文字列パラメーターは、エンドポイント URL で使用できます。

<!-- TBD: what about speech priming? -->

|クエリ文字列|Type|値の例|目的|
|--|--|--|--|
|verbose|ブール値|true|発話の[すべての意図スコア](#include-all-predicted-intent-scores)を含めます|
|timezoneOffset|数 (単位は分)|60|[datetimeV2 作成済みエンティティ](luis-reference-prebuilt-datetimev2.md)の[タイム ゾーン オフセット](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)を設定します|
|spellCheck|ブール値|true|発話の[スペル訂正](#enable-bing-spell-checker) -- bing-spell-check-subscription-key クエリ文字列パラメーターと組み合わせて使用します|
|bing-spell-check-subscription-key|サブスクリプション ID||spellCheck クエリ文字列パラメーターと組み合わせて使用します|
|staging|ブール値|false|ステージング エンドポイントまたは本番環境エンドポイントを選択します|
|log|ブール値|true|クエリと結果をログに追加します|


## <a name="test-your-published-endpoint-in-a-browser"></a>発行したエンドポイントをブラウザーでテストする
**[エンドポイント]** 列で URL を選択して、発行したエンドポイントをテストします。 生成された URL が既定のブラウザーで開きます。 URL パラメーター "&q" にテスト クエリを設定します。 たとえば、`&q=Book me a flight to Boston on May 4` を URL に追加し、Enter キーを押します。 ブラウザーに HTTP エンドポイントの JSON 応答が表示されます。 

![発行された HTTP エンドポイントからの JSON 応答](./media/luis-how-to-publish-app/luis-publish-app-json-response.png)

## <a name="next-steps"></a>次の手順

* LUIS アプリへのキーの追加、およびリージョンへのキーのマップ方法については、[キーの管理](./luis-how-to-manage-keys.md)に関するページをご覧ください。
* テスト コンソールでの発行済みアプリのテスト方法については、[アプリのトレーニングとテスト](luis-interactive-test.md)に関するページをご覧ください。
