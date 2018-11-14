---
title: Azure Batch 文字起こし API を使用する
titlesuffix: Azure Cognitive Services
description: 大量のオーディオ コンテンツの文字起こしに関するサンプルです。
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: panosper
ms.openlocfilehash: cd57e9a90b07447392fbff48017bb29f002ad29e
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2018
ms.locfileid: "51035953"
---
# <a name="use-batch-transcription"></a>バッチ文字起こしを使用する

Batch 文字起こしはストレージ内に大量のオーディオがある場合に最適です。 この REST API を使用すると、Shared Access Signatures (SAS) URI でオーディオ ファイルを示して、非同期に文字起こしを受け取ることができます。

## <a name="the-batch-transcription-api"></a>Batch 文字起こし API

Batch 文字起こし API では、音声からテキストへの非同期文字起こしと他の機能が提供されます。 次のためのメソッドを公開する REST API です。

1. バッチ処理要求の作成
1. クエリの状態 
1. 文字起こしのダウンロード

> [!NOTE]
> Batch 文字起こし API は、何千時間ものオーディオがたまるのが普通なコール センターに理想的です。 この API は "ファイア アンド フォーゲット" の方針に従い、大量のオーディオ録音を簡単に文字起こしできます。

### <a name="supported-formats"></a>サポートされるフォーマット

Batch 文字起こし API では、次の形式がサポートされています。

Name| チャネル  |
----|----------|
mp3 |   Mono   |   
mp3 |  ステレオ  | 
wav |   Mono   |
wav |  ステレオ  |
Opus|   Mono   |
Opus|  ステレオ  |

ステレオ オーディオ ストリームの場合、Batch 文字起こしは文字起こしの間に左チャンネルと右チャンネルを分離します。 各チャンネルから 2 つの JSON ファイルが作成されます。 発話に従うタイムスタンプにより、開発者は時間順の最終文字起こしを作成できます。 不適切な単語フィルターと句読点モデルの設定のためのプロパティを含むチャネルの出力が、次の JSON サンプルに示されています。

```json
{
  "recordingsUrl": "https://contoso.com/mystoragelocation",
  "models": [],
  "locale": "en-US",
  "name": "Transcription using locale en-US",
  "description": "An optional description of the transcription.",
  "properties": {
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic"
  },
```

> [!NOTE]
> Batch 文字起こし API は REST サービスを使用して、文字起こし、その状態、および関連する結果を要求します。 任意の言語からこの API を使用できます。 次のセクションでは、API の使用方法について説明します。

## <a name="authorization-token"></a>承認トークン

