---
title: Speech SDK を使用して音声合成の待機時間を短縮する方法
titleSuffix: Azure Cognitive Services
description: ストリーミング、事前接続など、Speech SDK を使用して音声合成の待機時間を短縮する方法。
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/29/2021
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-set-nineteen
ms.openlocfilehash: 38af8d1ffd422b151bebc2fea42c575f181cc72e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741098"
---
# <a name="lower-speech-synthesis-latency-using-speech-sdk"></a>Speech SDK を使用して音声合成の待機時間を短縮する

> [!NOTE]
> この記事では、Speech SDK 1.17.0 以降を必要とします。

アプリケーションにとって、合成の待機時間は重要です。
この記事では、待機時間を短くし、エンドユーザーに最高のパフォーマンスを提供するためのベスト プラクティスを紹介します。

通常、次のように、`first byte latency` と `finish latency` で待機時間を測定します。

::: zone pivot="programming-language-csharp"

| 待機時間 | 説明 | [SpeechSynthesisResult](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisresult) プロパティ キー |
|-----------|-------------|------------|
| 最初のバイト待機時間 | 合成タスクが開始されてから、オーディオ データの最初のチャンクが受信されるまでの遅延時間を示します。 | SpeechServiceResponse_SynthesisFirstByteLatencyMs |
| 完了までの待機時間 | 合成タスクが開始されてから、合成するオーディオ データ全体が受信されるまでの遅延時間を示します。 | SpeechServiceResponse_SynthesisFinishLatencyMs |

Speech SDK によって、[`SpeechSynthesisResult`](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisresult) のプロパティ コレクションに待機時間が入力されます。 これらの値を次のサンプル コードに示します。

```csharp
var result = await synthesizer.SpeakTextAsync(text);
Console.WriteLine($"first byte latency: \t{result.Properties.GetProperty(PropertyId.SpeechServiceResponse_SynthesisFirstByteLatencyMs)} ms");
Console.WriteLine($"finish latency: \t{result.Properties.GetProperty(PropertyId.SpeechServiceResponse_SynthesisFinishLatencyMs)} ms");
// you can also get the result id, and send to us when you need help for diagnosis
var resultId = result.ResultId;
```

::: zone-end

::: zone pivot="programming-language-cpp"

| 待機時間 | 説明 | [SpeechSynthesisResult](/cpp/cognitive-services/speech/speechsynthesisresult) プロパティ キー |
|-----------|-------------|------------|
| `first byte latency` | 合成が開始されてから最初のオーディオ チャンクが受信されるまでの遅延時間を示します。 | `SpeechServiceResponse_SynthesisFirstByteLatencyMs` |
| `finish latency` | 合成が開始されてから合成するオーディオ全体が受信されるまでの遅延時間を示します。 | `SpeechServiceResponse_SynthesisFinishLatencyMs` |

Speech SDK によって、待機時間が測定され、[`SpeechSynthesisResult`](/cpp/cognitive-services/speech/speechsynthesisresult) のプロパティ バッグに格納されます。 それらを取得するには、次のコードを参照してください。

```cpp
auto result = synthesizer->SpeakTextAsync(text).get();
auto firstByteLatency = std::stoi(result->Properties.GetProperty(PropertyId::SpeechServiceResponse_SynthesisFirstByteLatencyMs));
auto finishedLatency = std::stoi(result->Properties.GetProperty(PropertyId::SpeechServiceResponse_SynthesisFinishLatencyMs));
// you can also get the result id, and send to us when you need help for diagnosis
auto resultId = result->ResultId;
```

::: zone-end

::: zone pivot="programming-language-java"

| 待機時間 | 説明 | [SpeechSynthesisResult](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisresult) プロパティ キー |
|-----------|-------------|------------|
| `first byte latency` | 合成が開始されてから最初のオーディオ チャンクが受信されるまでの遅延時間を示します。 | `SpeechServiceResponse_SynthesisFirstByteLatencyMs` |
| `finish latency` | 合成が開始されてから合成するオーディオ全体が受信されるまでの遅延時間を示します。 | `SpeechServiceResponse_SynthesisFinishLatencyMs` |

