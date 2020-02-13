---
title: バッチ文字起こしの使用方法 - 音声サービス
titleSuffix: Azure Cognitive Services
description: バッチ文字起こしは、Azure BLOB などのストレージにある大量の音声を文字起こしする場合に理想的です。 専用の REST API を使用すると、Shared Access Signatures (SAS) URI でオーディオ ファイルを示して、非同期に文字起こしを受け取ることができます。
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: panosper
ms.openlocfilehash: 8a53f1cfbde2f518848e7ef1104bf41ba4996961
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2020
ms.locfileid: "76936404"
---
# <a name="how-to-use-batch-transcription"></a>バッチ文字起こしの使用方法

バッチ文字起こしは、ストレージ内の大量のオーディオを文字起こしする場合に最適です。 専用の REST API を使用すると、Shared Access Signatures (SAS) URI でオーディオ ファイルを示して、非同期に文字起こしの結果を受け取ることができます。

API によって、音声からテキストへの非同期文字起こしと他の機能が提供されます。 REST API を使用すると、以下を行うためのメソッドを公開できます。

- バッチ処理要求を作成する
- 状態を照会する
- 文字起こしの結果をダウンロードする
- サービスから文字起こしの情報を削除する

詳細な API は、[Swagger のドキュメント](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A)の見出し `Custom Speech transcriptions` の下にあります。

バッチ文字起こしジョブは、ベスト エフォート ベースでスケジュールされます。 現時点では、ジョブがいつ実行状態になるかについて予測できません。 通常のシステム負荷では、この処理は数分以内に行われます。 いったん実行状態になると、実際の文字起こしはリアルタイムのオーディオより速く処理されます。

使用しやすい API のほかに、カスタム エンドポイントをデプロイする必要はなく、監視する必要のある同時実行要件もありません。

## <a name="prerequisites"></a>前提条件

### <a name="subscription-key"></a>サブスクリプション キー

