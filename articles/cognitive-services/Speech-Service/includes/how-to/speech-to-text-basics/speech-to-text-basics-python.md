---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: dapine
ms.openlocfilehash: a0746ec73b827756ff47c0dfc010b37ed2b79844
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501432"
---
## <a name="prerequisites"></a>前提条件

この記事では、以下のことを前提としています。

* Azure アカウントと Speech Service のサブスクリプションをお持ちになっています。 アカウントとサブスクリプションをお持ちでない場合は、[Speech Service を無料でお試しください](../../../get-started.md)。

## <a name="install-and-import-the-speech-sdk"></a>Speech SDK をインストールしてインポートする

何らかの操作を行うには、事前に Speech SDK をインストールしておく必要があります。

```Python
pip install azure-cognitiveservices-speech
```

macOS を使用していて、インストールの問題が発生した場合は、まず次のコマンドを実行することが必要な場合があります。

```Python
python3 -m pip install --upgrade pip
```

Speech SDK がインストールされたら、それを次のステートメントを使用して Python プロジェクトにインポートします。

```Python
import azure.cognitiveservices.speech as speechsdk
```

## <a name="create-a-speech-configuration"></a>音声構成を作成する

Speech SDK を使用して Speech Service を呼び出すには、[`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) を作成する必要があります。 このクラスには、キー、関連付けられたリージョン、エンドポイント、ホスト、または認証トークンなど、ご利用のサブスクリプションに関する情報が含まれています。

> [!NOTE]
> 音声認識、音声合成、翻訳、またはインテント認識のどれを実行するのかに関係なく、必ず構成を作成します。

[`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) を初期化するには、次に示すようないくつかの方法があります。

* サブスクリプションの場合: キーと、それに関連付けられたリージョンを渡します。
* エンドポイントの場合: Speech Service エンドポイントを渡します。 キーまたは認証トークンは省略可能です。
* ホストの場合: ホスト アドレスを渡します。 キーまたは認証トークンは省略可能です。
* 認証トークンの場合: 認証トークンと、それに関連付けられたリージョンを渡します。

キーとリージョンを使用して [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) を作成する方法を見てみましょう。 リージョン識別子を確認するには、[リージョンのサポート](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk)に関するページを参照してください。

```Python
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
```

## <a name="initialize-a-recognizer"></a>認識エンジンを初期化する

[`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) を作成したら、次の手順として、[`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python) を初期化します。 [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python) を初期化する場合は、それを自分の `speech_config` に渡す必要があります。 これにより、Speech Service がお客様の要求を検証するために必要な資格情報が提供されます。

ご利用のデバイスの既定のマイクを使用して音声を認識している場合、[`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python) は次のようになります。

```Python
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config)
```

オーディオ入力デバイスを指定する場合は、[`AudioConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python) を作成し、[`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python) を初期化するときに `audio_config` パラメーターを指定する必要があります。

> [!TIP]
> [自分のオーディオ入力デバイスのデバイス ID を取得する方法の詳細](../../../how-to-select-audio-input-devices.md)。

```Python
audio_config = AudioConfig(device_name="<device id>");
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)
```

マイクを使用するのでなくオーディオ ファイルを提供する場合でも、`audio_config` を指定する必要があります。 ただし、`device_name` を指定するのではなく、[`AudioConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python) を作成する場合は、`filename` パラメーターを使用します。

```Python
audio_filename = "whatstheweatherlike.wav"
audio_input = speechsdk.AudioConfig(filename=audio_filename)
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_input)
```

## <a name="recognize-speech"></a>音声を認識する

Speech SDK for Python 用の [認識エンジン](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python) では、音声認識に使用できるいくつかの手法が公開されています。