Speech SDK によって、待機時間が測定され、[`SpeechSynthesisResult`](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisresult) のプロパティ バッグに格納されます。 それらを取得するには、次のコードを参照してください。

```java
SpeechSynthesisResult result = synthesizer.SpeakTextAsync(text).get();
System.out.println("first byte latency: \t" + result.getProperties().getProperty(PropertyId.SpeechServiceResponse_SynthesisFirstByteLatencyMs) + " ms.");
System.out.println("finish latency: \t" + result.getProperties().getProperty(PropertyId.SpeechServiceResponse_SynthesisFinishLatencyMs) + " ms.");
// you can also get the result id, and send to us when you need help for diagnosis
String resultId = result.getResultId();
```

::: zone-end


::: zone pivot="programming-language-python"

| 待機時間 | 説明 | [SpeechSynthesisResult](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisresult) プロパティ キー |
|-----------|-------------|------------|
| `first byte latency` | 合成が開始されてから最初のオーディオ チャンクが受信されるまでの遅延時間を示します。 | `SpeechServiceResponse_SynthesisFirstByteLatencyMs` |
| `finish latency` | 合成が開始されてから合成するオーディオ全体が受信されるまでの遅延時間を示します。 | `SpeechServiceResponse_SynthesisFinishLatencyMs` |

Speech SDK によって、待機時間が測定され、[`SpeechSynthesisResult`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisresult) のプロパティ バッグに格納されます。 それらを取得するには、次のコードを参照してください。

```python
result = synthesizer.speak_text_async(text).get()
first_byte_latency = int(result.properties.get_property(speechsdk.PropertyId.SpeechServiceResponse_SynthesisFirstByteLatencyMs))
finished_latency = int(result.properties.get_property(speechsdk.PropertyId.SpeechServiceResponse_SynthesisFinishLatencyMs))
# you can also get the result id, and send to us when you need help for diagnosis
result_id = result.result_id
```

::: zone-end

::: zone pivot="programming-language-objectivec"

| 待機時間 | 説明 | [SPXSpeechSynthesisResult](/objectivec/cognitive-services/speech/spxspeechsynthesisresult) プロパティ キー |
|-----------|-------------|------------|
| `first byte latency` | 合成が開始されてから最初のオーディオ チャンクが受信されるまでの遅延時間を示します。 | `SPXSpeechServiceResponseSynthesisFirstByteLatencyMs` |
| `finish latency` | 合成が開始されてから合成するオーディオ全体が受信されるまでの遅延時間を示します。 | `SPXSpeechServiceResponseSynthesisFinishLatencyMs` |

Speech SDK によって、待機時間が測定され、[`SPXSpeechSynthesisResult`](/objectivec/cognitive-services/speech/spxspeechsynthesisresult) のプロパティ バッグに格納されます。 それらを取得するには、次のコードを参照してください。

```Objective-C
SPXSpeechSynthesisResult *speechResult = [speechSynthesizer speakText:text];
int firstByteLatency = [intString [speechResult.properties getPropertyById:SPXSpeechServiceResponseSynthesisFirstByteLatencyMs]];
int finishedLatency = [intString [speechResult.properties getPropertyById:SPXSpeechServiceResponseSynthesisFinishLatencyMs]];
// you can also get the result id, and send to us when you need help for diagnosis
NSString *resultId = result.resultId;
```

::: zone-end

ほとんどの場合、最初のバイト待機時間は、終了待機時間よりもはるかに短くなります。
最初のバイト待機時間はテキストの長さとは無関係ですが、完了までの待機時間はテキストの長さによって増減します。

ユーザーが経験する待機時間 (ユーザーがサウンドを聞くまでの待機時間) を最小限に抑える、つまり音声合成サービスの 1 つのネットワーク ルート トリップ時間に最初のオーディオ チャンク待機時間をプラスした時間に抑えることができれば理想的です。

