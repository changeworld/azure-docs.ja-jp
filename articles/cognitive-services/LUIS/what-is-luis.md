---
title: Language Understanding (LUIS) の概要
description: Language Understanding (LUIS) - 機械学習を自然言語での会話に使用して、意味を予測し、情報を抽出するクラウドベースの API サービス。
keywords: Azure, 人工知能, ai, 自然言語処理, nlp, 自然言語理解, nlu, LUIS, 会話型 AI, ai チャットボット, nlp ai, azure luis
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 04/16/2021
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 3a83e6d62b030c436af31cc853ef0e5c109679a1
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/25/2021
ms.locfileid: "107945145"
---
# <a name="what-is-language-understanding-luis"></a>Language Understanding (LUIS) とは

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Language Understanding (LUIS) は、カスタムの機械学習インテリジェンスをユーザーの自然言語での会話テキストに適用して、全体の意味を予測し、関連性のある詳細な情報を引き出す、クラウドベースの会話型 AI サービスです。 LUIS には、[カスタム ポータル](https://www.luis.ai)、[API][endpoint-apis]、および [SDK クライアント ライブラリ](client-libraries-rest-api.md)を介してアクセスできます。

初めてのユーザーは、次の手順に従って [LUIS ポータルにサインイン](sign-in-luis-portal.md "LUIS ポータルにサインインする")してください。作業を開始するにあたり、LUIS の[作成済みドメイン アプリ](luis-get-started-create-app.md)を試すこともできます。

このドキュメントには、次の種類の記事が含まれています。  

* [**クイックスタート**](luis-get-started-create-app.md)は、サービスへの要求の実行方法を説明する概要手順です。  
* [**攻略ガイド**](luis-how-to-start-new-app.md)には、より具体的またはカスタマイズした方法でサービスを使用するための手順が記載されています。  
* [**概念**](artificial-intelligence.md)では、サービスの機能と特徴について詳しく説明します。  
* [**チュートリアル**](tutorial-intents-only.md)はより長文のガイドであり、より広範なビジネス ソリューションの 1 コンポーネントとしてこのサービスを使用する方法を示すものです。  

## <a name="what-does-luis-offer"></a>LUIS によって提供されるもの 

* **シンプル**: LUIS を使用するにあたり、社内の AI の専門知識や事前の機械学習の知識は必要ありません。 数回クリックするだけで、独自の会話型 AI アプリケーションを作成できます。 用意されている[クイックスタート](luis-get-started-create-app.md)のいずれかに従ってカスタム アプリケーションを作成することも、[作成済みドメイン](luis-get-started-create-app.md) アプリのいずれかを使用することもできます。
* **セキュリティ、プライバシー、およびコンプライアンス**: Azure インフラストラクチャによって支えられている LUIS は、エンタープライズグレードのセキュリティ、プライバシー、およびコンプライアンスを提供します。 ユーザーのデータはユーザーのもののままです。ユーザーは、いつでもデータを削除できます。 データは、ストレージ内にあるときは暗号化されます。 詳細については、[こちら](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy)をご覧ください。
* **統合**: LUIS アプリは、[Microsoft Bot Framework](https://docs.microsoft.com/composer/tutorial/tutorial-luis)、[QnA Maker](../QnAMaker/choose-natural-language-processing-service.md)、[音声サービス](../Speech-Service/quickstarts/intent-recognition.md)などの他の Microsoft サービスと簡単に統合できます。


## <a name="luis-scenarios"></a>LUIS のシナリオ
* [エンタープライズ グレードの会話型ボットの作成](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/conversational-bot): この参照アーキテクチャでは、Azure Bot Framework を使用してエンタープライズ グレードの会話型ボット (チャットボット) を作成する方法について説明します。
* [商用チャットボット](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/commerce-chatbot): Azure Bot Service と Language Understanding サービスを組み合わせることで、開発者は銀行、旅行、エンターテイメントなどのさまざまなシナリオ用の会話型インターフェイスを作成できます。
* [音声アシスタントを使用した IoT デバイスの制御](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/iot-controlling-devices-with-voice-assistant): 接続されたテレビや冷蔵庫から、接続された発電所のデバイスまで、インターネットにアクセス可能なすべてのデバイスとのシームレスな会話型インターフェイスを作成できます。


## <a name="application-development-life-cycle"></a>アプリケーション開発ライフ サイクル

![LUIS アプリ開発ライフ サイクル](./media/luis-overview/luis-dev-lifecycle.png "LUIS アプリケーション開発ライフサイクル")

-   **計画**: ユーザーがアプリケーションを使用する可能性のあるシナリオを識別します。 認識する必要があるアクションと関連情報を定義します。
-   **作成**: お使いの作成リソースを使用してアプリを開発します。 [意図](luis-concept-intent.md)と[エンティティ](luis-concept-entity-types.md)を定義することから始めます。 次に、各意図のトレーニング用の[発話](luis-concept-utterance.md)を追加します。 
-   **テストと改善**: 他の発話でモデルのテストを開始して、アプリの動作を把握し、改善が必要かどうかを判断することができます。 これらの[ベスト プラクティス](luis-concept-best-practices.md)に従うことで、アプリケーションを改善できます。 
-   **公開**: 予測用にアプリをデプロイし、予測リソースを使用してエンドポイントに対してクエリを実行します。 作成および予測リソースの詳細については、[こちら](luis-how-to-azure-subscription.md#luis-resources)を参照してください。 
-   **接続**: [Microsoft Bot Framework](https://docs.microsoft.com/composer/tutorial/tutorial-luis)、[QnA Maker](../QnAMaker/choose-natural-language-processing-service.md)、[音声サービス](../Speech-Service/quickstarts/intent-recognition.md)などの他のサービスに接続します。 
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
