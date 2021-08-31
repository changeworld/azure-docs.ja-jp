---
title: C++ Speech SDK で単純な言語パターン マッチングを使用する方法
titleSuffix: Azure Cognitive Services
description: このガイドでは、単純なパターンから意図とエンティティを認識する方法について説明します。
services: cognitive-services
author: chschrae
manager: travisw
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/14/2021
ms.author: chschrae
ms.custom: devx-track-cpp
ms.openlocfilehash: abb89fc460915d60402e6b259a8d34c0c5cdf05c
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2021
ms.locfileid: "114713359"
---
# <a name="how-to-use-simple-language-pattern-matching-with-the-c-speech-sdk"></a>C++ Speech SDK で単純な言語パターン マッチングを使用する方法

Cognitive Services の [Speech SDK](speech-sdk.md) は、**単純な言語パターン マッチング** による **意図認識** を提供する組み込み機能です。 意図とは、ウィンドウを閉じる、チェック ボックスをオンにする、テキストを挿入するなど、ユーザーが行いたいと思っている何らかの操作です。

このガイドでは、Speech SDK を使用して、デバイスのマイクを通したユーザーの発話から意図を抽出する C++ コンソール アプリケーションを開発します。 学習内容は次のとおりです。

> [!div class="checklist"]
>
> - Speech SDK NuGet パッケージを参照する Visual Studio プロジェクトを作成する
> - 音声構成を作成して意図認識エンジンを取得する
> - Speech SDK API を使用して意図とパターンを追加する
> - ファイルから音声を認識する
> - 非同期のイベント ドリブンの継続的な認識を使用する

## <a name="when-should-you-use-this"></a>どのようなときにこれを使用する必要があるか

このサンプル コードは、次の場合に使用します。 
* ユーザーが言ったことの非常に厳密なマッチングにのみ関心がある。 これらのパターンでは、LUIS より積極的にマッチングされます。
* LUIS アプリにアクセスできないが、それでも意図が必要である。 これは SDK 内に埋め込まれているので役立ちます。
* LUIS アプリを作成できない、または作成したくないが、それでも何らかの音声コマンド機能が必要である。

LUIS アプリにアクセスできないけれども、意図が必要な場合、これは SDK 内に埋め込まれているので便利です。


## <a name="prerequisites"></a>前提条件

このガイドを開始する前に、次の項目を用意する必要があります。

