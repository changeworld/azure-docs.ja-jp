---
title: Language Understanding (LUIS) の概要
description: Language Understanding (LUIS) - 機械学習を自然言語での会話に使用して、意味を予測し、情報を抽出するクラウドベースの API サービス。
keywords: Azure, 人工知能, ai, 自然言語処理, nlp, 自然言語理解, nlu, LUIS, 会話型 AI, ai チャットボット, nlp ai, azure luis
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 10/20/2021
ms.custom: cog-serv-seo-aug-2020, ignite-fall-2021
ms.openlocfilehash: 1595831e4da756e16df257ce93d28b03bb7c6eab
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069256"
---
# <a name="what-is-language-understanding-luis"></a>Language Understanding (LUIS) とは

> [!NOTE]
> 新しいバージョンの Language Understanding 機能が、Azure Cognitive Service for Language の一部として使用できるようになりました。 詳しくは、[Azure Cognitive Service for Language のドキュメント](../language-service/index.yml)参照してください。 Language Service 内の言語理解機能については、「[Conversational Language Understanding](../language-service/conversational-language-understanding/overview.md)」、「[カスタム固有表現認識](../language-service/custom-named-entity-recognition/overview.md)」、「[カスタム分類](../language-service/custom-classification/overview.md)」を参照してください。

