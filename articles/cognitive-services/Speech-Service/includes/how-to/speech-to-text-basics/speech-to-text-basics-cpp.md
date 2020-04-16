---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: trbye
ms.openlocfilehash: e39486f0daf1c865de3e53372d32a44fdcb8eb99
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399762"
---
## <a name="prerequisites"></a>前提条件

この記事は、Azure アカウントと Speech Service サブスクリプションをお持ちであることを前提としています。 アカウントとサブスクリプションをお持ちでない場合は、[Speech Service を無料でお試しください](../../../get-started.md)。

## <a name="install-the-speech-sdk"></a>Speech SDK のインストール

何らかの操作を行うには、事前に Speech SDK をインストールしておく必要があります。 ご利用のプラットフォームに応じて、次の手順を行います。

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=linux&pivots=programming-language-cpp" target="_blank">Linux <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=macos&pivots=programming-language-cpp" target="_blank">macOS <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=windows&pivots=programming-language-cpp" target="_blank">Windows <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>音声構成を作成する

Speech SDK を使用して Speech Service を呼び出すには、[`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) を作成する必要があります。 このクラスには、キー、関連付けられたリージョン、エンドポイント、ホスト、または認証トークンなど、ご利用のサブスクリプションに関する情報が含まれています。

> [!NOTE]
> 音声認識、音声合成、翻訳、またはインテント認識のどれを実行するのかに関係なく、必ず構成を作成します。

[`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) を初期化するには、次に示すようないくつかの方法があります。

* サブスクリプションの場合: キーと、それに関連付けられたリージョンを渡します。
* エンドポイントの場合: Speech Service エンドポイントを渡します。 キーまたは認証トークンは省略可能です。
* ホストの場合: ホスト アドレスを渡します。 キーまたは認証トークンは省略可能です。
* 認証トークンの場合: 認証トークンと、それに関連付けられたリージョンを渡します。

キーとリージョンを使用して [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) を作成する方法を見てみましょう。 リージョン識別子を確認するには、[リージョンのサポート](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk)に関するページを参照してください。

```cpp
auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-recognizer"></a>認識エンジンを初期化する

[`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) を作成したら、次の手順として、[`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) を初期化します。 [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) を初期化する場合は、それを自分の `speech_config` に渡す必要があります。 これにより、Speech Service がお客様の要求を検証するために必要な資格情報が提供されます。

ご利用のデバイスの既定のマイクを使用して音声を認識している場合、[`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) は次のようになります。

```cpp
auto recognizer = SpeechRecognizer::FromConfig(config);
```

オーディオ入力デバイスを指定する場合は、[`AudioConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig) を作成し、[`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) を初期化するときに `audioConfig` パラメーターを指定する必要があります。

> [!TIP]
> [自分のオーディオ入力デバイスのデバイス ID を取得する方法の詳細](../../../how-to-select-audio-input-devices.md)。

まず、自分の `#include` 定義の後に次の `using namespace` ステートメントを追加します。

```cpp
using namespace Microsoft::CognitiveServices::Speech::Audio;
```

これで、`AudioConfig` オブジェクトを次のように参照できるようになります。

```cpp
auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
auto recognizer = SpeechRecognizer::FromConfig(config, audioConfig);
```