- [Cognitive Services Azure リソース](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)または [Unified Speech リソース](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (任意のエディション)。

## <a name="speech-and-simple-patterns"></a>音声と単純なパターン

単純なパターンは Speech SDK の機能であり、Cognitive Services リソースまたは Unified Speech リソースが必要です。

パターンは、その中のどこかにエンティティが含まれるフレーズです。 エンティティは、単語を中かっこで囲むことによって定義されます。 次に例を示します。

```
    Take me to the {floorName}
```

これにより、大文字と小文字が区別される ID "floorName" を持つエンティティが定義されます。

その他のすべての特殊文字と句読点は無視されます。

意図は、IntentRecognizer->AddIntent() API の呼び出しを使用して追加されます。

## <a name="create-a-speech-project-in-visual-studio"></a>Visual Studio での Speech プロジェクトの作成

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

次に、Visual Studio でプロジェクトを開きます。

Visual Studio 2019 を起動します。
プロジェクトを読み込み、helloworld.cpp を開きます。
定型コードから始めます。プロジェクトのスケルトンとして機能するコードを追加しましょう。 recognizeIntent() という非同期メソッドが作成されていることを確認します。

## <a name="open-your-project-in-visual-studio"></a>Visual Studio でプロジェクトを開きます。

次に、Visual Studio でプロジェクトを開きます。

1. Visual Studio 2019 を起動します。
2. プロジェクトを読み込んで `helloworld.cpp` を開きます。

## <a name="start-with-some-boilerplate-code"></a>定型コードを使用して開始する

このプロジェクトのスケルトンとして機能するコードを追加しましょう。

```cpp
    #include <iostream>
    #include <speechapi_cxx.h>

    using namespace Microsoft::CognitiveServices::Speech;
    using namespace Microsoft::CognitiveServices::Speech::Intent;

    int main()
    {
        std::cout << "Hello World!\n";

        auto config = SpeechConfig::FromSubscription("YOUR_SUBSCRIPTION_KEY", "YOUR_SUBSCRIPTION_REGION");
    }
```

## <a name="create-a-speech-configuration"></a>Speech 構成を作成する

`IntentRecognizer` オブジェクトを初期化する前に、Cognitive Services 予測リソース用のキーとリージョンを使用する構成を作成する必要があります。

* `"YOUR_SUBSCRIPTION_KEY"` を、Cognitive Services の実際の予測キーに置き換えます。
* `"YOUR_SUBSCRIPTION_REGION"` を、Cognitive Services リソースの実際のリージョンに置き換えます。

このサンプルでは、`FromSubscription()` メソッドを使用して `SpeechConfig` をビルドします。 使用可能なメソッドの完全な一覧については、[SpeechConfig クラス](/cpp/cognitive-services/speech/speechconfig)に関する記事を参照してください。

## <a name="initialize-an-intentrecognizer"></a>IntentRecognizer を初期化する

次に、`IntentRecognizer` を作成します。 Speech 構成のすぐ下にこのコードを挿入します。

```cpp
    auto intentRecognizer = IntentRecognizer::FromConfig(config);
```

## <a name="add-some-intents"></a>意図を追加する

`AddIntent()` を呼び出すことにより、いくつかのパターンを `IntentRecognizer` と関連付ける必要があります。
階の変更に関する 2 つの意図を同じ ID で追加し、ドアの開閉に関するもう 1 つの意図を別の ID で追加します。

```cpp
    intentRecognizer->AddIntent("Take me to floor {floorName}.", "ChangeFloors");
    intentRecognizer->AddIntent("Go to floor {floorName}.", "ChangeFloors");
    intentRecognizer->AddIntent("{action} the door.", "OpenCloseDoor");
```

> [!NOTE]
> 宣言できるエンティティの数に制限はありませんが、それらは緩くマッチングされます。 "{action} door" のようなフレーズを追加した場合、"door" という単語の前にテキストがあるときは常に一致します。 意図は、エンティティの数に基づいて評価されます。 2 つのパターンが一致する場合は、定義されているエンティティが多いパターンが返されます。

## <a name="recognize-an-intent"></a>意図を認識する

`IntentRecognizer` オブジェクトから、`RecognizeOnceAsync()` メソッドを呼び出します。 このメソッドは、Speech サービスに対して、1 つのフレーズで音声を認識し、フレーズが識別されたら音声の認識を停止するよう要求します。 簡素化のため、結果が返されて完了するまで待機します。

このコードを意図の下に挿入します。

```cpp
    std::cout << "Say something ..." << std::endl;
    auto result = intentRecognizer->RecognizeOnceAsync().get();
```

## <a name="display-the-recognition-results-or-errors"></a>認識結果 (またはエラー) を表示する

Speech サービスから認識結果が返されたら、結果を出力するだけです。

次のコードを `auto result = intentRecognizer->RecognizeOnceAsync().get();` の下に挿入します。

```cpp
auto entities = result->GetEntities();

switch (result->Reason)
{
case ResultReason::RecognizedSpeech:
case ResultReason::RecognizedIntent:
    std::cout << "RECOGNIZED: Text = " << result->Text.c_str() << std::endl;
    std::cout << "  Intent Id = " << result->IntentId.c_str() << std::endl;
    if (entities.find("floorName") != entities.end())
    {
        std::cout << "  Floor name: = " << entities["floorName"].c_str() << std::endl;
    }

    if (entities.find("action") != entities.end())
    {
        std::cout << "  Action: = " << entities["action"].c_str() << std::endl;
    }

    break;
case ResultReason::NoMatch:
{
    auto noMatch = NoMatchDetails::FromResult(result);
    switch (noMatch->Reason)
    {
    case NoMatchReason::NotRecognized:
        std::cout << "NOMATCH: Speech was detected, but not recognized." << std::endl;
        break;
    case NoMatchReason::InitialSilenceTimeout:
        std::cout << "NOMATCH: The start of the audio stream contains only silence, and the service timed out waiting for speech." << std::endl;
        break;
    case NoMatchReason::InitialBabbleTimeout:
        std::cout << "NOMATCH: The start of the audio stream contains only noise, and the service timed out waiting for speech." << std::endl;
        break;
    case NoMatchReason::KeywordNotRecognized:
        std::cout << "NOMATCH: Keyword not recognized" << std::endl;
        break;
    }
    break;
}
case ResultReason::Canceled:
{
    auto cancellation = CancellationDetails::FromResult(result);

    if (!cancellation->ErrorDetails.empty())
    {
        std::cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails.c_str() << std::endl;
        std::cout << "CANCELED: Did you update the subscription info?" << std::endl;
    }
}
default:
    break;
}
```

## <a name="check-your-code"></a>コードを確認する

この時点で、コードは次のようになります。

```cpp
#include <iostream>
#include <speechapi_cxx.h>

using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Intent;

int main()
{
    auto config = SpeechConfig::FromSubscription("YOUR_SUBSCRIPTION_KEY", "YOUR_SUBSCRIPTION_REGION");
    auto intentRecognizer = IntentRecognizer::FromConfig(config);

    intentRecognizer->AddIntent("Take me to floor {floorName}.", "ChangeFloors");
    intentRecognizer->AddIntent("Go to floor {floorName}.", "ChangeFloors");
    intentRecognizer->AddIntent("{action} the door.", "OpenCloseDoor");

    std::cout << "Say something ..." << std::endl;

    auto result = intentRecognizer->RecognizeOnceAsync().get();
    auto entities = result->GetEntities();

    switch (result->Reason)
    {
    case ResultReason::RecognizedSpeech:
    case ResultReason::RecognizedIntent:
        std::cout << "RECOGNIZED: Text = " << result->Text.c_str() << std::endl;
        std::cout << "  Intent Id = " << result->IntentId.c_str() << std::endl;
        if (entities.find("floorName") != entities.end())
        {
            std::cout << "  Floor name: = " << entities["floorName"].c_str() << std::endl;
        }

        if (entities.find("action") != entities.end())
        {
            std::cout << "  Action: = " << entities["action"].c_str() << std::endl;
        }

        break;
    case ResultReason::NoMatch:
    {
        auto noMatch = NoMatchDetails::FromResult(result);
        switch (noMatch->Reason)
        {
        case NoMatchReason::NotRecognized:
            std::cout << "NOMATCH: Speech was detected, but not recognized." << std::endl;
            break;
        case NoMatchReason::InitialSilenceTimeout:
            std::cout << "NOMATCH: The start of the audio stream contains only silence, and the service timed out waiting for speech." << std::endl;
            break;
        case NoMatchReason::InitialBabbleTimeout:
            std::cout << "NOMATCH: The start of the audio stream contains only noise, and the service timed out waiting for speech." << std::endl;
            break;
        case NoMatchReason::KeywordNotRecognized:
            std::cout << "NOMATCH: Keyword not recognized." << std::endl;
            break;
        }
        break;
    }
    case ResultReason::Canceled:
    {
        auto cancellation = CancellationDetails::FromResult(result);

        if (!cancellation->ErrorDetails.empty())
        {
            std::cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails.c_str() << std::endl;
            std::cout << "CANCELED: Did you update the subscription info?" << std::endl;
        }
    }
    default:
        break;
    }
}
```
## <a name="build-and-run-your-app"></a>アプリをビルドして実行する

これで、アプリをビルドし、Speech サービスを使用して音声認識をテストする準備ができました。

1. **コードをコンパイルする** - Visual Studio のメニュー バーで、 **[ビルド]**  >  **[ソリューションのビルド]** の順に選択します。
2. **アプリを起動する** - メニュー バーから **[デバッグ]**  >  **[デバッグの開始]** の順に選択するか、<kbd>F5</kbd> キーを押します。
3. **認識を開始する** - ユーザーに何か話すように要求します。 既定の言語は English (英語) です。 音声が Speech Service に送信され、テキストとして文字起こしされて、コンソールに表示されます。

たとえば、"Take me to floor 7" (7 階に行く) と言った場合、次の出力が表示されます。

```
Say something ...
RECOGNIZED: Text = Take me to floor 7.
  Intent Id = ChangeFloors
  Floor name: = seven
```