Speech Service の他の機能と同様に、[使用開始ガイド](get-started.md)に従って [Azure portal](https://portal.azure.com) でサブスクリプション キーを作成します。

>[!NOTE]
> バッチ文字起こしを使用するには、音声サービスの Standard サブスクリプション (S0) が必要です。 Free サブスクリプション キー (F0) は機能しません。 詳細については、[価格と制限](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)に関するページを参照してください。

### <a name="custom-models"></a>カスタム モデル

音響モデルまたは言語モデルをカスタマイズする場合は、[音響モデルのカスタマイズ](how-to-customize-acoustic-models.md)および[カスタマイズ言語モデルの設計](how-to-customize-language-model.md)に関するページの手順に従ってください。 作成されたモデルをバッチ文字起こしで使用するには、モデル ID が必要です。 モデルの詳細を調べると、モデル ID を取得できます。 デプロイされたカスタム エンドポイントは、バッチ文字起こしサービスには必要ありません。

## <a name="the-batch-transcription-api"></a>Batch 文字起こし API

### <a name="supported-formats"></a>サポートされるフォーマット

Batch 文字起こし API では、次の形式がサポートされています。

| Format | コーデック | Bitrate | サンプル レート |
|--------|-------|---------|-------------|
| WAV | PCM 0 | 16 ビット | 8 kHz または 16 kHz、モノラルまたはステレオ |
| MP3 | PCM 0 | 16 ビット | 8 kHz または 16 kHz、モノラルまたはステレオ |
| OGG | OPUS | 16 ビット | 8 kHz または 16 kHz、モノラルまたはステレオ |

ステレオ オーディオ ストリームの場合、文字起こし中に左チャンネルと右チャンネルが分離されます。 各チャネルに対して、JSON 結果ファイルが作成されます。 発話ごとに生成されるタイムスタンプにより、開発者は時間順の最終文字起こしを作成できます。

### <a name="configuration"></a>構成

構成パラメーターは JSON として提供されます。

```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to use, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "ProfanityFilterMode": "None | Removed | Tags | Masked",
    "PunctuationMode": "None | Dictated | Automatic | DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True | False",
    "AddSentiment" : "True | False",
    "AddDiarization" : "True | False",
    "TranscriptionResultsContainerUrl" : "<service SAS URI to Azure container to store results into (write permission required)>"
  }
}
```

### <a name="configuration-properties"></a>構成プロパティ

次の省略可能なプロパティを使用して、文字起こしを構成します。

| パラメーター | 説明 |
|-----------|-------------|
| `ProfanityFilterMode` | 認識結果内の不適切な表現をどう扱うかを指定します。 指定できる値は、`None` (不適切な表現のフィルターを無効にする)、`Masked` (不適切な表現をアスタリスクに置き換える)、`Removed` (すべての不適切な表現を結果から除去する) または `Tags` ("不適切な表現" のタグを追加する) です。 既定の設定は `Masked` です。 |
| `PunctuationMode` | 認識結果内の句読点をどう扱うかを指定します。 指定できる値は、`None` (句読点を無効にする)、`Dictated` (明示的な句読点)、`Automatic` (デコーダーで句読点を処理する)、`DictatedAndAutomatic` (指定された句読点、または自動) です。 |
| `AddWordLevelTimestamps` | 単語レベルのタイムスタンプを出力に追加するかどうかを指定します。 `true` を指定すると単語レベルのタイムスタンプが有効になり、`false` (既定値) を指定すると無効になります。 |
| `AddSentiment` | センチメントを発話に追加するかどうかを指定します。 `true` を指定すると発話ごとのセンチメントが有効になり、`false` (既定値) を指定すると無効になります。 |
| `AddDiarization` | 2 つの音声を含むモノラル チャネルであることが予測される入力に対してダイアライゼーション分析を実行する必要があることを指定します。 指定できる値は、ダイアライゼーションを有効にする `true` と、それを無効にする `false` (既定値) です。 さらに、`AddWordLevelTimestamps` を true に設定する必要があります。|
|`TranscriptionResultsContainerUrl`|Azure の書き込み可能なコンテナーに対する[サービス SAS](../../storage/common/storage-sas-overview.md) のオプションの URL。 結果はこのコンテナーに格納されます。

### <a name="storage"></a>ストレージ

Batch 文字起こしでは、オーディオの読み取りや、文字起こしのストレージへの書き込みに [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) をサポートしています。

## <a name="the-batch-transcription-result"></a>バッチ文字起こしの結果

モノラル入力オーディオの場合、1 つの文字起こし結果ファイルが作成されます。 ステレオ入力オーディオの場合、2 つの文字起こし結果ファイルが作成されます。 それぞれ次のような構造です。

```json
{
  "AudioFileResults":[ 
    {
      "AudioFileName": "Channel.0.wav | Channel.1.wav"      'maximum of 2 channels supported'
      "AudioFileUrl": null                                  'always null'
      "AudioLengthInSeconds": number                        'Real number. Two decimal places'
      "CombinedResults": [
        {
          "ChannelNumber": null                             'always null'
          "Lexical": string
          "ITN": string
          "MaskedITN": string
          "Display": string
        }
      ]
      SegmentResults:[                                      'for each individual segment'
        {
          "RecognitionStatus": "Success | Failure"
          "ChannelNumber": null
          "SpeakerId": null | "1 | 2"                       'null if no diarization
                                                             or stereo input file, the
                                                             speakerId as a string if
                                                             diarization requested for
                                                             mono audio file'
          "Offset": number                                  'time in milliseconds'
          "Duration": number                                'time in milliseconds'
          "OffsetInSeconds" : number                        'Real number. Two decimal places'
          "DurationInSeconds" : number                      'Real number. Two decimal places'
          "NBest": [
            {
              "Confidence": number                          'between 0 and 1'
              "Lexical": string
              "ITN": string
              "MaskedITN": string
              "Display": string
              "Sentiment":
                {                                           'this is omitted if sentiment is
                                                             not requested'
                  "Negative": number                        'between 0 and 1'
                  "Neutral": number                         'between 0 and 1'
                  "Positive": number                        'between 0 and 1'
                }
              "Words": [
                {
                  "Word": string
                  "Offset": number                          'time in milliseconds'
                  "Duration": number                        'time in milliseconds'
                  "OffsetInSeconds": number                 'Real number. Two decimal places'
                  "DurationInSeconds": number               'Real number. Two decimal places'
                  "Confidence": number                      'between 0 and 1'
                }
              ]
            }
          ]
        }
      ]
    }
  ]
}
```

結果には、次の形式が含まれます。

|Form|コンテンツ|
|-|-|
|`Lexical`|実際に認識された単語。
|`ITN`|認識されたテキストの逆テキスト正規化形式。 略語 ("doctor smith" から "dr smith")、電話番号、およびその他の変換が適用されます。
|`MaskedITN`|不適切表現のマスキングを適用した ITN 形式。
|`Display`|認識されたテキストの表示形式。 これには、追加された句読点と大文字化が含まれます。

## <a name="speaker-separation-diarization"></a>話者の分離 (ダイアライゼーション)

ダイアライゼーションは、音声に含まれる話者を分離するプロセスです。 ダイアライゼーションはバッチ パイプラインによってサポートされ、モノラル チャンネル レコーディングの 2 人の話者を認識できます。 この機能は、ステレオ録音では使用できません。

すべての文字起こし出力には `SpeakerId` が含まれます。 ダイアライゼーションが使用されていない場合、JSON 出力では `"SpeakerId": null` が示されます。 ダイアライゼーションでは 2 つの音声がサポートされるため、話者は `"1"` または `"2"` として識別されます。

ダイアライゼーションを要求するには、次に示すように、HTTP 要求に関連するパラメーターを追加する必要があります。

 ```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to us, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "AddWordLevelTimestamps" : "True",
    "AddDiarization" : "True"
  }
}
```

単語レベルのタイムスタンプも、上の要求に示されているように、パラメーターとして "有効にする" 必要があります。

## <a name="sentiment-analysis"></a>センチメント分析

センチメント機能によって、オーディオで表現されたセンチメント (感情) が推測されます。 センチメントは、`Negative`、`Neutral`、および `Positive` センチメントについて、0 から 1 の値で表されます。 たとえば、感情分析は、コール センターのシナリオで使用できます。

- 顧客満足度に関する分析情報を得る
- エージェント (通話を受けるチーム) のパフォーマンスに関する分析情報を得る
- 通話がネガティブな方向に向かったときの正確なポイントインタイムを見つける
- ネガティブ通話がポジティブな方向に向かったときに何が良かったのか特定する
- 製品やサービスについて、お客様が何を気に入り、何を好まないかを特定する

センチメントは、語彙形式に基づいてオーディオ セグメントごとにスコア付けされます。 そのオーディオ セグメント内のテキスト全体が、センチメントの計算に使用されます。 文字起こし全体に対して集計センチメントは計算されません。

JSON の出力サンプルは、次のようになります。

```json
{
  "AudioFileResults": [
    {
      "AudioFileName": "Channel.0.wav",
      "AudioFileUrl": null,
      "SegmentResults": [
        {
          "RecognitionStatus": "Success",
          "ChannelNumber": null,
          "Offset": 400000,
          "Duration": 13300000,
          "NBest": [
            {
              "Confidence": 0.976174,
              "Lexical": "what's the weather like",
              "ITN": "what's the weather like",
              "MaskedITN": "what's the weather like",
              "Display": "What's the weather like?",
              "Words": null,
              "Sentiment": {
                "Negative": 0.206194,
                "Neutral": 0.793785,
                "Positive": 0.0
              }
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="best-practices"></a>ベスト プラクティス

文字起こしサービスは、送信された多数の文字起こしを処理できます。 [文字起こしメソッド](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/GetTranscriptions) の `GET` を使用して、文字起こしの状態を照会できます。 `take` パラメーター (数百) を指定することで、返される情報を妥当なサイズに保つようにします。 結果を取得した後、サービスから定期的に[文字起こしを削除](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/DeleteTranscription)してください。 これにより、文字起こし管理の呼び出しからの迅速な応答が保証されます。

## <a name="sample-code"></a>サンプル コード

完全なサンプルは、[GitHub サンプル リポジトリ](https://aka.ms/csspeech/samples)の `samples/batch` サブディレクトリにあります。

自分のサブスクリプション情報、サービス リージョン、文字起こしするオーディオ ファイルをポイントする SAS URI、カスタムの音響モデルまたは言語モデルを使用する場合のモデル ID で、サンプル コードをカスタマイズする必要があります。

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

サンプル コードでは、クライアントが設定されて、文字起こし要求が送信されます。 その後、状態情報がポーリングされて、文字起こしの進行状況に関する詳細が表示されます。

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

前の呼び出しに関する詳細については、[Swagger ドキュメント](https://westus.cris.ai/swagger/ui/index)を参照してください。 ここに示すすべてのサンプルについては、[GitHub](https://aka.ms/csspeech/samples) の `samples/batch` サブディレクトリにアクセスしてください。

オーディオを渡して文字起こしの状態を受け取る非同期セットアップに注意してください。 作成されるクライアントは .NET HTTP クライアントです。 `PostTranscriptions` メソッドはオーディオ ファイルの詳細を送信し、`GetTranscriptions` メソッドは結果を受け取ります。 `PostTranscriptions` はハンドルを返し、`GetTranscriptions` はそのハンドルを使用して文字起こしの状態を取得するためのハンドルを作成します。

現在のサンプル コードでは、カスタム モデルは指定されていません。 サービスは、ベースライン モデルをファイルの文字起こしに使用します。 モデルを指定するために、音響モデルと言語モデルのモデル ID と同じメソッドに渡すことができます。

> [!NOTE]
> ベースライン文字起こしの場合は、ベースライン モデルの ID を宣言する必要はありません。 言語モデル ID だけを指定すると (音響モデル ID を指定しない)、一致する音響モデルが自動的に選択されます。 音響モデル ID だけを指定すると、一致する言語モデルが自動的に選択されます。

## <a name="download-the-sample"></a>サンプルのダウンロード

サンプルについては、[GitHub のサンプル リポジトリ](https://aka.ms/csspeech/samples)の `samples/batch` ディレクトリをご覧ください。

## <a name="next-steps"></a>次のステップ

* [Speech 試用版サブスクリプションを取得する](https://azure.microsoft.com/try/cognitive-services/)
