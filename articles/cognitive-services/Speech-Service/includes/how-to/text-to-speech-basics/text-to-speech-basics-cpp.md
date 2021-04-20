---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.openlocfilehash: 7b1bc00e54abed70d4cbb769e15ff0f304c62b85
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108953"
---
このクイックスタートでは、Speech SDK を使用してテキスト読み上げ合成を行うための一般的な設計パターンについて説明します。 まずは基本的な構成と合成を行った後、次のようなより高度なカスタム アプリケーション開発の例に進みます。

* インメモリ ストリームとして応答を取得する
* 出力のサンプル レートとビット レートをカスタマイズする
* SSML (音声合成マークアップ言語) を使用して合成要求を送信する
* ニューラル音声を使用する

## <a name="skip-to-samples-on-github"></a>記事をスキップして GitHub 上のサンプルにアクセスする

この記事をスキップしてサンプル コードをご覧になりたい方は、GitHub 上の [C++ クイックスタート サンプル](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/text-to-speech)を参照してください。

## <a name="prerequisites"></a>前提条件

この記事は、Azure アカウントと Speech Service サブスクリプションをお持ちであることを前提としています。 アカウントとサブスクリプションをお持ちでない場合は、[Speech Service を無料でお試しください](../../../overview.md#try-the-speech-service-for-free)。

## <a name="install-the-speech-sdk"></a>Speech SDK のインストール

何らかの操作を行うには、事前に Speech SDK をインストールしておく必要があります。 ご利用のプラットフォームに応じて、次の手順を行います。

* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=linux" target="_blank">Linux </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=macos" target="_blank">macOS </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-cpp&tabs=windows" target="_blank">Windows </a>

## <a name="import-dependencies"></a>依存関係のインポート

この記事の例を実行するには、スクリプトの先頭に次の import および `using` ステートメントを含めます。

```cpp
#include <iostream>
#include <fstream>
#include <string>
#include <speechapi_cxx.h>

using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;
```

## <a name="create-a-speech-configuration"></a>音声構成を作成する

Speech SDK を使用して Speech Service を呼び出すには、[`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) を作成する必要があります。 このクラスには、キー、関連付けられたリージョン、エンドポイント、ホスト、または認証トークンなど、ご利用のサブスクリプションに関する情報が含まれています。

> [!NOTE]
> 音声認識、音声合成、翻訳、またはインテント認識のどれを実行するのかに関係なく、必ず構成を作成します。

[`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) を初期化するには、次に示すようないくつかの方法があります。

* サブスクリプションの場合: キーと、それに関連付けられたリージョンを渡します。
* エンドポイントの場合: Speech Service エンドポイントを渡します。 キーまたは認証トークンは省略可能です。
* ホストの場合: ホスト アドレスを渡します。 キーまたは認証トークンは省略可能です。
* 認証トークンの場合: 認証トークンと、それに関連付けられたリージョンを渡します。

この例では、サブスクリプション キーとリージョンを使用して [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) を作成します。 「[Speech Service を無料で試す](../../../overview.md#try-the-speech-service-for-free)」の手順に従って、これらの資格情報を取得します。 また、この記事の残りの部分で使用する、基本的な定型コードをいくつか作成します。これを変更して、さまざまなカスタマイズを行います。

```cpp
int wmain()
{
    try
    {
        synthesizeSpeech();
    }
    catch (exception e)
    {
        cout << e.what();
    }
    return 0;
}

void synthesizeSpeech()
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
}
```

## <a name="synthesize-speech-to-a-file"></a>音声をファイルに合成する

次に、[`SpeechSynthesizer`](/cpp/cognitive-services/speech/speechsynthesizer) オブジェクトを作成します。これにより、テキストから音声への変換と、スピーカー、ファイル、またはその他の出力ストリームへの出力が実行されます。 [`SpeechSynthesizer`](/cpp/cognitive-services/speech/speechsynthesizer) は、前の手順で作成した [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) オブジェクトと、出力結果の処理方法を指定する [`AudioConfig`](/cpp/cognitive-services/speech/audio-audioconfig) オブジェクトをパラメーターとして受け取ります。

まず、`FromWavFileOutput()` 関数を使用して `.wav` ファイルに出力を自動的に書き込む `AudioConfig` を作成します。

```cpp
void synthesizeSpeech()
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto audioConfig = AudioConfig::FromWavFileOutput("path/to/write/file.wav");
}
```

次に、`config` オブジェクトと `audioConfig` オブジェクトをパラメーターとして渡すことで `SpeechSynthesizer` をインスタンス化します。 こうすることで、音声合成を実行してファイルに書き込むことが、テキスト文字列を使用して `SpeakTextAsync()` を実行するのと同じぐらい簡単になります。

```cpp
void synthesizeSpeech()
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto audioConfig = AudioConfig::FromWavFileOutput("path/to/write/file.wav");
    auto synthesizer = SpeechSynthesizer::FromConfig(config, audioConfig);
    auto result = synthesizer->SpeakTextAsync("A simple test to write to a file.").get();
}
```

このプログラムを実行すると、合成された `.wav` ファイルが、指定した場所に書き込まれます。 以上は最も基本的な使用方法の好例ですが、この次は、カスタム シナリオに対応できるよう、出力をカスタマイズし、出力応答をインメモリ ストリームとして処理する方法について説明します。

## <a name="synthesize-to-speaker-output"></a>スピーカー出力に合成する

場合によっては、合成された音声をスピーカーに直接出力することが必要になる場合があります。 これは、上記の例で `SpeechSynthesizer` を作成するときに、`AudioConfig` パラメーターを省略することで実行できます。 これにより、現在のアクティブな出力デバイスに対して合成が行われます。

```cpp
void synthesizeSpeech()
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto synthesizer = SpeechSynthesizer::FromConfig(config);
    auto result = synthesizer->SpeakTextAsync("Synthesizing directly to speaker output.").get();
}
```

## <a name="get-result-as-an-in-memory-stream"></a>結果をインメモリ ストリームとして取得する

音声アプリケーション開発の多くのシナリオでは、結果として得られたオーディオ データは、ファイルに直接書き込むのではなく、インメモリ ストリームとして必要となるケースがよくあります。 その場合、次のようなカスタム動作を構築できます。

* 結果として得られたバイト配列を、カスタム ダウンストリーム サービス向けのシーク可能なストリームとして抽象化する。
* 結果を他の API またはサービスと統合する。
* オーディオ データの変更やカスタム `.wav` ヘッダーの記述などを行う。

この変更は、前の例から簡単に行うことができます。 まず、`AudioConfig` を削除します。これは、制御を高めるために、この時点から出力動作を手動で管理するからです。 次に、`SpeechSynthesizer` コンストラクターの `AudioConfig` に `NULL` を渡します。

> [!NOTE]
> 前述のスピーカー出力の例のように省略するのではなく、`AudioConfig` に `NULL` を渡した場合、既定ではオーディオは現在のアクティブな出力デバイスで再生されません。

今回は、結果を [`SpeechSynthesisResult`](/cpp/cognitive-services/speech/speechsynthesisresult) 変数に保存します。 `GetAudioData` ゲッターは、出力データの `byte []` を返します。 この `byte []` を手動で操作することも、[`AudioDataStream`](/cpp/cognitive-services/speech/audiodatastream) クラスを使用してインメモリ ストリームを管理することもできます。 この例では、`AudioDataStream.FromResult()` 静的関数を使用して、結果からストリームを取得します。

```cpp
void synthesizeSpeech()
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto synthesizer = SpeechSynthesizer::FromConfig(config, NULL);

    auto result = synthesizer->SpeakTextAsync("Getting the response as an in-memory stream.").get();
    auto stream = AudioDataStream::FromResult(result);
}
```

ここから、結果として得られた `stream` オブジェクトを使用して、任意のカスタム動作を実装できます。

## <a name="customize-audio-format"></a>オーディオ形式をカスタマイズする

次のセクションでは、次のようなオーディオ出力属性をカスタマイズする方法について説明します。

* オーディオ ファイルの種類
* サンプルレート
* ビット深度

オーディオ形式を変更するには、`SpeechConfig` オブジェクトで `SetSpeechSynthesisOutputFormat()` 関数を使用します。 この関数には、[`SpeechSynthesisOutputFormat`](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#speechsynthesisoutputformat) 型の `enum` が必要です。これは、出力形式を選択するために使用します。 使用できる[オーディオ形式の一覧](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#speechsynthesisoutputformat)については、リファレンス ドキュメントを参照してください。

要件に応じて、ファイルの種類ごとにさまざまなオプションがあります。 定義上、`Raw24Khz16BitMonoPcm` のような未加工の形式にはオーディオ ヘッダーが含まれないことに注意してください。 未加工の形式は、ダウンストリームの実装で未加工のビットストリームをデコードできることがわかっている場合か、ビット深度、サンプル レート、チャネル数などに基づいてヘッダーを手動で作成する場合にのみ使用してください。

この例では、`SpeechConfig` オブジェクトに `SpeechSynthesisOutputFormat` を設定することにより、高忠実度の RIFF 形式 `Riff24Khz16BitMonoPcm` を指定します。 前のセクションの例と同様に、[`AudioDataStream`](/cpp/cognitive-services/speech/audiodatastream) を使用して結果のインメモリ ストリームを取得し、それをファイルに書き込みます。

```cpp
void synthesizeSpeech()
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    config->SetSpeechSynthesisOutputFormat(SpeechSynthesisOutputFormat::Riff24Khz16BitMonoPcm);

    auto synthesizer = SpeechSynthesizer::FromConfig(config, NULL);
    auto result = synthesizer->SpeakTextAsync("A simple test to write to a file.").get();

    auto stream = AudioDataStream::FromResult(result);
    stream->SaveToWavFileAsync("path/to/write/file.wav").get();
}
```

プログラムをもう一度実行すると、指定したパスに `.wav` ファイルが書き込まれます。

## <a name="use-ssml-to-customize-speech-characteristics"></a>SSML を使用して音声の特徴をカスタマイズする

音声合成マークアップ言語 (SSML) を使用すると、XML スキーマから要求を送信して、テキスト読み上げ出力のピッチ、発音、読み上げ速度、ボリュームなどを微調整することができます。 このセクションでは音声を変更する例を紹介しますが、より詳細なガイドについては、[SSML の操作方法に関する記事](../../../speech-synthesis-markup.md)を参照してください。

SSML を使用したカスタマイズを開始するには、音声を切り替える単純な変更を加えます。
まず、ルート プロジェクト ディレクトリに SSML 構成用の新しい XML ファイルを作成します (この例では `ssml.xml`)。 ルート要素は常に `<speak>` であり、テキストを `<voice>` 要素でラップすることで、`name` パラメーターを使用して音声を変更できます。 サポートされている **ニューラル** 音声の[全一覧](../../../language-support.md#neural-voices)を参照してください。

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    When you're on the freeway, it's a good idea to use a GPS.
  </voice>
</speak>
```

次に、XML ファイルを参照するように音声合成要求を変更する必要があります。 要求はほとんど同じですが、`SpeakTextAsync()` 関数を使用する代わりに、`SpeakSsmlAsync()` を使用します。 この関数には XML 文字列が必要なので、最初に SSML 構成を文字列として読み込みます。 ここからは、結果のオブジェクトは前の例とまったく同じです。

```cpp
void synthesizeSpeech()
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto synthesizer = SpeechSynthesizer::FromConfig(config, NULL);

    std::ifstream file("./ssml.xml");
    std::string ssml, line;
    while (std::getline(file, line))
    {
        ssml += line;
        ssml.push_back('\n');
    }
    auto result = synthesizer->SpeakSsmlAsync(ssml).get();

    auto stream = AudioDataStream::FromResult(result);
    stream->SaveToWavFileAsync("path/to/write/file.wav").get();
}
```

> [!NOTE]
> SSML を使用せずに音声を変更するには、`SpeechConfig.SetSpeechSynthesisVoiceName("en-US-AriaNeural")` を使用して `SpeechConfig` のプロパティを設定します

## <a name="get-facial-pose-events"></a>表情イベントを取得する

スピーチは、表情のアニメーションを動かす有効な手段となる場合があります。
特定の音素を生成するときの唇、顎、舌の位置など、観察された発話における主要な姿勢を表すために、[口形素](../../../how-to-speech-synthesis-viseme.md)がよく使用されます。
口形素イベントは、Speech SDK でサブスクライブできます。
その後、口形素イベントを適用すれば、スピーチ音声の再生に伴って、キャラクターに顔のアニメーションを付けることができます。
[口形素イベントを取得する方法](../../../how-to-speech-synthesis-viseme.md#get-viseme-events-with-the-speech-sdk)に関するセクションを参照してください。
