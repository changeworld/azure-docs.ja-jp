---
title: AudioInputStream の概念
description: AudioInputStream API の機能の概要。
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: fmegen
ms.openlocfilehash: b3e12fbc616c8d67b557102c6094467e119a23f1
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39281907"
---
# <a name="about-the-audio-input-stream-api"></a>オーディオ入力ストリームの API について

**オーディオ入力ストリーム** API では、マイクまたは入力ファイルの API を使用する代わりに、認識エンジンにオーディオ ストリームをストリーミングする方法を提供します。

## <a name="api-overview"></a>API の概要

この API では、`AudioInputStream` (生のオーディオ データ) と `AudioInputStreamFormat` の、2 つのコンポーネントを使用します。

`AudioInputStreamFormat` により、オーディオ データの形式を定義します。 Windows の wave ファイルの標準 `WAVEFORMAT` 構造体に対応するものです。

  - `FormatTag`

    オーディオの形式。 Speech SDK では現在、`format 1` (PCM - リトル エンディアン) のみがサポートされています。

  - `Channels`

    チャンネル数。 現在の音声サービスでは、1 チャンネル (モノラル) のオーディオ マテリアルのみがサポートされています。

  - `SamplesPerSec`

    サンプル レート。 一般的なマイク録音では、1 秒あたり 16,000 サンプルです。

  - `AvgBytesPerSec`

    1 秒あたりの平均バイト数。`SamplesPerSec * Channels * ceil(BitsPerSample, 8)` として計算されます。 1 秒あたりの平均バイト数は、可変ビットレートを使用するオーディオ ストリームの場合は異なる可能性があります。

  - `BlockAlign`

    単一フレームのサイズ。`Channels * ceil(wBitsPerSample, 8)` として計算されます。 パディングが原因で、実際の値がこの値よりも大きくなる可能性があります。

  - `BitsPerSample`

    サンプルごとのビット数。 一般的なオーディオ ストリームでは、16 ビット/サンプル (CD 音質) が使用されています。

`AudioInputStream` 基底クラスは、カスタム ストリーム アダプターによって上書きされます。 このアダプターでは、以下の関数を実装する必要があります。

   - `GetFormat()`

     この関数は、オーディオ ストリームの形式を取得するために呼び出されます。 これにより、AudioInputStreamFormat バッファーへのポインターが取得されます。

   - `Read()`

     この関数は、オーディオ ストリームからデータを取得するために呼び出されます。 1 つ目のパラメーターは、オーディオ データのコピー先のバッファーへのポインターです。 2 つ目のパラメーターは、バッファーのサイズです。 この関数は、バッファーにコピーされたバイト数を返します。 戻り値 `0` は、ストリームの末尾を示します。

   - `Close()`

     この関数は、オーディオ ストリームを閉じるために呼び出されます。

## <a name="usage-examples"></a>使用例

一般に、オーディオ入力ストリームを使用する場合は、次の手順が関係します。

  - オーディオ ストリームの形式を識別します。 この形式は、SDK と音声認識サービスでサポートされている必要があります。 現在、次の構成がサポートされています。

    1 つのオーディオ形式のタグ (PCM)、1 つのチャンネル、16,000 サンプル/秒、32,000 バイト/秒、2 つのブロック配列 (1 つのサンプルのパディングを含む 16 ビット)、16 ビット/サンプル

  - 上に示した仕様に従って、コードが生のオーディオ データを提供できることを確認します。 オーディオ ソース データが、サポートされている形式と一致しない場合、オーディオは、必要な形式にトランスコードする必要があります。

  - カスタム オーディオ入力ストリーム クラスを `AudioInputStream` から派生させます。 `GetFormat()`、`Read()`、および `Close()` の各操作を実装します。 正確な関数シグネチャは言語に依存しますが、そのコードは以下のサンプル コードのようになります。

    ```
     public class ContosoAudioStream : AudioInputStream {
        ContosoConfig config;

        public ContosoAudioStream(const ContosoConfig& config) {
            this.config = config;
        }

        public void GetFormat(AudioInputStreamFormat& format) {
            // returns format data to the caller.
            // e.g. format.FormatTag = config.XXX;
            // ...
        }

        public size_t Read(byte *buffer, size_t size) {
            // returns audio data to the caller.
            // e.g. return read(config.YYY, buffer, size);
        }

        public void Close() {
            // close and cleanup resources.
        }
     };
    ```

  - ご自分のオーディオ入力ストリームを使用してください。

    ```
    var contosoStream = new ContosoAudioStream(contosoConfig);

    var factory = SpeechFactory.FromSubscription(...);
    var recognizer = CreateSpeechRecognizerWithStream(contosoStream);

    // run stream through recognizer
    var result = await recognizer.RecognizeAsync();

    var text = result.GetText();

    // In some languages you need to delete the stream explicitly.
    // delete contosoStream;
    ```

  - 一部の言語では、認識が完了した後に、`contosoStream` を明示的に削除する必要があります。 入力が完全に読み取られる前に AudioStream を解放することはできません。 `StopContinuousRecognitionAsync` と `StopContinuousRecognitionAsync` を使用するシナリオでは、次のサンプルで示す概念が必要になります。

    ```
    var contosoStream = new ContosoAudioStream(contosoConfig);

    var factory = SpeechFactory.FromSubscription(...);
    var recognizer = CreateSpeechRecognizerWithStream(contosoStream);

    // run stream through recognizer
    await recognizer.StartContinuousRecognitionAsync();

    // ERROR: do not delete the contosoStream before ending recognition!
    // delete contosoStream;

    await recognizer.StopContinuousRecognitionAsync();

    // OK: Safe to delete the contosoStream.
    // delete contosoStream;
    ```

## <a name="next-steps"></a>次の手順

* [Speech 試用版サブスクリプションを取得する](https://azure.microsoft.com/try/cognitive-services/)
* [C# で音声を認識する方法を確認する](quickstart-csharp-dotnet-windows.md)
