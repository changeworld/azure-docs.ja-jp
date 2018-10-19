---
title: Azure Batch 文字起こし API
description: サンプル
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.component: Speech
ms.topic: article
ms.date: 04/26/2018
ms.author: panosper
ms.openlocfilehash: 8f9a033ebf9cdfdb96ae8511b14202e49ec0a85e
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2018
ms.locfileid: "48884461"
---
# <a name="batch-transcription"></a>バッチ文字起こし

Batch 文字起こしは大量のオーディオがある場合に最適です。 URI でオーディオ ファイルを指定して、文字起こしを非同期モードで受け取ることができます。

## <a name="batch-transcription-api"></a>バッチ文字起こし API

Batch 文字起こし API では、音声からテキストへの非同期文字起こしと他の機能が提供されます。

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

ステレオ オーディオ ストリームの場合、Batch 文字起こしは文字起こしの間に左チャンネルと右チャンネルを分離します。 各チャンネルから 2 つの JSON ファイルが作成されます。 発話に従うタイムスタンプにより、開発者は時間順の最終文字起こしを作成できます。 次の JSON サンプルはチャンネルの出力を示します。

```json
       {
        "recordingsUrl": "https://mystorage.blob.core.windows.net/cris-e2e-datasets/TranscriptionsDataset/small_sentence.wav?st=2018-04-19T15:56:00Z&se=2040-04-21T15:56:00Z&sp=rl&sv=2017-04-17&sr=b&sig=DtvXbMYquDWQ2OkhAenGuyZI%2BYgaa3cyvdQoHKIBGdQ%3D",
        "resultsUrls": {
            "channel_0": "https://mystorage.blob.core.windows.net/bestor-87a0286f-304c-4636-b6bd-b3a96166df28/TranscriptionData/24265e4c-e459-4384-b572-5e3e7795221f?sv=2017-04-17&sr=b&sig=IY2qd%2Fkgtz2PwRe2C88BphH4Hv%2F1VCb1UVJ33xsw%2BEY%3D&se=2018-04-23T14:48:24Z&sp=r"
        },
        "statusMessage": "None.",
        "id": "0bb95356-ff06-469d-acc7-81f9144a269a",
        "createdDateTime": "2018-04-20T14:11:57.167",
        "lastActionDateTime": "2018-04-20T14:12:54.643",
        "status": "Succeeded",
        "locale": "en-US"
    },
```

> [!NOTE]
> バッチ文字起こし API は、REST サービスを使って、文字起こし、その状態、関連する結果を要求します。 任意の言語からこの API を使用できます。 次に使い方を説明します。

## <a name="authorization-token"></a>承認トークン