* 単発の認識 (同期) - ブロッキング (同期) モードで認識を実行します。 1 つの発話が認識された後に、返されます。 1 つの発話の終わりは、終了時の無音状態をリッスンするか、最大 15 秒のオーディオが処理されるまで待機することによって決定されます。 このタスクからは、結果として認識テキストが返されます。
* 単発の認識 (非同期) - 非ブロッキング (非同期) モードで認識を実行します。 これにより、1 つの発話が認識されます。 1 つの発話の終わりは、終了時の無音状態をリッスンするか、最大 15 秒のオーディオが処理されるまで待機することによって決定されます。
* 継続的認識 (同期) - 継続的な認識を同期的に開始します。 クライアントが認識結果を受け取るには `EventSignal` に接続する必要があります。 認識を停止するには、[stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--) を呼び出します。
* 継続的認識 (非同期) - 継続的な認識操作を非同期に開始します。 ユーザーが認識結果を受け取るには、EventSignal に接続する必要があります。 非同期の継続的認識を停止するには、[stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition-async--) を呼び出します。

> [!NOTE]
> [音声認識モードを選択](../../../how-to-choose-recognition-mode.md)する方法の詳細情報。

### <a name="single-shot-recognition"></a>単発の認識

[`recognize_once()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognize-once) を使用した同期的な単発認識の例を次に示します。

```Python
result = speech_recognizer.recognize_once()
```

[`recognize_once_async()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognize-once-async------azure-cognitiveservices-speech-resultfuture) を使用した同期的な単発認識の例を次に示します。

```Python
result = speech_recognizer.recognize_once_async()
```

同期メソッドまたは非同期メソッドのどちらを使用したかにかかわらず、結果を反復処理するコードを記述する必要があります。 このサンプルでは、[`result.reason`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.resultreason?view=azure-python) を評価します。

* 認識結果を出力します: `speechsdk.ResultReason.RecognizedSpeech`
* 認識が一致しない場合は、ユーザーに通知します: `speechsdk.ResultReason.NoMatch `
* エラーが検出された場合は、エラー メッセージを出力します: `speechsdk.ResultReason.Canceled`

```Python
if result.reason == speechsdk.ResultReason.RecognizedSpeech:
    print("Recognized: {}".format(result.text))
elif result.reason == speechsdk.ResultReason.NoMatch:
    print("No speech could be recognized: {}".format(result.no_match_details))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech Recognition canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        print("Error details: {}".format(cancellation_details.error_details))
```

### <a name="continuous-recognition"></a>継続的認識

継続的認識は、単発の認識よりも少し複雑です。 認識結果を取得するには `EventSignal` に接続する必要があります。認識を停止するには、[stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--) または [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition-async--) を呼び出す必要があります。 オーディオ入力ファイルに対して継続的認識を実行する方法の例を次に示します。

入力を定義し、[`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python) を初期化することから始めましょう。

```Python
audio_config = speechsdk.audio.AudioConfig(filename=weatherfilename)
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)
```

次に、音声認識の状態を管理する変数を作成してみましょう。 開始するには、これを `False` に設定します。認識の開始時に、それが間違いなく完了していないと見なすことができるからです。

```Python
done = False
```

次に、`evt` が受信されたときに、継続的認識を停止するコールバックを作成します。 注意すべき点がいくつかあります。

* `evt` が受信されると、`evt` メッセージが出力されます。
* `evt` が受信されたら、認識を停止するために [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--) が呼び出されます。
* 認識状態が `True` に変更されます。

```Python
def stop_cb(evt):
    print('CLOSING on {}'.format(evt))
    speech_recognizer.stop_continuous_recognition()
    nonlocal done
    done = True
```

以下のコード サンプルでは、[`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#start-continuous-recognition--) から送信されたイベントにコールバックを接続する方法を示します。

