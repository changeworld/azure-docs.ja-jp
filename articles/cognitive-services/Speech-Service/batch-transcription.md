---
title: Azure Batch 文字起こし API
description: サンプル
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.technology: Speech to Text
ms.topic: article
ms.date: 04/26/2018
ms.author: panosper
ms.openlocfilehash: f21973855ceb3a257627c147490ac50465c54020
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39281941"
---
# <a name="batch-transcription"></a>バッチ文字起こし

バッチ文字起こしは大量のオーディオがあるユース ケースに最適です。 開発者は、オーディオ ファイルを指定して、文字起こしを非同期モードで受け取ることができます。

## <a name="batch-transcription-api"></a>バッチ文字起こし API

バッチ文字起こし API を使ってこのようなシナリオを実現できます。 音声からテキストへの非同期文字起こしと他の機能が提供されます。

> [!NOTE]
> バッチ文字起こし API は、何千時間ものオーディオがたまるのが普通なコール センターに理想的です。 API のファイア アンド フォーゲット方針により、大量のオーディオ録音を簡単に文字起こしできます。

### <a name="supported-formats"></a>サポートされるフォーマット

バッチ文字起こし API はすべてのオフライン コール センター関連シナリオの標準となることが意図されており、すべての関連フォーマットのサポートを提供します。 現在サポートされているフォーマット:

Name| チャネル  |
----|----------|
mp3 |   Mono   |   
mp3 |  ステレオ  | 
wav |   Mono   |
wav |  ステレオ  |

ステレオ オーディオ ストリームの場合、バッチ文字起こしは文字起こしの間に左チャンネルと右チャンネルを分離します。 各チャンネルから 2 つの JSON ファイルが作成されます。 発話に従うタイムスタンプにより、開発者は時間順の最終文字起こしを作成できます。 次の JSON サンプルはチャンネルの出力を示します。

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
> バッチ文字起こし API は、REST サービスを使って、文字起こし、その状態、関連する結果を要求します。 この API は、任意の言語から使用できます。 次に使い方を説明します。

## <a name="authorization-token"></a>承認トークン

音声認識統合サービスの他の機能と同様に、ユーザーは [Azure portal](https://portal.azure.com) でサブスクリプション キーを作成する必要があります。 さらに、Speech ポータルから API キーを入手する必要があります。 API キー生成手順は次のとおりです。

1. https://customspeech.ai にログインします。

2. [サブスクリプション] をクリックします。

3. [`Generate API Key`] オプションをクリックします。

    ![アップロード ビュー](media/stt/Subscriptions.jpg)

4. キーをコピーし、下のサンプルのクライアント コードに貼り付けます。

> [!NOTE]
> カスタム モデルを使用する場合は、そのモデルの ID も必要です。 これはエンドポイント詳細ビューに表示される展開 ID またはエンドポイント ID ではなく、そのモデルの [詳細] をクリックして取得できるモデル ID であることに注意してください。

## <a name="sample-code"></a>サンプル コード

API の使用方法はとても簡単です。 下のサンプル コードをサブスクリプション キーと API キーでカスタマイズする必要があります。それにより、次のコード スニペットに示すように、開発者はベアラー トークンを取得できます。

```cs
    public static async Task<CrisClient> CreateApiV1ClientAsync(string username, string key, string hostName, int port)
        {
            var client = new HttpClient();
            client.Timeout = TimeSpan.FromMinutes(25);
            client.BaseAddress = new UriBuilder(Uri.UriSchemeHttps, hostName, port).Uri;

            var tokenProviderPath = "/oauth/ctoken";
            var clientToken = await CreateClientTokenAsync(client, hostName, port, tokenProviderPath, username, key).ConfigureAwait(false);
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("bearer", clientToken.AccessToken);

            return new CrisClient(client);
        }
```

トークンが取得されたら、開発者は、文字起こしが必要なオーディオ ファイルを指す SAS URI を指定する必要があります。 コードの残りは単純に、状態を反復処理し、結果を表示します。

```cs
   static async Task TranscribeAsync()
        { 
            // Creating a Batch transcription API Client
            var client = 
                await CrisClient.CreateApiV1ClientAsync(
                    "<your msa>", // MSA email
                    "<your api key>", // API key
                    "stt.speech.microsoft.com",
                    443).ConfigureAwait(false);
            
            var newLocation = 
                await client.PostTranscriptionAsync(
                    "<selected locale i.e. en-us>", // Locale 
                    "<your subscription key>", // Subscription Key
                    new Uri("<SAS URI to your file>")).ConfigureAwait(false);

            var transcription = await client.GetTranscriptionAsync(newLocation).ConfigureAwait(false);

            while (true)
            {
                transcription = await client.GetTranscriptionAsync(transcription.Id).ConfigureAwait(false);

                if (transcription.Status == "Failed" || transcription.Status == "Succeeded")
                {
                    Console.WriteLine("Transcription complete!");

                    if (transcription.Status == "Succeeded")
                    {
                        var resultsUri = transcription.ResultsUrls["channel_0"];

                        WebClient webClient = new WebClient();

                        var filename = Path.GetTempFileName();
                        webClient.DownloadFile(resultsUri, filename);

                        var results = File.ReadAllText(filename);
                        Console.WriteLine(results);
                    }

                    await client.DeleteTranscriptionAsync(transcription.Id).ConfigureAwait(false);

                    break;
                }
                else
                {
                    Console.WriteLine("Transcription status: " + transcription.Status);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }

            Console.ReadLine();
        }
```

> [!NOTE]
> 上のコード スニペットで示されているサブスクリプション キーは、Azure portal で作成する Speech(Preview) リソースからのキーです。 Custom Speech Service リソースから取得したキーでは動きません。


オーディオを渡して文字起こしの状態を受け取る非同期セットアップに注意してください。 作成されるクライアントは .NET HTTP クライアントです。 `PostTranscriptions` メソッドはオーディオ ファイルの詳細を送信し、`GetTranscriptions` メソッドは結果を受け取ります。 `PostTranscriptions` はハンドルを返し、`GetTranscriptions` メソッドはそのハンドルを使用して文字起こしの状態を取得するためのハンドルを作成します。

現在のサンプル コードでは、カスタム モデルは指定されていません。 サービスは、ベースライン モデルをファイルの文字起こしに使用します。 ユーザーは、モデルを指定したい場合は、音響と言語のモデルの modelID を同じメソッドに渡すことができます。 

ベースラインを使いたくない場合は、音響モデルと言語モデル両方のモデル ID を渡す必要があります。

> [!NOTE]
> ベースライン文字起こしの場合は、ユーザーはベースライン モデルのエンドポイントを宣言する必要はありません。 カスタム モデルを使いたい場合は、[サンプル](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI)のようにエンドポイント ID を提供する必要があります。 音響ベースラインとベースライン言語モデルを使いたい場合は、カスタム モデルのエンドポイント ID を宣言することだけが必要です。 システムは内部的にパートナー ベースライン モデル (音響または言語) を見つけ、それを使用して文字起こし要求を実行します。

### <a name="supported-storage"></a>サポートされるストレージ

現在、サポートされているストレージは Azure BLOB だけです。

## <a name="downloading-the-sample"></a>サンプルのダウンロード

ここで示してあるサンプルは、[GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI) にあります。

> [!NOTE]
> 通常、オーディオの文字起こしには、オーディオ ファイルの長さにオーバーヘッドの 2 ～ 3 分を加えた時間が必要です。

## <a name="next-steps"></a>次の手順

* [Speech 試用版サブスクリプションを取得する](https://azure.microsoft.com/try/cognitive-services/)
