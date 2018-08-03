---
title: LUIS で Speech C# SDK を使用する - Azure | Microsoft Docs
titleSuffix: Azure
description: Speech C# SDK サンプルを使ってマイクに向かって話し、返される LUIS のインテントおよびエンティティ予測を取得します。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/26/2018
ms.author: diberry;
ms.openlocfilehash: 286efcd97c0c9ab95a8241215bc36799c486a8b6
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2018
ms.locfileid: "39247717"
---
# <a name="integrate-speech-service"></a>音声サービスを統合する
[音声サービス](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/)を使用すると、1 回の要求を使用して音声を受け取り、LUIS 予測 JSON オブジェクトを返せます。

この記事では、C# プロジェクトをダウンロードして Visual Studio で使用し、マイクに向かって発話して LUIS 予測情報を受け取ります。 プロジェクトでは、参照として既に含まれている Speech [NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/) パッケージを使用します。 

この記事では、アプリケーションをインポートするための、無料の [LUIS ][LUIS] Website アカウントが必要です。

## <a name="create-luis-endpoint-key"></a>LUIS エンドポイント キーを作成する
Azure Portal で、**Language Understanding** (LUIS) キーを[作成](luis-how-to-azure-subscription.md#create-luis-endpoint-key)します。 

## <a name="import-human-resources-luis-app"></a>人事管理 LUIS アプリをインポートする
この記事のインテントと発話は、[LUIS サンプル](https://github.com/Microsoft/LUIS-Samples) Github リポジトリにある LUIS アプリからのものです。 [HumanResources.json](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/HumanResources.json) ファイルをダウンロードし、*.json 拡張子を付けて保存し、LUIS に[インポート](luis-how-to-start-new-app.md#import-new-app)します。 

このアプリには、人事分野に関連するインテント、エンティティ、および発話があります。 発話の例を次に示します。

```
Who is John Smith's manager?
Who does John Smith manage?
Where is Form 123456?
Do I have any paid time off?
```

## <a name="add-keyphrase-prebuilt-entity"></a>KeyPhrase 事前構築済みエンティティを追加する
アプリをインポートしたら、**[エンティティ]** を選択し、**[Manage prebuilt entities]\(構築済みのエンティティを管理\)** を選択します。 **KeyPhrase** エンティティを追加します。 KeyPhrase エンティティは、発話からキーの主題を抽出します。

## <a name="train-and-publish-the-app"></a>アプリをトレーニングして公開する
1. 右上のナビゲーション バーで、**[トレーニングする]** ボタンをクリックして LUIS アプリをトレーニングします。

2. **[公開]** をクリックして公開ページに移動します。 

3. **[公開]** ページ下部で、「[LUIS エンドポイント キーを作成する](#create-luis-endpoint-key)」セクションで作成した LUIS キーを追加します。

4. 公開スロットの右側にある **[公開]** ボタンを選択して LUIS アプリを公開します。 

  **[公開]** ページで、アプリ ID、公開リージョン、および「[LUIS エンドポイント キーを作成する](#create-luis-endpoint-key)」セクションで作成した LUIS キーのサブスクリプション ID を収集します。 この記事の後半でこれらの値を使用するためにコードを変更する必要があります。 

  これらの値はすべて、作成したキーの **[公開]** ページの下部にあるエンドポイント URL に含まれています。 
  
  この演習では、無料のスターター キーを使用**しないで**ください。 この演習では、Azure Portal で作成された **Language Understanding** キーのみが使用できます。 

  https://**REGION**.api.cognitive.microsoft.com/luis/v2.0/apps/**APPID**?subscription-key=**LUISKEY**&q=

## <a name="audio-device"></a>オーディオ デバイス
この記事では、お使いのコンピューターのオーディオ デバイスを使用します。 これには、マイク付きのヘッドセットや、コンピューターに内蔵されているオーディオ デバイスが含まれます。 オーディオ デバイスに音声を認識させるために、通常よりも大きな音量で話す必要があるかどうか、オーディオの入力レベルを確認してください。 

## <a name="download-the-luis-sample-project"></a>LUIS サンプル プロジェクトをダウンロードする
 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples) リポジトリを複製またはダウンロードします。 Visual Studio で [Speech to intent project](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-speech-intent-recognition) を開き、NuGet パッケージを復元します。 VS ソリューション ファイルは .\LUIS-Samples-master\documentation-samples\tutorial-speech-intent-recognition\csharp\csharp_samples.sln です。

Speech SDK が既に参照として含まれています。 

[![](./media/luis-tutorial-speech-to-intent/nuget-package.png "Microsoft.CognitiveServices.Speech NuGet パッケージを表示した Visual Studio 2017 のスクリーン ショット")](./media/luis-tutorial-speech-to-intent/nuget-package.png#lightbox)

## <a name="modify-the-c-code"></a>C# コードを編集する
**LUIS_samples.cs** ファイルを開き、次の変数を変更します。

|変数名|目的|
|--|--|
|luisSubscriptionKey|[発行] ページからのエンドポイント URL のサブスクリプション キーの値に対応しています|
|luisRegion|エンドポイント URL の最初のサブドメインに対応しています|
|luisAppId|**apps/** の後に続く、エンドポイント URL のルートに対応します|

[![](./media/luis-tutorial-speech-to-intent/change-variables.png "Visual Studio 2017 LUIS_samples.cs 変数を表示したスクリーン ショット")](./media/luis-tutorial-speech-to-intent/change-variables.png#lightbox)

ファイルには既に人事インテントがマップされています。

[![](./media/luis-tutorial-speech-to-intent/intents.png "LUIS_samples.cs インテントを表示した Visual Studio 2017 のスクリーン ショット")](./media/luis-tutorial-speech-to-intent/intents.png#lightbox)

アプリケーションをビルドし、実行します。 

## <a name="test-code-with-utterance"></a>発話を使ってコードをテストする
**1** を選択し、マイクに向かって「John Smith の上司は誰ですか」と話します。

```cmd
1. Speech recognition of LUIS intent.
0. Stop.
Your choice: 1
LUIS...
Say something...
ResultId:cc83cebc9d6040d5956880bcdc5f5a98 Status:Recognized IntentId:<GetEmployeeOrgChart> Recognized text:<Who is the manager of John Smith?> Recognized Json:{"DisplayText":"Who is the manager of John Smith?","Duration":25700000,"Offset":9200000,"RecognitionStatus":"Success"}. LanguageUnderstandingJson:{
  "query": "Who is the manager of John Smith?",
  "topScoringIntent": {
    "intent": "GetEmployeeOrgChart",
    "score": 0.617331
  },
  "entities": [
    {
      "entity": "manager of john smith",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 31
    }
  ]
}

Recognition done. Your Choice:

```

正しいインテント、**GetEmployeeOrgChart** が61% の信頼度で見つかりました。 keyPhrase エンティティが返されました。 

Speech SDK は、LUIS の応答全体を返します。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ
不要になったら、LUIS HumanResources アプリを削除します。 それを行うには、アプリの一覧内のアプリ名の右にある省略記号 (***...***) ボタンを選択し、**[削除]** を選択します。 **[Delete app?]\(アプリを削除しますか?\)** ポップアップ ダイアログで、**[OK]** をクリックします。

サンプル コードの使用が終わったら、LUIS Samples ディレクトリを削除します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [LUIS をボットと統合する](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website