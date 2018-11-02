---
title: Azure Batch 文字起こし API
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
ms.openlocfilehash: e7523bf97d6252422ebb853b818453c935640f50
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2018
ms.locfileid: "49648804"
---
# <a name="batch-transcription"></a>バッチ文字起こし

Batch 文字起こしはストレージ内に大量のオーディオがある場合に最適です。 この REST API を使用すると、SAS URI でオーディオ ファイルを示して、非同期に文字起こしを受け取ることができます。

## <a name="batch-transcription-api"></a>バッチ文字起こし API

Batch 文字起こし API では、音声からテキストへの非同期文字起こしと他の機能が提供されます。 次のためのメソッドを公開する REST API です。

1. バッチ処理要求の作成

2. クエリの状態 

3. 文字起こしのダウンロード

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

トークンを取得した後、文字起こしが必要なオーディオ ファイルを指す SAS URI を指定する必要があります。 コードの残りでは、状態を反復処理し、結果を表示します。 最初に、使用する キー、リージョン、モデルと、SA を設定します。 これを次のコード スニペットに示します。 次に、クライアントと POST 要求をインスタンス化します。 

```cs
            private const string SubscriptionKey = "<your Speech subscription key>";
            private const string HostName = "westus.cris.ai";
            private const int Port = 443;
    
            // SAS URI 
            private const string RecordingsBlobUri = "some SAS URI";

            // adapted model Ids
            private static Guid AdaptedAcousticId = new Guid("some guid");
            private static Guid AdaptedLanguageId = new Guid("some guid");

            // Creating a Batch transcription API Client
            var client = CrisClient.CreateApiV2Client(SubscriptionKey, HostName, Port);
            
            var transcriptionLocation = await client.PostTranscriptionAsync(Name, Description, Locale, new Uri(RecordingsBlobUri), new[] { AdaptedAcousticId, AdaptedLanguageId }).ConfigureAwait(false);
```

要求が行われたので、ユーザーはクエリを行って、文字起こしの結果をダウンロードできます (コード スニペット参照)。

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
            }
        }
```

この [Swagger ドキュメント](https://westus.cris.ai/swagger/ui/index)では、上記の呼び出しの完全な詳細がわかります。 ここで示した完全なサンプルは、[GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI) にあります。

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