マイクを使用するのでなくオーディオ ファイルを提供する場合でも、`audioConfig` を指定する必要があります。 ただし、`FromDefaultMicrophoneInput` を呼び出すのではなく、[`AudioConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig) を作成する場合は、`FromWavFileOutput` を呼び出して `filename` パラメーターを渡します。

```cpp
auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
```

## <a name="recognize-speech"></a>音声を認識する

Speech SDK for C++ 用の [認識エンジン](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) では、音声認識に使用できるいくつかの手法が公開されています。

* 単発の認識 (非同期) - 非ブロッキング (非同期) モードで認識を実行します。 これにより、1 つの発話が認識されます。 1 つの発話の終わりは、終了時の無音状態をリッスンするか、最大 15 秒のオーディオが処理されるまで待機することによって決定されます。
* 継続的認識 (非同期) - 継続的な認識操作を非同期に開始します。 ユーザーは認識結果を受け取るには、EventSignal に接続する必要があります。 非同期の継続的認識を停止するには、[`StopContinuousRecognitionAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#stopcontinuousrecognitionasync) を呼び出します。

> [!NOTE]
> [音声認識モードを選択](../../../how-to-choose-recognition-mode.md)する方法の詳細情報。

### <a name="single-shot-recognition"></a>単発の認識

[`RecognizeOnceAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync) を使用した非同期の単発認識の例を次に示します。

```cpp
auto result = recognizer->RecognizeOnceAsync().get();
```

結果を処理するコードを記述する必要があります。 このサンプルでは、[`result->Reason`](https://docs.microsoft.com/cpp/cognitive-services/speech/recognitionresult#reason) を評価します。

* 認識結果を出力します: `ResultReason::RecognizedSpeech`
* 認識が一致しない場合は、ユーザーに通知します: `ResultReason::NoMatch`
* エラーが検出された場合は、エラー メッセージを出力します: `ResultReason::Canceled`

```cpp
switch (result->Reason)
{
    case ResultReason::RecognizedSpeech:
        cout << "We recognized: " << result->Text << std::endl;
        break;
    case ResultReason::NoMatch:
        cout << "NOMATCH: Speech could not be recognized." << std::endl;
        break;
    case ResultReason::Canceled:
        {
            auto cancellation = CancellationDetails::FromResult(result);
            cout << "CANCELED: Reason=" << (int)cancellation->Reason << std::endl;
    
            if (cancellation->Reason == CancellationReason::Error) {
                cout << "CANCELED: ErrorCode= " << (int)cancellation->ErrorCode << std::endl;
                cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails << std::endl;
                cout << "CANCELED: Did you update the subscription info?" << std::endl;
            }
        }
        break;
    default:
        break;
}
```

### <a name="continuous-recognition"></a>継続的認識

継続的認識は、単発の認識よりも少し複雑です。 この場合は、認識結果を取得するために、`Recognizing`、`Recognized`、`Canceled` の各イベントをサブスクライブする必要があります。 認識を停止するには、[StopContinuousRecognitionAsync](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#stopcontinuousrecognitionasync) を呼び出す必要があります。 オーディオ入力ファイルに対して継続的認識を実行する方法の例を次に示します。

入力を定義し、[`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) を初期化することから始めましょう。

```cpp
auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
```

次に、音声認識の状態を管理する変数を作成してみましょう。 開始するには、`promise<void>` を宣言します。認識の開始時に、それが間違いなく完了していないと見なすことができるからです。

```cpp
promise<void> recognitionEnd;
```

[`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) から送信されたイベントをサブスクライブします。

* [`Recognizing`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizing): 中間的な認識結果を含むイベントのシグナル。
* [`Recognized`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognized): 最終的な認識結果を含むイベント (認識の試行が成功したことを示す) のシグナル。
* [`SessionStopped`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#sessionstopped): 認識セッション (操作) の終了を示すイベントのシグナル。
* [`Canceled`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#canceled): キャンセルされた認識結果を含むイベント (結果としてキャンセルされた認識の試みまたは直接的なキャンセル要求、あるいは転送またはプロトコルの失敗を示す) のシグナル。

```cpp
recognizer->Recognizing.Connect([](const SpeechRecognitionEventArgs& e)
    {
        cout << "Recognizing:" << e.Result->Text << std::endl;
    });

recognizer->Recognized.Connect([](const SpeechRecognitionEventArgs& e)
    {
        if (e.Result->Reason == ResultReason::RecognizedSpeech)
        {
            cout << "RECOGNIZED: Text=" << e.Result->Text 
                 << " (text could not be translated)" << std::endl;
        }
        else if (e.Result->Reason == ResultReason::NoMatch)
        {
            cout << "NOMATCH: Speech could not be recognized." << std::endl;
        }
    });

recognizer->Canceled.Connect([&recognitionEnd](const SpeechRecognitionCanceledEventArgs& e)
    {
        cout << "CANCELED: Reason=" << (int)e.Reason << std::endl;
        if (e.Reason == CancellationReason::Error)
        {
            cout << "CANCELED: ErrorCode=" << (int)e.ErrorCode << "\n"
                 << "CANCELED: ErrorDetails=" << e.ErrorDetails << "\n"
                 << "CANCELED: Did you update the subscription info?" << std::endl;

            recognitionEnd.set_value(); // Notify to stop recognition.
        }
    });

recognizer->SessionStopped.Connect([&recognitionEnd](const SessionEventArgs& e)
    {
        cout << "Session stopped.";
        recognitionEnd.set_value(); // Notify to stop recognition.
    });
```

すべてが設定されると、[`StopContinuousRecognitionAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#startcontinuousrecognitionasync) を呼び出すことができます。

```cpp
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer->StartContinuousRecognitionAsync().get();

// Waits for recognition end.
recognitionEnd.get_future().get();

// Stops recognition.
recognizer->StopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>ディクテーション モード

継続的認識を使用する際、対応する "ディクテーションの有効化" 関数を使用することで、ディクテーション処理を有効にすることができます。 このモードでは、音声構成インスタンスが、句読点など文構造の単語の表現を解釈します。 たとえば、"Do you live in town question mark" という発話なら、"Do you live in town?" というテキストとして解釈されます。

ディクテーション モードを有効にするには、[`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) 上で [`EnableDictation`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation) メソッドを使用します。

```cpp
config->EnableDictation();
```

## <a name="change-source-language"></a>ソース言語を変更する

音声認識の一般的なタスクは、入力 (またはソース) 言語を指定することです。 入力言語をドイツ語に変更する場合の方法を見てみましょう。 自分のコード内で、ご利用の [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) を見つけて、そのすぐ下に次の行を追加します。

```cpp
config->SetSpeechRecognitionLanguage("fr-FR");
```

[`SetSpeechRecognitionLanguage`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage) は、引数として文字列を取るパラメーターです。 サポートされている[ロケールまたは言語](../../../language-support.md)のリストに任意の値を指定できます。

## <a name="improve-recognition-accuracy"></a>認識の精度を向上させる

Speech SDK を使用して認識の精度を向上させるには、いくつかの方法があります。 フレーズ リストを見てみましょう。 フレーズ リストは、音声データ内で、人物の名前や特定の場所などの既知のフレーズを識別するために使用されます。 フレーズ リストには、単一の単語または完全なフレーズを追加できます。 認識時に、音声にフレーズ全体に対する完全一致が含まれる場合、フレーズ リスト内のエントリが使用されます。 フレーズに対する完全一致が見つからない場合、認識はサポートされません。

> [!IMPORTANT]
> フレーズ リスト機能は英語でのみ使用できます。

フレーズ リストを使用するには、まず [`PhraseListGrammar`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar) オブジェクトを作成します。次に、[`AddPhrase`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar#addphrase) を使用して特定の単語と語句を追加します。

[`PhraseListGrammar`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar) への変更は、次の認識時、または Speech Service への再接続後に有効になります。

```cpp
auto phraseListGrammar = PhraseListGrammar::FromRecognizer(recognizer);
phraseListGrammar->AddPhrase("Supercalifragilisticexpialidocious");
```

ご利用の語句リストをクリアする必要がある場合は、次のようにします。 

```cpp
phraseListGrammar->Clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>認識の精度を向上させるためのその他のオプション

フレーズ リストは、認識の精度を向上させるためのオプションの 1 つにすぎません。 次のこともできます。 

* [Custom Speech を使用して精度を向上させる](../../../how-to-custom-speech.md)
* [テナント モデルを使用して精度を向上させる](../../../tutorial-tenant-model.md)