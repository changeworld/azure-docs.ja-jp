---
title: クイック スタート:音声、意図、エンティティを認識する、Python - Speech Service
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
ms.openlocfilehash: f39ddc8a3460bc026bdac96c18b5bea4d6ecfc0f
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280380"
---
## <a name="prerequisites"></a>前提条件

開始する前に、必ず次のことを行ってください。

> [!div class="checklist"]
>
> * [Azure Speech リソースを作成する](../../../../get-started.md)
> * [Language Understanding (LUIS) アプリケーションを作成し、エンドポイント キーを取得する](../../../../quickstarts/create-luis.md)
> * [使用する開発環境を設定する](../../../../quickstarts/setup-platform.md)
> * [空のサンプル プロジェクトを作成する](../../../../quickstarts/create-project.md)

## <a name="open-your-project"></a>プロジェクトを開く

Python エディターで Quickstart.py を開きます。

## <a name="start-with-some-boilerplate-code"></a>定型コードを使用して開始する

このプロジェクトのスケルトンとして機能する何らかのコードを追加してみましょう。
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-7)]

## <a name="create-a-speech-configuration"></a>Speech 構成を作成する

`IntentRecognizer` オブジェクトを初期化するには、LUIS エンドポイント キーとリージョンを使用する構成を作成する必要があります。 このコードを次に挿入します。

このサンプルでは、LUIS キーとリージョンを使用して `SpeechConfig` オブジェクトを構築します。 使用可能なメソッドの完全な一覧については、[SpeechConfig クラス](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig)に関する記事を参照してください。

> [!NOTE]
> 音声意図判定認識にはエンドポイント キーのみが有効となるため、スターター キーや オーサリング キーではなく、LUIS エンドポイント キーを使用することが重要です。 正しいキーを取得する方法については、[LUIS アプリケーションを作成し、エンドポイント キーを取得する](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md)方法に関する記事を参照してください。

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=12)]

## <a name="initialize-an-intentrecognizer"></a>IntentRecognizer を初期化する

ここで、`IntentRecognizer` を作成しましょう。 Speech 構成のすぐ下にこのコードを挿入します。
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=15)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>LanguageUnderstandingModel と意図を追加する

次に、`LanguageUnderstandingModel` と意図認識エンジンを関連付け、認識させる意図を追加する必要があります。
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=19-27)]

## <a name="recognize-an-intent"></a>意図を認識する

`IntentRecognizer` オブジェクトから、`recognize_once()` メソッドを呼び出します。 認識の対象として 1 つの語句を送信しようとしていること、また、その語句が識別された後で、音声認識を停止しようとしていることが、このメソッドを通じて Speech サービスに伝えられます。
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=35)]

## <a name="display-the-recognition-results-or-errors"></a>認識結果 (またはエラー) を表示する

音声サービスによって認識結果が返されたら、それを使用して何らかの操作を行います。 シンプルに保ち、結果をコンソールに出力します。

使用するステートメントの中で、`recognize_once()` の呼び出しの下に、このコードを追加します: [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=38-47)]

## <a name="check-your-code"></a>コードを確認する

この時点で、コードは次のようになります。  
(このバージョンにはいくつかのコメントを追加してあります) [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-47)]

## <a name="build-and-run-your-app"></a>アプリをビルドして実行する

コンソールまたは IDE からサンプルを実行します。

    ````
    python quickstart.py
    ````

その後 15 秒間、マイクからの音声入力が認識され、コンソール ウィンドウにログが記録されます。

## <a name="next-steps"></a>次の手順

[!INCLUDE [footer](./footer.md)]
