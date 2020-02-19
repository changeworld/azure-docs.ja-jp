---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 01/27/2020
ms.topic: include
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 6bd276f07f75ecad21ffb0bf19150f6158eb7b7c
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77156450"
---
## <a name="prerequisites"></a>前提条件

開始する前に、以下の操作を行います。

* 今回初めて Java (JRE) プロジェクトを作成する場合は、このガイドを使用して<a href="~/articles/cognitive-services/Speech-Service/quickstarts/create-project.md?tabs=jre" target="_blank">空のサンプル プロジェクトを作成<span class="docon docon-navigate-external x-hidden-focus"></span></a>します。
* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=jre" target="_blank">開発環境に対応した Speech SDK をインストールします<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

## <a name="create-a-luis-app-for-intent-recognition"></a>意図認識用の LUIS アプリを作成する

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>プロジェクトを開く

1. 好みの IDE を開きます。
2. プロジェクトを読み込んで `Main.java` を開きます。

## <a name="start-with-some-boilerplate-code"></a>定型コードを使用して開始する

このプロジェクトのスケルトンとして機能するコードを追加しましょう。

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-20,68-75)]

## <a name="create-a-speech-configuration"></a>Speech 構成を作成する

`IntentRecognizer` オブジェクトを初期化する前に、LUIS 予測リソース用のキーとリージョンを使用する構成を作成する必要があります。  

`main()` の try/catch ブロックにこのコードを挿入します。 次の値を必ず更新してください。

* `"YourLanguageUnderstandingSubscriptionKey"` を LUIS 予測キーで置き換えます。
* `"YourLanguageUnderstandingServiceRegion"` を LUIS の場所で置き換えます。 [リージョン](https://aka.ms/speech/sdkregion)の "Speech SDK パラメーター" を使用します

>[!TIP]
> これらの値を見つける方法については、「[意図認識用の LUIS アプリを作成する](#create-a-luis-app-for-intent-recognition)」を参照してください。

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=27)]

このサンプルでは、`FromSubscription()` メソッドを使用して `SpeechConfig` をビルドします。 使用可能なメソッドの完全な一覧については、[SpeechConfig クラス](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)に関する記事を参照してください。

Speech SDK では、既定で認識される言語が en-us です。ソース言語の選択については、「[音声テキスト変換のソース言語を指定する](../../../../how-to-specify-source-language.md)」を参照してください。

## <a name="initialize-an-intentrecognizer"></a>IntentRecognizer を初期化する

ここで、`IntentRecognizer` を作成しましょう。 Speech 構成のすぐ下にこのコードを挿入します。

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=30)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>LanguageUnderstandingModel と意図を追加する

`LanguageUnderstandingModel` と意図認識エンジンを関連付け、認識させる意図を追加する必要があります。 ホーム オートメーション用のあらかじめ構築されたドメインの意図を使用します。

次のコードを `IntentRecognizer` の下に挿入します。 `"YourLanguageUnderstandingAppId"` は必ずお客様の LUIS app ID で置き換えてください。

>[!TIP]
> この値を見つける方法については、「[意図認識用の LUIS アプリを作成する](#create-a-luis-app-for-intent-recognition)」を参照してください。

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=33-35)]

## <a name="recognize-an-intent"></a>意図を認識する

`IntentRecognizer` オブジェクトから、`recognizeOnceAsync()` メソッドを呼び出します。 認識の対象として 1 つの語句を送信しようとしていること、また、その語句が識別された後で、音声認識を停止しようとしていることが、このメソッドを通じて Speech サービスに伝えられます。

次のコードをモデルの下に挿入します。

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=40)]

## <a name="display-the-recognition-results-or-errors"></a>認識結果 (またはエラー) を表示する

音声サービスによって認識結果が返されたら、それを使用して何らかの操作を行います。 シンプルに保ち、結果をコンソールに出力します。

次のコードを `recognizeOnceAsync()` の呼び出しの下に挿入します。[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=43-64)]

## <a name="release-resources"></a>リソースを解放する

音声リソースの使用が終わったら、それを解放することが重要です。 try/catch ブロックの末尾に次のコードを挿入します。

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=66-67)]

## <a name="check-your-code"></a>コードを確認する

この時点で、コードは次のようになります。

> [!NOTE]
> このバージョンにはいくつかのコメントを追加してあります。

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-75)]

## <a name="build-and-run-your-app"></a>アプリをビルドして実行する

<kbd>F11</kbd> キーを押すか、 **[Run]**  >  **[Debug]** の順に選択します。
その後 15 秒間、マイクからの音声入力が認識され、コンソール ウィンドウにログが記録されます。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [footer](./footer.md)]
