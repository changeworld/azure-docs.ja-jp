---
author: yulin-li
ms.service: cognitive-services
ms.topic: include
ms.date: 07/02/2021
ms.author: yulili
ms.openlocfilehash: 7c64db9a503caff0bb2cda3e7dc576bb3e72884d
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2021
ms.locfileid: "122423409"
---
このクイックスタートでは、Speech SDK を使用してテキスト読み上げ合成を行うための一般的な設計パターンについて説明します。

## <a name="skip-to-samples-on-github"></a>記事をスキップして GitHub 上のサンプルにアクセスする

この記事をスキップしてサンプル コードをご覧になりたい方は、GitHub 上の [Go クイックスタート サンプル](https://github.com/microsoft/cognitive-services-speech-sdk-go/tree/master/samples/synthesizer)を参照してください。

## <a name="prerequisites"></a>前提条件

この記事は、Azure アカウントと Speech Service サブスクリプションをお持ちであることを前提としています。 アカウントとサブスクリプションをお持ちでない場合は、[Speech Service を無料でお試しください](../../../overview.md#try-the-speech-service-for-free)。

## <a name="install-the-speech-sdk"></a>Speech SDK のインストール


なんらかの操作を行うには、事前に [Speech SDK for Go](../../../quickstarts/setup-platform.md?pivots=programming-language-go&tabs=dotnet%252cwindows%252cjre%252cbrowser) をインストールしておく必要があります。

## <a name="text-to-speech-to-speaker"></a>スピーカーへのテキスト読み上げ

音声合成を既定のオーディオ出力デバイスに対して実行するには、次のコード サンプルを使用します。
変数 `subscription` と `region` を、音声キーと場所またはリージョンに置き換えます。
スクリプトを実行すると、入力テキストが既定のスピーカーで読み上げられます。

```go
package main

import (
    "bufio"
    "fmt"
    "os"
    "strings"
    "time"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/common"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func synthesizeStartedHandler(event speech.SpeechSynthesisEventArgs) {
    defer event.Close()
    fmt.Println("Synthesis started.")
}

func synthesizingHandler(event speech.SpeechSynthesisEventArgs) {
    defer event.Close()
    fmt.Printf("Synthesizing, audio chunk size %d.\n", len(event.Result.AudioData))
}

func synthesizedHandler(event speech.SpeechSynthesisEventArgs) {
    defer event.Close()
    fmt.Printf("Synthesized, audio length %d.\n", len(event.Result.AudioData))
}

func cancelledHandler(event speech.SpeechSynthesisEventArgs) {
    defer event.Close()
    fmt.Println("Received a cancellation.")
}

func main() {
    subscription := "<paste-your-speech-key-here>"
    region := "<paste-your-speech-location/region-here>"

    audioConfig, err := audio.NewAudioConfigFromDefaultSpeakerOutput()
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer audioConfig.Close()
    config, err := speech.NewSpeechConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    speechSynthesizer, err := speech.NewSpeechSynthesizerFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer speechSynthesizer.Close()

    speechSynthesizer.SynthesisStarted(synthesizeStartedHandler)
    speechSynthesizer.Synthesizing(synthesizingHandler)
    speechSynthesizer.SynthesisCompleted(synthesizedHandler)
    speechSynthesizer.SynthesisCanceled(cancelledHandler)

    for {
        fmt.Printf("Enter some text that you want to speak, or enter empty text to exit.\n> ")
        text, _ := bufio.NewReader(os.Stdin).ReadString('\n')
        text = strings.TrimSuffix(text, "\n")
        if len(text) == 0 {
            break
        }

        task := speechSynthesizer.SpeakTextAsync(text)
        var outcome speech.SpeechSynthesisOutcome
        select {
        case outcome = <-task:
        case <-time.After(60 * time.Second):
            fmt.Println("Timed out")
            return
        }
        defer outcome.Close()
        if outcome.Error != nil {
            fmt.Println("Got an error: ", outcome.Error)
            return
        }

        if outcome.Result.Reason == common.SynthesizingAudioCompleted {
            fmt.Printf("Speech synthesized to speaker for text [%s].\n", text)
        } else {
            cancellation, _ := speech.NewCancellationDetailsFromSpeechSynthesisResult(outcome.Result)
            fmt.Printf("CANCELED: Reason=%d.\n", cancellation.Reason)

            if cancellation.Reason == common.Error {
                fmt.Printf("CANCELED: ErrorCode=%d\nCANCELED: ErrorDetails=[%s]\nCANCELED: Did you update the subscription info?\n",
                    cancellation.ErrorCode,
                    cancellation.ErrorDetails)
            }
        }
    }
}
```

次のコマンドを実行して、GitHub でホストされるコンポーネントにリンクされる `go.mod` ファイルを作成します。

```shell
go mod init quickstart
go get github.com/Microsoft/cognitive-services-speech-sdk-go
```

次に、コードをビルドして実行します。

```shell
go build
go run quickstart
```

[`SpeechConfig`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go/speech#SpeechConfig) クラスと [`SpeechSynthesizer`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go/speech#SpeechSynthesizer) クラスの詳細については、リファレンス ドキュメントを参照してください。

## <a name="text-to-speech-to-in-memory-stream"></a>メモリ内ストリームへのテキスト読み上げ

音声アプリケーション開発の多くのシナリオでは、結果として得られたオーディオ データは、ファイルに直接書き込むのではなく、インメモリ ストリームとして必要となるケースがよくあります。
その場合、次のようなカスタム動作を構築できます。

* 結果として得られたバイト配列を、カスタム ダウンストリーム サービス向けのシーク可能なストリームとして抽象化する。
* 結果を他の API またはサービスと統合する。
* オーディオ データの変更やカスタム `.wav` ヘッダーの記述などを行う。

この変更は、前の例から簡単に行うことができます。 まず、`AudioConfig` を削除します。これは、制御を高めるために、この時点から出力動作を手動で管理するからです。 次に、`SpeechSynthesizer` コンストラクターの `AudioConfig` に `nil` を渡します。

> [!NOTE]
> 前述のスピーカー出力の例のように省略するのではなく、`AudioConfig` に `nil` を渡した場合、既定ではオーディオは現在のアクティブな出力デバイスで再生されません。

今回は、結果を [`SpeechSynthesisResult`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go/speech#SpeechSynthesisResult) 変数に保存します。
`AudioData` プロパティは、出力データの `[]byte` を返します。 この `[]byte` を手動で操作することも、[`AudioDataStream`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go/speech#AudioDataStream) クラスを使用してインメモリ ストリームを管理することもできます。
この例では、`NewAudioDataStreamFromSpeechSynthesisResult()` 静的関数を使用して、結果からストリームを取得します。

変数 `subscription` と `region` を、音声キーと場所またはリージョンに置き換えます。

```go
package main

import (
    "bufio"
    "fmt"
    "io"
    "os"
    "strings"
    "time"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func main(subscription string, region string) {
    subscription := "<paste-your-speech-key-here>"
    region := "<paste-your-speech-location/region-here>"

    config, err := speech.NewSpeechConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    speechSynthesizer, err := speech.NewSpeechSynthesizerFromConfig(config, nil)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer speechSynthesizer.Close()

    speechSynthesizer.SynthesisStarted(synthesizeStartedHandler)
    speechSynthesizer.Synthesizing(synthesizingHandler)
    speechSynthesizer.SynthesisCompleted(synthesizedHandler)
    speechSynthesizer.SynthesisCanceled(cancelledHandler)

    for {
        fmt.Printf("Enter some text that you want to speak, or enter empty text to exit.\n> ")
        text, _ := bufio.NewReader(os.Stdin).ReadString('\n')
        text = strings.TrimSuffix(text, "\n")
        if len(text) == 0 {
            break
        }

        // StartSpeakingTextAsync sends the result to channel when the synthesis starts.
        task := speechSynthesizer.StartSpeakingTextAsync(text)
        var outcome speech.SpeechSynthesisOutcome
        select {
        case outcome = <-task:
        case <-time.After(60 * time.Second):
            fmt.Println("Timed out")
            return
        }
        defer outcome.Close()
        if outcome.Error != nil {
            fmt.Println("Got an error: ", outcome.Error)
            return
        }

        // in most case we want to streaming receive the audio to lower the latency,
        // we can use AudioDataStream to do so.
        stream, err := speech.NewAudioDataStreamFromSpeechSynthesisResult(outcome.Result)
        defer stream.Close()
        if err != nil {
            fmt.Println("Got an error: ", err)
            return
        }

        var all_audio []byte
        audio_chunk := make([]byte, 2048)
        for {
            n, err := stream.Read(audio_chunk)

            if err == io.EOF {
                break
            }

            all_audio = append(all_audio, audio_chunk[:n]...)
        }

        fmt.Printf("Read [%d] bytes from audio data stream.\n", len(all_audio))
    }
}
```

次のコマンドを実行して、GitHub でホストされるコンポーネントにリンクされる `go.mod` ファイルを作成します。

```shell
go mod init quickstart
go get github.com/Microsoft/cognitive-services-speech-sdk-go
```

次に、コードをビルドして実行します。

```shell
go build
go run quickstart
```

[`SpeechConfig`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go/speech#SpeechConfig) クラスと [`SpeechSynthesizer`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go/speech#SpeechSynthesizer) クラスの詳細については、リファレンス ドキュメントを参照してください。

## <a name="use-ssml-to-customize-speech-characteristics"></a>SSML を使用して音声の特徴をカスタマイズする

音声合成マークアップ言語 (SSML) を使用すると、XML スキーマから要求を送信して、テキスト読み上げ出力のピッチ、発音、読み上げ速度、ボリュームなどを微調整することができます。 このセクションでは音声を変更する例を紹介しますが、より詳細なガイドについては、[SSML の操作方法に関する記事](../../../speech-synthesis-markup.md)を参照してください。

SSML を使用したカスタマイズを開始するには、音声を切り替える単純な変更を加えます。
まず、ルート プロジェクト ディレクトリに SSML 構成用の新しい XML ファイルを作成します (この例では `ssml.xml`)。 ルート要素は常に `<speak>` であり、テキストを `<voice>` 要素でラップすることで、`name` パラメーターを使用して音声を変更できます。 サポートされている **ニューラル** 音声の [全一覧](../../../language-support.md#neural-voices)を参照してください。

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-US-ChristopherNeural">
    When you're on the freeway, it's a good idea to use a GPS.
  </voice>
</speak>
```

次に、XML ファイルを参照するように音声合成要求を変更する必要があります。
要求はほとんど同じですが、`SpeakTextAsync()` 関数を使用する代わりに、`SpeakSsmlAsync()` を使用します。 この関数には XML 文字列が必要なので、最初に SSML 構成を文字列として読み込みます。 ここからは、結果のオブジェクトは前の例とまったく同じです。

> [!NOTE]
> SSML を使用せずに音声を変更するには、`speechConfig.SetSpeechSynthesisVoiceName("en-US-ChristopherNeural")` を使用して `SpeechConfig` のプロパティを設定します

## <a name="get-facial-pose-events"></a>表情イベントを取得する

スピーチは、表情のアニメーションを動かす有効な手段となる場合があります。
特定の音素を生成するときの唇、顎、舌の位置など、観察された発話における主要な姿勢を表すために、[口形素](../../../how-to-speech-synthesis-viseme.md)がよく使用されます。
口形素イベントは、Speech SDK でサブスクライブできます。
その後、口形素イベントを適用すれば、スピーチ音声の再生に伴って、キャラクターに顔のアニメーションを付けることができます。
[口形素イベントを取得する方法](../../../how-to-speech-synthesis-viseme.md#get-viseme-events-with-the-speech-sdk)に関するセクションを参照してください。