## <a name="streaming"></a>ストリーミング

待機時間を短縮するうえで、ストリーミングが重要な鍵を握っています。
クライアント コードでは、最初のオーディオ チャンクを受信したときに再生を開始できます。
サービス シナリオでは、オーディオ全体を待機するのではなく、オーディオ チャンクをすぐにクライアントに転送できます。

::: zone pivot="programming-language-csharp"

Speech SDK の [`PullAudioOutputStream`](/dotnet/api/microsoft.cognitiveservices.speech.audio.pullaudiooutputstream)、[`PushAudioOutputStream`](/dotnet/api/microsoft.cognitiveservices.speech.audio.pushaudiooutputstream)、[`Synthesizing` イベント](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer.synthesizing)、[`AudioDateStream`](/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream) を使用して、ストリーミングを有効にすることができます。

例として、`AudioDateStream` を取り上げます。

```csharp
using (var synthesizer = new SpeechSynthesizer(config, null as AudioConfig))
{
    using (var result = await synthesizer.StartSpeakingTextAsync(text))
    {
        using (var audioDataStream = AudioDataStream.FromResult(result))
        {
            byte[] buffer = new byte[16000];
            uint filledSize = 0;
            while ((filledSize = audioDataStream.ReadData(buffer)) > 0)
            {
                Console.WriteLine($"{filledSize} bytes received.");
            }
        }
    }
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

Speech SDK の [`PullAudioOutputStream`](/cpp/cognitive-services/speech/audio-pullaudiooutputstream)、[`PushAudioOutputStream`](/cpp/cognitive-services/speech/audio-pushaudiooutputstream)、[`Synthesizing` イベント](/cpp/cognitive-services/speech/speechsynthesizer#synthesizing)、[`AudioDateStream`](/cpp/cognitive-services/speech/audiodatastream) を使用して、ストリーミングを有効にすることができます。

例として、`AudioDateStream` を取り上げます。

```cpp
auto synthesizer = SpeechSynthesizer::FromConfig(config, nullptr);
auto result = synthesizer->SpeakTextAsync(text).get();
auto audioDataStream = AudioDataStream::FromResult(result);
uint8_t buffer[16000];
uint32_t filledSize = 0;
while ((filledSize = audioDataStream->ReadData(buffer, sizeof(buffer))) > 0)
{
    cout << filledSize << " bytes received." << endl;
}
```

::: zone-end

::: zone pivot="programming-language-java"

Speech SDK の [`PullAudioOutputStream`](/java/api/com.microsoft.cognitiveservices.speech.audio.pullaudiooutputstream)、[`PushAudioOutputStream`](/java/api/com.microsoft.cognitiveservices.speech.audio.pushaudiooutputstream)、[`Synthesizing` イベント](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer.synthesizing#com_microsoft_cognitiveservices_speech_SpeechSynthesizer_Synthesizing)、[`AudioDateStream`](/java/api/com.microsoft.cognitiveservices.speech.audiodatastream) を使用して、ストリーミングを有効にすることができます。

例として、`AudioDateStream` を取り上げます。

```java
SpeechSynthesizer synthesizer = new SpeechSynthesizer(config, null);
SpeechSynthesisResult result = synthesizer.StartSpeakingTextAsync(text).get();
AudioDataStream audioDataStream = AudioDataStream.fromResult(result);
byte[] buffer = new byte[16000];
long filledSize = audioDataStream.readData(buffer);
while (filledSize > 0) {
    System.out.println(filledSize + " bytes received.");
    filledSize = audioDataStream.readData(buffer);
}
```

::: zone-end

::: zone pivot="programming-language-python"

Speech SDK の [`PullAudioOutputStream`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.pullaudiooutputstream)、[`PushAudioOutputStream`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.pushaudiooutputstream)、[`Synthesizing` イベント](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer#synthesizing)、[`AudioDateStream`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream) を使用して、ストリーミングを有効にすることができます。

例として、`AudioDateStream` を取り上げます。

```python
speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=None)
result = speech_synthesizer.start_speaking_text_async(text).get()
audio_data_stream = speechsdk.AudioDataStream(result)
audio_buffer = bytes(16000)
filled_size = audio_data_stream.read_data(audio_buffer)
while filled_size > 0:
    print("{} bytes received.".format(filled_size))
    filled_size = audio_data_stream.read_data(audio_buffer)