* [`recognizing`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognizing): 中間的な認識結果を含むイベントのシグナル。
* [`recognized`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognized): 最終的な認識結果を含むイベント (認識の試行が成功したことを示す) のシグナル。
* [`session_started`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-started): 認識セッション (操作) の開始を示すイベントのシグナル。
* [`session_stopped`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-stopped): 認識セッション (操作) の終了を示すイベントのシグナル。
* [`canceled`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#canceled): キャンセルされた認識結果を含むイベント (結果としてキャンセルされた認識の試みまたは直接的なキャンセル要求、あるいは転送またはプロトコルの失敗を示す) のシグナル。

```Python
speech_recognizer.recognizing.connect(lambda evt: print('RECOGNIZING: {}'.format(evt)))
speech_recognizer.recognized.connect(lambda evt: print('RECOGNIZED: {}'.format(evt)))
speech_recognizer.session_started.connect(lambda evt: print('SESSION STARTED: {}'.format(evt)))
speech_recognizer.session_stopped.connect(lambda evt: print('SESSION STOPPED {}'.format(evt)))
speech_recognizer.canceled.connect(lambda evt: print('CANCELED {}'.format(evt)))

speech_recognizer.session_stopped.connect(stop_cb)
speech_recognizer.canceled.connect(stop_cb)
```

すべてが設定されると、[start_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-stopped) を呼び出すことができます。

```Python
speech_recognizer.start_continuous_recognition()
while not done:
    time.sleep(.5)
```

### <a name="dictation-mode"></a>ディクテーション モード

継続的認識を使用する際、対応する "ディクテーションの有効化" 関数を使用することで、ディクテーション処理を有効にすることができます。 このモードでは、音声構成インスタンスが、句読点など文構造の単語の表現を解釈します。 たとえば、"Do you live in town question mark" という発話なら、"Do you live in town?" というテキストとして解釈されます。

ディクテーション モードを有効にするには、[`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) 上で [`enable_dictation()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--) メソッドを使用します。

```Python 
SpeechConfig.enable_dictation()
```

## <a name="change-source-language"></a>ソース言語を変更する

音声認識の一般的なタスクは、入力 (またはソース) 言語を指定することです。 入力言語をドイツ語に変更する場合の方法を見てみましょう。 自分のコード内で、ご利用の SpeechConfig を見つけて、そのすぐ下に次の行を追加します。

```Python
speech_config.speech_recognition_language="de-DE"
```

[`speech_recognition_language`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#speech-recognition-language) は、引数として文字列を取るパラメーターです。 サポートされている[ロケールまたは言語](../../../language-support.md)のリストに任意の値を指定できます。

## <a name="improve-recognition-accuracy"></a>認識の精度を向上させる

Speech SDK を使用して認識の精度を向上させるには、いくつかの方法があります。 フレーズ リストを見てみましょう。 フレーズ リストは、音声データ内で、人物の名前や特定の場所などの既知のフレーズを識別するために使用されます。 フレーズ リストには、単一の単語または完全なフレーズを追加できます。 認識時に、音声にフレーズ全体に対する完全一致が含まれる場合、フレーズ リスト内のエントリが使用されます。 フレーズに対する完全一致が見つからない場合、認識はサポートされません。

> [!IMPORTANT]
> フレーズ リスト機能は英語でのみ使用できます。

フレーズ リストを使用するには、まず [`PhraseListGrammar`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python) オブジェクトを作成します。次に、[`addPhrase`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python#addphrase-phrase--str-) を使用して特定の単語と語句を追加します。

[`PhraseListGrammar`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python) への変更は、次の認識時、または Speech Service への再接続後に有効になります。

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Supercalifragilisticexpialidocious")
```

ご利用の語句リストをクリアする必要がある場合は、次のようにします。 

```Python
phrase_list_grammar.clear()
```

### <a name="other-options-to-improve-recognition-accuracy"></a>認識の精度を向上させるためのその他のオプション

フレーズ リストは、認識の精度を向上させるためのオプションの 1 つにすぎません。 次のこともできます。 

* [Custom Speech を使用して精度を向上させる](../../../how-to-custom-speech.md)
* [テナント モデルを使用して精度を向上させる](../../../tutorial-tenant-model.md)