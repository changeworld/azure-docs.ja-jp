---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 09/15/2020
ms.author: trbye
ms.openlocfilehash: f87db54b43f0ece49b940521bea34737da676a31
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2020
ms.locfileid: "96912376"
---
Speech Service の中核となる機能の 1 つは、人間の音声を認識して文字起こしをする機能です (多くの場合、音声テキスト変換と呼ばれます)。 このクイックスタートでは、アプリや製品で Speech SDK を使用し、高品質の音声テキスト変換を実行する方法について説明します。

## <a name="skip-to-samples-on-github"></a>記事をスキップして GitHub 上のサンプルにアクセスする

この記事をスキップしてサンプル コードをご覧になりたい方は、GitHub 上の [Go クイックスタート サンプル](https://github.com/microsoft/cognitive-services-speech-sdk-go/tree/master/samples/recognizer)を参照してください。

## <a name="prerequisites"></a>前提条件

この記事は、Azure アカウントと Speech Service サブスクリプションをお持ちであることを前提としています。 アカウントとサブスクリプションをお持ちでない場合は、[Speech Service を無料でお試しください](../../../overview.md#try-the-speech-service-for-free)。

## <a name="install-the-speech-sdk"></a>Speech SDK のインストール

なんらかの操作を行うには、事前に [Speech SDK for Go](../../../quickstarts/setup-platform.md?pivots=programming-language-go&tabs=dotnet%252cwindows%252cjre%252cbrowser) をインストールしておく必要があります。

## <a name="speech-to-text-from-microphone"></a>マイクからの音声テキスト変換

既定のデバイスのマイクから音声認識を実行するには、以下のコード サンプルを使用します。 `subscription` 変数と `region` 変数は、ご利用のサブスクリプション キーとリージョン キーに置き換えてください。 スクリプトを実行すると、既定のマイクで認識セッションが開始されてテキストが出力されます。

```go
package main

import (
    "bufio"
    "fmt"
    "os"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func sessionStartedHandler(event speech.SessionEventArgs) {
    defer event.Close()
    fmt.Println("Session Started (ID=", event.SessionID, ")")
}

func sessionStoppedHandler(event speech.SessionEventArgs) {
    defer event.Close()
    fmt.Println("Session Stopped (ID=", event.SessionID, ")")
}

func recognizingHandler(event speech.SpeechRecognitionEventArgs) {
    defer event.Close()
    fmt.Println("Recognizing:", event.Result.Text)
}

func recognizedHandler(event speech.SpeechRecognitionEventArgs) {
    defer event.Close()
    fmt.Println("Recognized:", event.Result.Text)
}

func cancelledHandler(event speech.SpeechRecognitionCanceledEventArgs) {
    defer event.Close()
    fmt.Println("Received a cancellation: ", event.ErrorDetails)
}

func main() {
    subscription :=  "YOUR_SUBSCRIPTION_KEY"
    region := "YOUR_SUBSCRIPTIONKEY_REGION"

    audioConfig, err := audio.NewAudioConfigFromDefaultMicrophoneInput()
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
    speechRecognizer, err := speech.NewSpeechRecognizerFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer speechRecognizer.Close()
    speechRecognizer.SessionStarted(sessionStartedHandler)
    speechRecognizer.SessionStopped(sessionStoppedHandler)
    speechRecognizer.Recognizing(recognizingHandler)
    speechRecognizer.Recognized(recognizedHandler)
    speechRecognizer.Canceled(cancelledHandler)
    speechRecognizer.StartContinuousRecognitionAsync()
    defer speechRecognizer.StopContinuousRecognitionAsync()
    bufio.NewReader(os.Stdin).ReadBytes('\n')
}
```

[`SpeechConfig`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go@v1.14.0/speech#SpeechConfig) クラスと [`SpeechRecognizer`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go@v1.14.0/speech#SpeechRecognizer) クラスの詳細については、リファレンス ドキュメントを参照してください。

## <a name="speech-to-text-from-audio-file"></a>オーディオ ファイルからの音声テキスト変換

オーディオ ファイルから音声認識を実行するには、以下のサンプルを使用します。 `subscription` 変数と `region` 変数は、ご利用のサブスクリプション キーとリージョン キーに置き換えてください。 加えて、変数 `file` を .wav ファイルのパスに置き換えます。 スクリプトを実行すると、ファイルからの音声が認識され、結果がテキストとして出力されます。

```go
package main

import (
    "fmt"
    "time"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func main() {
    subscription :=  "YOUR_SUBSCRIPTION_KEY"
    region := "YOUR_SUBSCRIPTIONKEY_REGION"
    file := "path/to/file.wav"

    audioConfig, err := audio.NewAudioConfigFromWavFileInput(file)
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
    speechRecognizer, err := speech.NewSpeechRecognizerFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer speechRecognizer.Close()
    speechRecognizer.SessionStarted(func(event speech.SessionEventArgs) {
        defer event.Close()
        fmt.Println("Session Started (ID=", event.SessionID, ")")
    })
    speechRecognizer.SessionStopped(func(event speech.SessionEventArgs) {
        defer event.Close()
        fmt.Println("Session Stopped (ID=", event.SessionID, ")")
    })

    task := speechRecognizer.RecognizeOnceAsync()
    var outcome speech.SpeechRecognitionOutcome
    select {
    case outcome = <-task:
    case <-time.After(5 * time.Second):
        fmt.Println("Timed out")
        return
    }
    defer outcome.Close()
    if outcome.Error != nil {
        fmt.Println("Got an error: ", outcome.Error)
    }
    fmt.Println("Got a recognition!")
    fmt.Println(outcome.Result.Text)
}
```

[`SpeechConfig`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go@v1.14.0/speech#SpeechConfig) クラスと [`SpeechRecognizer`](https://pkg.go.dev/github.com/Microsoft/cognitive-services-speech-sdk-go@v1.14.0/speech#SpeechRecognizer) クラスの詳細については、リファレンス ドキュメントを参照してください。