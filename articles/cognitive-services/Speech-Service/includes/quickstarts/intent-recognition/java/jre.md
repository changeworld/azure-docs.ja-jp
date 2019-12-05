---
title: クイック スタート:音声、意図、エンティティを認識する、Java - Speech Service
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: c9aa4200dc217e9c79290ca91b03810f0769bdc2
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280424"
---
## <a name="prerequisites"></a>前提条件

開始する前に、必ず次のことを行ってください。

> [!div class="checklist"]
>
> * [Azure Speech リソースを作成する](../../../../get-started.md)
> * [Language Understanding (LUIS) アプリケーションを作成し、エンドポイント キーを取得する](../../../../quickstarts/create-luis.md)
> * [使用する開発環境を設定する](../../../../quickstarts/setup-platform.md?tabs=jre)
> * [空のサンプル プロジェクトを作成する](../../../../quickstarts/create-project.md?tabs=jre)

## <a name="open-your-project"></a>プロジェクトを開く

プロジェクトを読み込んで `Main.java` を開きます。

## <a name="start-with-some-boilerplate-code"></a>定型コードを使用して開始する

このプロジェクトのスケルトンとして機能する何らかのコードを追加してみましょう。
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-20,69-76)]

## <a name="create-a-speech-configuration"></a>Speech 構成を作成する

`IntentRecognizer` オブジェクトを初期化するには、LUIS エンドポイント キーとリージョンを使用する構成を作成する必要があります。 main の try/catch ブロックにこのコードを挿入します

このサンプルでは、`FromSubscription()` メソッドを使用して `SpeechConfig` をビルドします。 使用可能なメソッドの完全な一覧については、[SpeechConfig クラス](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)に関する記事を参照してください。

> [!NOTE]
> 音声意図判定認識にはエンドポイント キーのみが有効となるため、スターター キーや オーサリング キーではなく、LUIS エンドポイント キーを使用することが重要です。 正しいキーを取得する方法については、[LUIS アプリケーションを作成し、エンドポイント キーを取得する](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md)方法に関する記事を参照してください。

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=27)]

## <a name="initialize-an-intentrecognizer"></a>IntentRecognizer を初期化する

ここで、`IntentRecognizer` を作成しましょう。 Speech 構成のすぐ下にこのコードを挿入します。
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=30)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>LanguageUnderstandingModel と意図を追加する

次に、`LanguageUnderstandingModel` と意図認識エンジンを関連付け、認識させる意図を追加する必要があります。
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=33-36)]

## <a name="recognize-an-intent"></a>意図を認識する

`IntentRecognizer` オブジェクトから、`recognizeOnceAsync()` メソッドを呼び出します。 認識の対象として 1 つの語句を送信しようとしていること、また、その語句が識別された後で、音声認識を停止しようとしていることが、このメソッドを通じて Speech サービスに伝えられます。

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=41)]

## <a name="display-the-recognition-results-or-errors"></a>認識結果 (またはエラー) を表示する

音声サービスによって認識結果が返されたら、それを使用して何らかの操作を行います。 シンプルに保ち、結果をコンソールに出力します。

`recognizeOnceAsync()` の呼び出しの下でコード [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=44-65)] を追加します。

## <a name="release-resources"></a>リソースを解放する

音声リソースの使用が終わったら、それを解放することが重要です。 try/catch ブロックの末尾にコード [!code-java を挿入します。[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=67-68)]

## <a name="check-your-code"></a>コードを確認する

この時点で、コードは次のようになります。  
(このバージョンにはいくつかのコメントを追加してあります) [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-76)]

## <a name="build-and-run-your-app"></a>アプリをビルドして実行する

F11 キーを押すか、 **[Run]**  >  **[Debug]** の順に選択します。
その後 15 秒間、マイクからの音声入力が認識され、コンソール ウィンドウにログが記録されます。

## <a name="next-steps"></a>次の手順

[!INCLUDE [footer](./footer.md)]