Speech Service の他の機能と同様に、[使用開始ガイド](get-started.md)に従って [Azure portal](https://portal.azure.com) でサブスクリプション キーを作成します。 ベースライン モデルから文字起こしを取得する場合は、行う必要があるのはキーを作成することだけです。 

カスタム モデルをカスタマイズして使用する場合は、次の手順に従って、Custom Speech ポータルにサブスクリプション キーを追加します。

1. [Custom Speech](https://customspeech.ai) にサインインします。

2. 右上で **[サブスクリプション]** を選択します。

3. **[Connect Existing Subscription]\(既存のサブスクリプションに接続\)** を選択します。

4. ポップアップ表示されるウィンドウで、サブスクリプション キーと別名を追加します。

    ![[サブスクリプションの追加] ウィンドウ](media/stt/Subscriptions.jpg)

5. そのキーをコピーし、下のサンプルのクライアント コードに貼り付けます。

> [!NOTE]
> カスタム モデルを使用する場合は、そのモデルの ID も必要です。 この ID は [エンドポイントの詳細] ビューに表示されるエンドポイント ID ではありません。 これは、そのモデルの詳細を選択すると取得できるモデル ID です。

## <a name="sample-code"></a>サンプル コード

サブスクリプション キーと API キーで次のサンプル コードをカスタマイズします。 このアクションでは、ベアラー トークンを取得できます。

```cs
     public static CrisClient CreateApiV2Client(string key, string hostName, int port)

        {
            var client = new HttpClient();
            client.Timeout = TimeSpan.FromMinutes(25);
            client.BaseAddress = new UriBuilder(Uri.UriSchemeHttps, hostName, port).Uri;
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);
         
            return new CrisClient(client);
        }
```

トークンを取得した後、文字起こしが必要なオーディオ ファイルを指す SAS URI を指定します。 コードの残りの部分では、状態を反復処理し、結果を表示します。 最初に、使用するキー、リージョン、モデルを設定し、次のコード スニペットに示すように SA を指定します。 次に、クライアントと POST 要求をインスタンス化します。 

```cs
            private const string SubscriptionKey = "<your Speech subscription key>";
            private const string HostName = "westus.cris.ai";
            private const int Port = 443;
    
            // SAS URI 
            private const string RecordingsBlobUri = "SAS URI pointing to the file in Azure Blob Storage";

            // adapted model Ids
            private static Guid AdaptedAcousticId = new Guid("guid of the acoustic adaptation model");
            private static Guid AdaptedLanguageId = new Guid("guid of the language model");

            // Creating a Batch Transcription API Client
            var client = CrisClient.CreateApiV2Client(SubscriptionKey, HostName, Port);
            
            var transcriptionLocation = await client.PostTranscriptionAsync(Name, Description, Locale, new Uri(RecordingsBlobUri), new[] { AdaptedAcousticId, AdaptedLanguageId }).ConfigureAwait(false);
```

これで要求が完了したため、次のコード スニペットに示すように、文字起こし結果に対してクエリを実行してダウンロードすることができます。

```cs
  
            // get all transcriptions for the user
            transcriptions = await client.GetTranscriptionAsync().ConfigureAwait(false);

            // for each transcription in the list we check the status
            foreach (var transcription in transcriptions)
            {
                switch(transcription.Status)
                {
                    case "Failed":
                    case "Succeeded":

                            // we check to see if it was one of the transcriptions we created from this client.
                        if (!createdTranscriptions.Contains(transcription.Id))
                        {
                            // not created from here, continue
                            continue;
                        }
                            
                        completed++;
                            
                        // if the transcription was successful, check the results
                        if (transcription.Status == "Succeeded")
                        {
                            var resultsUri = transcription.ResultsUrls["channel_0"];
                            WebClient webClient = new WebClient();
                            var filename = Path.GetTempFileName();
                            webClient.DownloadFile(resultsUri, filename);
                            var results = File.ReadAllText(filename);
                            Console.WriteLine("Transcription succeeded. Results: ");
                            Console.WriteLine(results);
                        }
                    
                    break;
                    case "Running":
                    running++;
                     break;
                    case "NotStarted":
                    notStarted++;
                    break;
                    
                    }
                }
            }
        }
```

前の呼び出しに関する詳細については、[swagger ドキュメント](https://westus.cris.ai/swagger/ui/index)を参照してください。 ここに示すすべてのサンプルについては、[GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI) にアクセスしてください。

> [!NOTE]
> 上のコードで示されているサブスクリプション キーは、Azure portal で作成する Speech リソースからのキーです。 Custom Speech Service リソースから取得したキーでは動きません。

オーディオを渡して文字起こしの状態を受け取る非同期セットアップに注意してください。 作成されるクライアントは .NET HTTP クライアントです。 `PostTranscriptions` メソッドはオーディオ ファイルの詳細を送信し、`GetTranscriptions` メソッドは結果を受け取ります。 `PostTranscriptions` はハンドルを返し、`GetTranscriptions` はそのハンドルを使用して文字起こしの状態を取得するためのハンドルを作成します。

現在のサンプル コードでは、カスタム モデルは指定されていません。 サービスは、ベースライン モデルをファイルの文字起こしに使用します。 モデルを指定するために、音響モデルと言語モデルのモデル ID と同じメソッドに渡すことができます。 

ベースラインを使用しない場合は、音響モデルと言語モデルの両方のモデル ID を渡す必要があります。

> [!NOTE]
> ベースライン文字起こしの場合は、ベースライン モデルのエンドポイントを宣言する必要はありません。 カスタム モデルを使用する場合は、[サンプル](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI)のようにそのエンドポイント ID を提供します。 音響ベースラインとベースライン言語モデルを使用する場合は、カスタム モデルのエンドポイント ID だけを宣言する必要があります。 Microsoft がパートナー ベースライン モデル (音響または言語) を検出し、それを使用して文字起こし要求を実行します。

### <a name="supported-storage"></a>サポートされるストレージ

現在、サポートされているストレージは Azure BLOB ストレージだけです。

## <a name="download-the-sample"></a>サンプルのダウンロード

この記事のサンプルは、[GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI) で見つけることができます。

> [!NOTE]
> 通常、オーディオの文字起こしには、オーディオ ファイルの長さにオーバーヘッドの 2 ～ 3 分を加えた時間が必要です。

## <a name="next-steps"></a>次の手順

* [Speech 試用版サブスクリプションを取得する](https://azure.microsoft.com/try/cognitive-services/)