```

::: zone-end

::: zone pivot="programming-language-objectivec"

Speech SDK の [`SPXPullAudioOutputStream`](/objectivec/cognitive-services/speech/spxpullaudiooutputstream)、[`SPXPushAudioOutputStream`](/objectivec/cognitive-services/speech/spxpushaudiooutputstream)、[`Synthesizing` イベント](/objectivec/cognitive-services/speech/spxspeechsynthesizer#addsynthesizingeventhandler)、[`SPXAudioDataStream`](/objectivec/cognitive-services/speech/spxaudiodatastream) を使用して、ストリーミングを有効にすることができます。

例として、`AudioDateStream` を取り上げます。

```Objective-C
SPXSpeechSynthesizer *synthesizer = [[SPXSpeechSynthesizer alloc] initWithSpeechConfiguration:speechConfig audioConfiguration:nil];
SPXSpeechSynthesisResult *speechResult = [synthesizer startSpeakingText:inputText];
SPXAudioDataStream *stream = [[SPXAudioDataStream alloc] initFromSynthesisResult:speechResult];
NSMutableData* data = [[NSMutableData alloc]initWithCapacity:16000];
while ([stream readData:data length:16000] > 0) {
    // Read data here
}
```

::: zone-end

## <a name="pre-connect-and-reuse-speechsynthesizer"></a>SpeechSynthesizer の事前接続と再利用

Speech SDK は WebSocket を使用してサービスと通信します。
ネットワーク待機時間は 1 つのルート トリップ時間 (RTT) に抑えることが理想的です。
接続が新たに確立された場合、ネットワーク待機時間に、接続を確立するための時間が余分にかかります。
WebSocket 接続を確立するには、TCP ハンドシェイク、SSL ハンドシェイク、HTTP 接続、プロトコルのアップグレードが必要で、これにより遅延が生じます。
接続の待機時間を回避するために、`SpeechSynthesizer` の事前接続と再利用をお勧めします。

### <a name="pre-connect"></a>接続前

事前に接続するには、接続が間もなく必要になると思われる時点で、Speech サービスへの接続を確立します。 たとえば、クライアントで音声ボットを構築している場合は、ユーザーが話し始めるときに音声合成サービスに事前に接続し、ボットの返信テキストの準備ができたタイミングで `SpeakTextAsync` を呼び出すことができます。

::: zone pivot="programming-language-csharp"

```csharp
using (var synthesizer = new SpeechSynthesizer(uspConfig, null as AudioConfig))
{
    using (var connection = Connection.FromSpeechSynthesizer(synthesizer))
    {
        connection.Open(true);
    }
    await synthesizer.SpeakTextAsync(text);
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto synthesizer = SpeechSynthesizer::FromConfig(config, nullptr);
auto connection = Connection::FromSpeechSynthesizer(synthesizer);
connection->Open(true);
```

::: zone-end

::: zone pivot="programming-language-java"

```java
SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, (AudioConfig) null);
Connection connection = Connection.fromSpeechSynthesizer(synthesizer);
connection.openConnection(true);
```

::: zone-end

::: zone pivot="programming-language-python"

```python
synthesizer = speechsdk.SpeechSynthesizer(config, None)
connection = speechsdk.Connection.from_speech_synthesizer(synthesizer)
connection.open(True)
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXSpeechSynthesizer* synthesizer = [[SPXSpeechSynthesizer alloc]initWithSpeechConfiguration:self.speechConfig audioConfiguration:nil];
SPXConnection* connection = [[SPXConnection alloc]initFromSpeechSynthesizer:synthesizer];
[connection open:true];
```

::: zone-end

> [!NOTE]
> 合成テキストが使用可能な場合は、`SpeakTextAsync` を呼び出してオーディオを合成します。 SDK は接続を処理します。

### <a name="reuse-speechsynthesizer"></a>SpeechSynthesizer の再利用

接続の待機時間を短縮するもう 1 つの方法は、`SpeechSynthesizer` を再利用することです。そうすることで、各合成に新しい `SpeechSynthesizer` を作成する必要がなくなります。
サービス シナリオでは、オブジェクト プールを使用することをお勧めします。[C#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs) と [Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechSynthesisScenarioSamples.java) のサンプル コードを参照してください。


## <a name="transmit-compressed-audio-over-the-network"></a>圧縮したオーディオをネットワーク経由で送信する

ネットワークが不安定な場合、または帯域幅が限られている場合は、ペイロードのサイズも待機時間に影響します。
一方、圧縮オーディオ形式は、ユーザーのネットワーク帯域幅を節約するのに役立ちます。これは、モバイル ユーザーにとって特に有益です。

`opus`、`webm`、`mp3`、`silk` などさまざまな圧縮形式がサポートされています。[SpeechSynthesisOutputFormat](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#speechsynthesisoutputformat) の完全なリストを参照してください。
たとえば、`Riff24Khz16BitMonoPcm` 形式のビットレートは 384 kbps ですが、`Audio24Khz48KBitRateMonoMp3` では 48 kbps です。
`pcm` 出力形式が設定され、`GStreamer` が正しくインストールされている場合、Microsoft Speech SDK では転送に圧縮形式が自動的に使用されます。
Speech SDK を使用するために `GStreamer` をインストールして構成するには、[こちらの手順](how-to-use-codec-compressed-audio-input-streams.md)を参照してください。

## <a name="others-tips"></a>その他のヒント

### <a name="cache-crl-files"></a>CRL ファイルをキャッシュする

Speech SDK では、証明書を確認するために CRL ファイルが使用されます。
期限切れになるまで、CRL ファイルをキャッシュすることで、CRL ファイルを毎回ダウンロードせずに済みます。
詳細については、[Linux 用に OpenSSL を構成する方法](how-to-configure-openssl-linux.md#certificate-revocation-checks)に関するページを参照してください。

### <a name="use-latest-speech-sdk"></a>最新の Speech SDK を使用する

Speech SDK のパフォーマンスが向上し続けているため、アプリケーションで最新の Speech SDK を使用してください。
たとえば、[1.16.0](releasenotes.md#speech-sdk-1160-2021-march-release) の `TCP_NODELAY` 設定の問題を修正しました。これにより、1 つ分の余分なルート トリップ時間が短縮されます。

## <a name="load-test-guideline"></a>ロード テストのガイドライン

ロード テストを使用して、音声合成サービスの容量と待機時間をテストすることができます。
ガイドラインを次に示します。

 - 音声合成サービスには自動スケール機能がありますが、スケールアウトに時間がかかります。同時実行の数が短時間で増加した場合、クライアントの待機時間が長くなったり、`429` エラー コードが返されたり (要求が多すぎます) する可能性があります。 そのため、ロード テストでは、同時実行を徐々に増やすことをお勧めします。 詳細については、[こちらの記事を参照してください](speech-services-quotas-and-limits.md#general-best-practices-to-mitigate-throttling-during-autoscaling)。特に、[ワークロード パターンのこの例](speech-services-quotas-and-limits.md#example-of-a-workload-pattern-best-practice)をご確認ください。
 - オブジェクト プールを使用したサンプル ([C#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs) および [Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechSynthesisScenarioSamples.java)) を、ロード テストや待機時間数の取得に利用できます。 サンプルのテストのターン数と同時実行を、目的の同時実行に合わせて変更できます。
 - このサービスには実際のトラフィックに基づくクォータ制限があるため、実際のトラフィックよりも同時実行の数を多くしてロード テストを実行する場合は、テストの前に接続しておきます。

## <a name="next-steps"></a>次のステップ

* GitHub 上の[サンプル](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master)を参照してください