Language Understanding (LUIS) は、カスタムの機械学習インテリジェンスをユーザーの自然言語での会話テキストに適用して、全体の意味を予測し、関連性のある詳細な情報を引き出す、クラウドベースの会話型 AI サービスです。 LUIS には、[カスタム ポータル](https://www.luis.ai)、[API][endpoint-apis]、および [SDK クライアント ライブラリ](client-libraries-rest-api.md)を介してアクセスできます。

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

初めてのユーザーは、次の手順に従って [LUIS ポータルにサインイン](sign-in-luis-portal.md "LUIS ポータルにサインインする")してください。作業を開始するにあたり、LUIS の[作成済みドメイン アプリ](luis-get-started-create-app.md)を試すこともできます。

このドキュメントには、次の種類の記事が含まれています。  

* [**クイックスタート**](luis-get-started-create-app.md)は、サービスへの要求の実行方法を説明する概要手順です。  
* [**攻略ガイド**](luis-how-to-start-new-app.md)には、より具体的またはカスタマイズした方法でサービスを使用するための手順が記載されています。  
* [**概念**](artificial-intelligence.md)では、サービスの機能と特徴について詳しく説明します。  
* [**チュートリアル**](tutorial-intents-only.md)はより長文のガイドであり、より広範なビジネス ソリューションの 1 コンポーネントとしてこのサービスを使用する方法を示すものです。  

<<<<<<< HEAD
## <a name="use-luis-in-a-chat-bot"></a>チャット ボットでの LUIS の使用

<a name="Accessing-LUIS"></a>

Azure LUIS アプリの公開後、クライアント アプリケーションは、LUIS 自然言語処理エンドポイント [API][endpoint-apis] に発話 (テキスト) を送信し、その結果を JSON 応答として受信します。 LUIS の一般的なクライアント アプリケーションはチャット ボットです。


![自然言語の理解 (NLP) でユーザー テキストを予測するためにチャット ボットと連携する LUIS の概念図](./media/luis-overview/LUIS-chat-bot-request-response.svg "自然言語の理解 (NLP) でユーザー テキストを予測するためにチャット ボットと連携する LUIS の概念図")

|手順|アクション|
|:--|:--|
|1|クライアント アプリケーションがユーザーの "_発話_" (自分の言葉で表現されたテキスト) を送信します。ここでは、"I want to call my HR rep." を LUIS エンドポイントに HTTP 要求として送信します。|
|2|LUIS を使うと、カスタム言語モデルを作成して、アプリケーションにインテリジェンスを追加できます。 機械学習言語モデルにより、ユーザーの非構造化入力テキストが取得され、最上位の意図 `HRContact` を含む JSON 形式の応答が返されます。 エンドポイントからの JSON 形式の応答には、少なくとも、クエリの発話と上位スコアの意図が含まれます。 _Contact Type_ エンティティなどのデータを抽出することもできます。|
|3|クライアント アプリケーションが、JSON 応答を使用して、ユーザーから受けた要求への対応方法を判断します。 これらの判断には、Bot Framework コードでのデシジョン ツリーや、他のサービスの呼び出しが含まれることがあります。 |

LUIS アプリは、クライアント アプリケーションが賢明な選択を行えるようにするためのインテリジェンスを提供します。 そうした選択を LUIS が提供するわけではありません。

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-understanding-nlu"></a>自然言語理解 (NLU)

LUIS は、自然言語処理 AI のサブセットである NLU の形式で[人工知能 (AI) を提供](artificial-intelligence.md "LUIS は人工知能 (AI) を提供します")します。

LUIS アプリには、特定の領域 (ドメイン) に固有の自然言語モデルが含まれます。 LUIS アプリは、構築済みのドメイン モデルをひな形にできるほか、独自にモデルを構築したり、構築済みのドメインの一部に独自の情報を融合したりすることができます。

* **構築済みのモデル**: LUIS には、意図、発話、および構築済みのエンティティを含む構築済みのドメイン モデルが多数あります。 構築済みのモデルの意図と発話を使用せずに、構築済みのエンティティを使用できます。 [構築済みのドメイン モデル](./howto-add-prebuilt-models.md "事前構築済みドメイン モデル")には、必要な設計がすべて含まれているため、LUIS の使用をすぐに開始できる優れた方法です。

* **カスタム モデル** の LUIS を使うと、意図を含む独自のカスタムモデルやエンティティを複数の方法で識別できます。 エンティティには、機械学習エンティティ、特定のエンティティまたはリテラル エンティティ、機械学習エンティティとリテラル エンティティの組み合わせが含まれます。

[NLP AI](artificial-intelligence.md "NLP") の詳細と、NLU の LUIS 固有の領域について学習してください。

## <a name="step-1-design-and-build-your-model"></a>手順 1:モデルを設計して構築する

**[意図](luis-concept-intent.md "意図")** と呼ばれる、ユーザーの意図のカテゴリを使用してモデルを設計します。 意図にはそれぞれ、ユーザー **[発話](luis-concept-utterance.md "発話")** の例が必要です。 それぞれの発話からデータが得られますが、それらのデータは[機械学習エンティティ](luis-concept-entity-types.md#effective-machine-learned-entities "機械学習エンティティ")を使って抽出する必要があります。

|ユーザーの発話例|Intent|抽出されたデータ|
|-----------|-----------|-----------|
|`Book a flight to Seattle?`|BookFlight|Seattle|
|`When does your store open?`|StoreHoursAndLocation|open|
|`Schedule a meeting at 1pm with Bob in Distribution`|ScheduleMeeting|1pm、Bob (午後 1 時、Bob)|

モデルの構築には、[オーサリング](https://go.microsoft.com/fwlink/?linkid=2092087 "オーサリング") API シリーズ、**[LUIS ポータル](https://www.luis.ai "LUIS ポータル")**、またはその両方を使用します。 [ポータル](get-started-portal-build-app.md "ポータル")と [SDK クライアント ライブラリ](./client-libraries-rest-api.md?pivots=rest-api "SDK クライアント ライブラリ")を使用して構築する方法について確認してください。

## <a name="step-2-get-the-query-prediction"></a>手順 2:クエリ予測を取得する

アプリのモデルがトレーニングされてエンドポイントに発行された後、クライアント アプリケーション (チャットボットなど) によって、予測[エンドポイント](https://go.microsoft.com/fwlink/?linkid=2092356 "endpoint") API に発話が送信されます。 API によって、分析のために発話にモデルが適用され、予測結果が JSON 形式で返されます。

エンドポイントからの JSON 形式の応答には、少なくとも、クエリの発話と上位スコアの意図が含まれます。 以下の **Contact Type** エンティティや全体的なセンチメントなどのデータを抽出することもできます。

```JSON
{
    "query": "I want to call my HR rep",
    "prediction": {
        "topIntent": "HRContact",
        "intents": {
            "HRContact": {
                "score": 0.8582669
            }
        },
        "entities": {
            "Contact Type": [
                "call"
            ]
        },
        "sentiment": {
            "label": "neutral",
            "score": 0.5
        }
    }
}
```

## <a name="step-3-improve-model-prediction"></a>手順 3:モデルの予測の改善

LUIS アプリが公開され、実際のユーザーの発話を受け取ると、予測精度向上のためにエンドポイント発話の[アクティブな学習](luis-concept-review-endpoint-utterances.md "アクティブ ラーニング")が LUIS によって提供されます。 これらの提案は、開発ライフサイクルの定期的なメンテナンス作業の一部として確認してください。

<a name="using-luis"></a>

## <a name="development-lifecycle-and-tools"></a>開発ライフサイクルとツール
LUIS を使うと、完全な[開発ライフ サイクル](luis-concept-app-iteration.md "開発ライフ サイクル")に統合するためのツール、バージョン管理、および他の LUIS 作成者とのコラボレーションが提供されます。

Language Understanding (LUIS) は、REST API として、HTTP 要求を行う任意の製品、サービス、フレームワークで使用できます。 LUIS には、いくつかの主要なプログラミング言語用のクライアント ライブラリ (SDK) も用意されています。 提供されている[開発者向けリソース](developer-reference-resource.md "開発者リソース")の詳細を確認してください。

LUIS をボットですばやく簡単に使用するためのツール:
* [LUIS CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS "LUIS CLI"): この NPM パッケージは、スタンドアロンのコマンドライン ツールまたはインポートとして、作成と予測を提供します。
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen "LUISGen"): LUISGen は、エクスポートされた LUIS モデルから厳密に型指定された C# および typescript のソース コードを生成するためのツールです。
* [Dispatch](https://aka.ms/dispatch-tool "Dispatch"): ディスパッチャー モデルを使うことで、いくつかの LUIS アプリや QnA Maker アプリを親アプリから使用できます。
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown "LUDown"): LUDown は、ボットの言語モデルを管理するのに役立つコマンドライン ツールです。

## <a name="integrate-with-a-bot"></a>ボットとの統合

[Azure Bot Service](/azure/bot-service/ "Azure Bot service") を [Microsoft Bot Framework](https://dev.botframework.com/ "Microsoft Bot Framework") と共に使用して、チャットボットを構築してデプロイします。 設計と開発には、グラフィカル インターフェイス ツールの [Composer](/composer/ "Composer") または主要なボット シナリオ用に設計された[実用的なボット サンプル](https://github.com/microsoft/BotBuilder-Samples "作業ボットのサンプル")を使用します。
=======
## <a name="what-does-luis-offer"></a>LUIS によって提供されるもの 
>>>>>>> repo_sync_working_branch

* **シンプル**: LUIS を使用するにあたり、社内の AI の専門知識や事前の機械学習の知識は必要ありません。 数回クリックするだけで、独自の会話型 AI アプリケーションを作成できます。 用意されている[クイックスタート](luis-get-started-create-app.md)のいずれかに従ってカスタム アプリケーションを作成することも、[作成済みドメイン](luis-get-started-create-app.md) アプリのいずれかを使用することもできます。
* **セキュリティ、プライバシー、およびコンプライアンス**: Azure インフラストラクチャによって支えられている LUIS は、エンタープライズグレードのセキュリティ、プライバシー、およびコンプライアンスを提供します。 ユーザーのデータはユーザーのもののままです。ユーザーは、いつでもデータを削除できます。 データは、ストレージ内にあるときは暗号化されます。 詳細については、[こちら](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy)をご覧ください。
* **統合**: LUIS アプリは、[Microsoft Bot Framework](/composer/tutorial/tutorial-luis)、[QnA Maker](../QnAMaker/choose-natural-language-processing-service.md)、[音声サービス](../speech-service/get-started-intent-recognition.md)などの他の Microsoft サービスと簡単に統合できます。


## <a name="luis-scenarios"></a>LUIS のシナリオ
* [エンタープライズ グレードの会話型ボットの作成](/azure/architecture/reference-architectures/ai/conversational-bot): この参照アーキテクチャでは、Azure Bot Framework を使用してエンタープライズ グレードの会話型ボット (チャットボット) を作成する方法について説明します。
* [商用チャットボット](/azure/architecture/solution-ideas/articles/commerce-chatbot): Azure Bot Service と Language Understanding サービスを組み合わせることで、開発者は銀行、旅行、エンターテイメントなどのさまざまなシナリオ用の会話型インターフェイスを作成できます。
* [音声アシスタントを使用した IoT デバイスの制御](/azure/architecture/solution-ideas/articles/iot-controlling-devices-with-voice-assistant): 接続されたテレビや冷蔵庫から、接続された発電所のデバイスまで、インターネットにアクセス可能なすべてのデバイスとのシームレスな会話型インターフェイスを作成できます。


## <a name="application-development-life-cycle"></a>アプリケーション開発ライフ サイクル

![LUIS アプリ開発ライフ サイクル](./media/luis-overview/luis-dev-lifecycle.png "LUIS アプリケーション開発ライフサイクル")

-   **計画**: ユーザーがアプリケーションを使用する可能性のあるシナリオを識別します。 認識する必要があるアクションと関連情報を定義します。
-   **作成**: お使いの作成リソースを使用してアプリを開発します。 [意図](luis-concept-intent.md)と[エンティティ](luis-concept-entity-types.md)を定義することから始めます。 次に、各意図のトレーニング用の[発話](luis-concept-utterance.md)を追加します。 
-   **テストと改善**: 他の発話でモデルのテストを開始して、アプリの動作を把握し、改善が必要かどうかを判断することができます。 これらの[ベスト プラクティス](luis-concept-best-practices.md)に従うことで、アプリケーションを改善できます。 
-   **公開**: 予測用にアプリをデプロイし、予測リソースを使用してエンドポイントに対してクエリを実行します。 作成および予測リソースの詳細については、[こちら](luis-how-to-azure-subscription.md)を参照してください。 
-   **接続**: [Microsoft Bot Framework](/composer/tutorial/tutorial-luis)、[QnA Maker](../QnAMaker/choose-natural-language-processing-service.md)、[音声サービス](../speech-service/get-started-intent-recognition.md)などの他のサービスに接続します。 
-   **調整**: [エンドポイントの発話を確認](luis-concept-review-endpoint-utterances.md)し、実際の例を使用してアプリケーションを改善します

アプリケーションの計画と作成の詳細については、[こちら](luis-how-plan-your-app.md)を参照してください。

## <a name="next-steps"></a>次のステップ

* サービスとドキュメントの[最新情報](whats-new.md "新機能")
* [LUIS アプリの作成](tutorial-intents-only.md)
* [API リファレンス][endpoint-apis]
* [ベスト プラクティス](luis-concept-best-practices.md)
* LUIS の[開発者向けリソース](developer-reference-resource.md "開発者向けリソース")。
* [意図](luis-concept-intent.md "意図")と[エンティティ](luis-concept-entity-types.md "entities")を使用する[アプリを計画する](luis-how-plan-your-app.md "アプリの計画")。

[bot-framework]: /bot-framework/
[flow]: /connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