Speech Service の他の機能と同様に、[使用開始ガイド](get-started.md)に従って [Azure portal](https://portal.azure.com) でサブスクリプション キーを作成します。 ベースライン モデルから文字起こしを取得する場合は、行う必要があるのはこれだけです。 

カスタム モデルをカスタマイズして使用する場合は、次のようにこのサブスクリプション キーを Custom Speech ポータルに追加する必要があります。

1. [Custom Speech](https://customspeech.ai) にサインインします。

2. **[サブスクリプション]** を選択します。

3. **[Connect Existing Subscription]\(既存のサブスクリプションに接続\)** を選択します。

4. ポップアップ表示されるビューで、サブスクリプション キーと別名を追加します

    ![Custom Speech サブスクリプション ページのスクリーンショット](media/stt/Subscriptions.jpg)

5. そのキーをコピーし、下のサンプルのクライアント コードに貼り付けます。

> [!NOTE]
> カスタム モデルを使用する場合は、そのモデルの ID も必要です。 これは [エンドポイントの詳細] ビューに表示されるエンドポイント ID ではないことに注意してください。 これは、そのモデルの詳細を選択すると取得できるモデル ID です。

## <a name="sample-code"></a>サンプル コード

サブスクリプション キーと API キーで次のサンプル コードをカスタマイズします。 これにより、ベアラー トークンを取得することができます。

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

トークンを取得した後、文字起こしが必要なオーディオ ファイルを指す SAS URI を指定する必要があります。 コードの残りでは、状態を反復処理し、結果を表示します。

```cs
   static async Task TranscribeAsync()
        { 
            private const string SubscriptionKey = "<your Speech subscription key>";
            private const string HostName = "westus.cris.ai";
            private const int Port = 443;
    
            // Creating a Batch transcription API Client
            var client = CrisClient.CreateApiV2Client(SubscriptionKey, HostName, Port);
            
            var transcriptions = await client.GetTranscriptionAsync().ConfigureAwait(false);

            var transcriptionLocation = await client.PostTranscriptionAsync(Name, Description, Locale, new Uri(RecordingsBlobUri), new[] { AdaptedAcousticId, AdaptedLanguageId }).ConfigureAwait(false);

            // get the transcription Id from the location URI
            var createdTranscriptions = new List<Guid>();
            createdTranscriptions.Add(new Guid(transcriptionLocation.ToString().Split('/').LastOrDefault()))

            while (true)
            {
                // get all transcriptions for the user
                transcriptions = await client.GetTranscriptionAsync().ConfigureAwait(false);
                completed = 0; running = 0; notStarted = 0;

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
                                // not creted form here, continue
                                continue;
                            }
                            
                            completed++;
                            
                            // if the transcription was successfull, check the results
                            if (transcription.Status == "Succeeded")
                            {
                                var resultsUri = transcription.ResultsUrls["channel_0"];
                                WebClient webClient = new WebClient();
                                var filename = Path.GetTempFileName();
                                webClient.DownloadFile(resultsUri, filename);
                                var results = File.ReadAllText(filename);
                                Console.WriteLine("Transcription succedded. Results: ");
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

                Console.WriteLine(string.Format("Transcriptions status: {0} completed, {1} running, {2} not started yet", completed, running, notStarted));

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }

            Console.WriteLine("Press any key...");
        }
```

> [!NOTE]
> 上のコードで示されているサブスクリプション キーは、Azure portal で作成する Speech リソースからのキーです。 Custom Speech Service リソースから取得したキーでは動きません。

オーディオを渡して文字起こしの状態を受け取る非同期セットアップに注意してください。 作成されるクライアントは .NET HTTP クライアントです。 `PostTranscriptions` メソッドはオーディオ ファイルの詳細を送信し、`GetTranscriptions` メソッドは結果を受け取ります。 `PostTranscriptions` はハンドルを返し、`GetTranscriptions` はそのハンドルを使用して文字起こしの状態を取得するためのハンドルを作成します。

現在のサンプル コードでは、カスタム モデルは指定されていません。 サービスは、ベースライン モデルをファイルの文字起こしに使用します。 モデルを指定するには、音響モデルと言語のモデルのモデル ID を同じメソッドに渡すことができます。 

ベースラインを使用しない場合は、音響モデルと言語モデル両方のモデル ID を渡す必要があります。

> [!NOTE]
> ベースライン文字起こしの場合は、ベースライン モデルのエンドポイントを宣言する必要はありません。 カスタム モデルを使用する場合は、[サンプル](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI)のようにそのエンドポイント ID を提供します。 音響ベースラインとベースライン言語モデルを使用する場合は、カスタム モデルのエンドポイント ID だけを宣言する必要があります。 Microsoft がパートナー ベースライン モデル (音響または言語) を検出し、それを使用して文字起こし要求を実行します。

### <a name="supported-storage"></a>サポートされるストレージ

現在、サポートされているストレージは Azure BLOB ストレージだけです。

## <a name="downloading-the-sample"></a>サンプルのダウンロード

ここで示したサンプルは、[GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI) にあります。

> [!NOTE]
> 通常、オーディオの文字起こしには、オーディオ ファイルの長さにオーバーヘッドの 2 ～ 3 分を加えた時間が必要です。

## <a name="next-steps"></a>次の手順

* [Speech 試用版サブスクリプションを取得する](https://azure.microsoft.com/try/cognitive-services/)
