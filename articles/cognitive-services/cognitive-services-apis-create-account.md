---
title: Azure portal で Cognitive Services リソースを作成する
titleSuffix: Azure Cognitive Services
description: Azure portal でリソースを作成し、サブスクライブすることによって、Azure Cognitive Services の使用を開始します。
services: cognitive-services
author: aahill
manager: nitinme
keywords: Cognitive Services, コグニティブ インテリジェンス, コグニティブ ソリューション, AI サービス
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 09/14/2020
ms.author: aahi
ms.openlocfilehash: 0ad2d0a10b20aa049147d61bd4e8168ea956392c
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031924"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-portal"></a>クイックスタート: Azure portal を使用して Cognitive Services リソースを作成する

このクイックスタートを使用して、Azure Cognitive Services の使用を開始します。 Azure portal で Cognitive Services リソースを作成すると、アプリケーションの認証を行うためのエンドポイントとキーが得られます。

Azure Cognitive Services は、開発者が直接的な人工知能 (AI) またはデータ サイエンスのスキルや知識がなくてもコグニティブかつインテリジェントなアプリケーションを構築できる、REST API シリーズとクライアント ライブラリ SDK を含むクラウドベースのサービスです。 開発者は Azure Cognitive Services を使用して、見たり、聞いたり、話したり、理解したり、推論し始めたりできるコグニティブ ソリューションを使用したコグニティブ機能をそのアプリケーションに容易に追加することができます。


[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>前提条件

* 有効な Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/cognitive-services/)。

## <a name="create-a-new-azure-cognitive-services-resource"></a>新しい Azure Cognitive Services リソースを作成する

1. リソースを作成します。

    #### <a name="multi-service-resource"></a>[マルチサービス リソース](#tab/multiservice)

    マルチサービス リソースには、ポータルで **Cognitive Services** という名前が付けられます。 [Cognitive Services リソースを作成します](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)。

    現在、マルチサービス リソースでは次の Cognitive Services にアクセスできます。

    - Computer Vision
    - Content Moderator
    - Face
    - Language Understanding (LUIS)
    - Text Analytics
    - [変換者]
    - Bing Search v7 <br>(Web、画像、ニュース、ビデオ、ビジュアル)
    - Bing Custom Search
    - Bing Entity Search
    - Bing Autosuggest
    - Bing Spell Check

    #### <a name="single-service-resource"></a>[単一サービス リソース](#tab/singleservice)

    以下のリンクを使用して、利用可能な Cognitive Services のリソースを作成します。

    | 視覚                      | 音声                  | Language                          | 決定             | 検索                 |
    |-----------------------------|-------------------------|-----------------------------------|----------------------|------------------------|
    | [Computer Vision](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [Speech Services](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [イマーシブ リーダー](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [Anomaly Detector](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | [Bing Search API V7](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7) |
    | [Custom Vision Service](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) | [Speaker Recognition](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeakerRecognition) | [Language Understanding (LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | [Bing Custom Search](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingCustomSearch) |
    | [Face](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [Personalizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     | [Bing Entity Search](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingEntitySearch) |
    | [Ink Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesInkRecognizer)        |                         | [Text Analytics](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |  [Metrics Advisor](https://go.microsoft.com/fwlink/?linkid=2142156)                    | [Bing Spell Check](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSpellCheck-v7)   |
    |           |                         | [Translator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)               |                      | [Bing Autosuggest](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingAutosuggest-v7)                       |

    ***

3. **[作成]** ページで、次の情報を指定します。

    #### <a name="multi-service-resource"></a>[マルチサービス リソース](#tab/multiservice)

    |    |    |
    |--|--|
    | **名前** | Cognitive Sservices リソースのわかりやすい名前。 例: *MyCognitiveServicesResource*。 |
    | **サブスクリプション** | 使用できる Azure サブスクリプションのいずれかを選択します。 |
    | **場所** | Cognitive Services インスタンスの場所。 別の場所を選択すると待機時間が生じる可能性がありますが、リソースのランタイムの可用性には影響しません。 |
    | **価格レベル** | Cognitive Services アカウントのコストは、選択しているオプションと使用量によって異なります。 詳細については、「[API の価格の詳細](https://azure.microsoft.com/pricing/details/cognitive-services/)」をご覧ください。
    | **リソース グループ** | Cognitive Services リソースを含むことになる Azure リソース グループ。 新しいグループを作成することも、既存のグループに追加することもできます。 |

    ![マルチサービス リソースのリソース作成画面](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    **Create** をクリックしてください。

    #### <a name="single-service-resource"></a>[単一サービス リソース](#tab/singleservice)

    |    |    |
    |--|--|
    | **名前** | Cognitive Sservices リソースのわかりやすい名前。 例: *TextAnalyticsResource*。 |
    | **サブスクリプション** | 使用できる Azure サブスクリプションのいずれかを選択します。 |
    | **場所** | Cognitive Services インスタンスの場所。 別の場所を選択すると待機時間が生じる可能性がありますが、リソースのランタイムの可用性には影響しません。 |
    | **価格レベル** | Cognitive Services アカウントのコストは、選択しているオプションと使用量によって異なります。 詳細については、「[API の価格の詳細](https://azure.microsoft.com/pricing/details/cognitive-services/)」をご覧ください。
    | **リソース グループ** | Cognitive Services リソースを含むことになる Azure リソース グループ。 新しいグループを作成することも、既存のグループに追加することもできます。 |

    ![単一サービス リソースのリソース作成画面](media/cognitive-services-apis-create-account/resource_create_screen.png)

    **Create** をクリックしてください。

    ***

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]

## <a name="get-the-keys-for-your-resource"></a>リソースのキーを取得する

1. リソースが正常にデプロイされたら、 **[次の手順]** の下にある **[リソースに移動]** をクリックします。

    ![Cognitive Services を検索する](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. 開かれたクイックスタート ウィンドウで、キーとエンドポイントにアクセスできます。

    ![キーとエンドポイントを取得する](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、そのグループに含まれている他のリソースも削除されます。

1. Azure Portal で左側のメニューを展開してサービスのメニューを開き、 **[リソース グループ]** を選択して、リソース グループの一覧を表示します。
2. 削除するリソースが含まれているリソース グループを見つけます
3. リソース グループの一覧を右クリックします。 **[リソース グループの削除]** を選択し、確認します。

## <a name="see-also"></a>関連項目

* [Azure Cognitive Services に対する要求の認証](authentication.md)
* [Azure Cognitive Services とは](./what-are-cognitive-services.md)
* [Azure Management クライアント ライブラリを使用して新しいリソースを作成する](.\cognitive-services-apis-create-account-client-library.md)
* [自然言語のサポート](language-support.md)
* [Docker コンテナーのサポート](cognitive-services-container-support.md)