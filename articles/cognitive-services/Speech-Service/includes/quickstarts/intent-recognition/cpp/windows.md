---
title: クイック スタート:音声、意図、エンティティを認識する、C++ - Speech Service
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
ms.openlocfilehash: 277548c859370a700973d9f60556fe38e94548b0
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280411"
---
## <a name="prerequisites"></a>前提条件

開始する前に、必ず次のことを行ってください。

> [!div class="checklist"]
>
> * [Azure Speech リソースを作成する](../../../../get-started.md)
> * [Language Understanding (LUIS) アプリケーションを作成し、エンドポイント キーを取得する](../../../../quickstarts/create-luis.md)
> * [使用する開発環境を設定する](../../../../quickstarts/setup-platform.md?tabs=windows)
> * [空のサンプル プロジェクトを作成する](../../../../quickstarts/create-project.md?tabs=windows)

## <a name="open-your-project-in-visual-studio"></a>Visual Studio でプロジェクトを開きます。

最初の手順として、ご利用のプロジェクトを Visual Studio で開いていることを確認します。

1. Visual Studio 2019 を起動します。
2. プロジェクトを読み込んで `helloworld.cpp` を開きます。

## <a name="start-with-some-boilerplate-code"></a>定型コードを使用して開始する

このプロジェクトのスケルトンとして機能するコードを追加しましょう。 `recognizeIntent()` という非同期メソッドを作成済みであることを確認します。
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-16,73-81)]

## <a name="create-a-speech-configuration"></a>Speech 構成を作成する

`IntentRecognizer` オブジェクトを初期化するには、LUIS エンドポイント キーとリージョンを使用する構成を作成する必要があります。 このコードを `recognizeIntent()` メソッドに挿入します。

このサンプルでは、`FromSubscription()` メソッドを使用して `SpeechConfig` をビルドします。 使用可能なメソッドの完全な一覧については、[SpeechConfig クラス](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)に関する記事を参照してください。

> [!NOTE]
> 音声意図判定認識にはエンドポイント キーのみが有効となるため、スターター キーや オーサリング キーではなく、LUIS エンドポイント キーを使用することが重要です。 正しいキーを取得する方法については、[LUIS アプリケーションを作成し、エンドポイント キーを取得する](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md)方法に関する記事を参照してください。

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=25)]

## <a name="initialize-an-intentrecognizer"></a>IntentRecognizer を初期化する

ここで、`IntentRecognizer` を作成しましょう。 このコードを Speech 構成のすぐ下にある `recognizeIntent()` メソッドに挿入します。
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=28)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>LanguageUnderstandingModel と Intents を追加する

次に、`LanguageUnderstandingModel` と意図認識エンジンを関連付け、認識させる意図を追加する必要があります。
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=31-34)]

## <a name="recognize-an-intent"></a>意図を認識する

`IntentRecognizer` オブジェクトから、`RecognizeOnceAsync()` メソッドを呼び出します。 認識の対象として 1 つの語句を送信しようとしていること、また、その語句が識別された後で、音声認識を停止しようとしていることが、このメソッドを通じて Speech サービスに伝えられます。
簡素化のため、結果が返されて完了するまで待機します。

using ステートメント内に、このコードを追加します。[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=44)]

## <a name="display-the-recognition-results-or-errors"></a>認識結果 (またはエラー) を表示する

音声サービスによって認識結果が返されたら、それを使用して何らかの操作を行います。 シンプルに保ち、結果をコンソールに出力します。

using ステートメント内の `RecognizeOnceAsync()` の下に、次のコードを追加します。[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=47-72)]

## <a name="check-your-code"></a>コードを確認する

この時点で、コードは次のようになります。  
(このバージョンにはいくつかのコメントを追加してあります) [!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-81)]

## <a name="build-and-run-your-app"></a>アプリをビルドして実行する

これで、アプリをビルドし、Speech サービスを使用する音声認識をテストする準備ができました。

1. **コードをコンパイルする** - Visual Studio のメニュー バーで、 **[ビルド]**  >  **[ソリューションのビルド]** の順に選択します。
2. **アプリを開始する** - メニュー バーで **[デバッグ]**  >  **[デバッグの開始]** の順に選択するか、**F5** キーを押します。
3. **認識を開始する** - 英語で語句を読み上げるように求められます。 音声が Speech Service に送信され、テキストとして文字起こしされて、コンソールに表示されます。

## <a name="next-steps"></a>次の手順

[!INCLUDE [footer](./footer.md)]