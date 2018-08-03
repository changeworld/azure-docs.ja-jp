---
title: 音声サービスの REST API
description: 音声サービスの REST API のリファレンス。
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/09/2018
ms.author: v-jerkin
ms.openlocfilehash: 9991e0a1fc54e6aa4a99b8bfbd93abdfe974b01b
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39283071"
---
# <a name="speech-service-rest-apis"></a>音声サービスの REST API

音声統合サービスの REST API は、[Speech API](https://docs.microsoft.com/azure/cognitive-services/Speech) (旧 Bing Speech Service) によって提供される API と類似しています。 エンドポイントが以前の音声サービスによって使用されるものと異なります。

## <a name="speech-to-text"></a>音声テキスト変換

Speech to Text API では、エンドポイントのみが以前の音声サービスの Speech Recognition API で使用されるものと異なります。 新しいエンドポイントを以下の表に示します。 ご利用のサブスクリプション リージョンと一致するものを使用してください。

[!include[](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

それ以外については、Speech to Text API は以前の Speech API の [REST API](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedrest) と類似しています。

Speech to Text REST API では、短い発話のみがサポートされています。 要求には最大で 10 秒の音声が含まれ、全体を通して最大で 14 秒に達することがあります。 REST API は最終的な結果のみを返し、部分的または中間の結果は返しません。

> [!NOTE]
> 音響モデルまたは言語モデル、つまり発音をカスタマイズした場合は、代わりにカスタム エンドポイントを使用してください。

## <a name="text-to-speech"></a>テキストから音声へ

新しい Text to Speech API は 24-KHz オーディオ出力をサポートしています。 `X-Microsoft-OutputFormat` ヘッダーには次の値が含まれる場合があります。

|||
|-|-|
`raw-16khz-16bit-mono-pcm`         | `audio-16khz-16kbps-mono-siren`
`riff-16khz-16kbps-mono-siren`     | `riff-16khz-16bit-mono-pcm`
`audio-16khz-128kbitrate-mono-mp3` | `audio-16khz-64kbitrate-mono-mp3`
`audio-16khz-32kbitrate-mono-mp3`  | `raw-24khz-16bit-mono-pcm`
`riff-24khz-16bit-mono-pcm`        | `audio-24khz-160kbitrate-mono-mp3`
`audio-24khz-96kbitrate-mono-mp3`  | `audio-24khz-48kbitrate-mono-mp3`

音声サービスは、次の 2 つの 24-KHz の音声を提供するようになりました。

ロケール | Language   | 性別 | サービス名のマッピング
-------|------------|--------|------------
en-US  | 英語 (米国) | 女性 | "Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)" 
en-US  | 英語 (米国) | 男性   | "Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)"

次に音声統合サービスの Text to Speech API の REST エンドポイントを示します。 ご利用のサブスクリプション リージョンと一致するエンドポイントを使用してください。

[!include[](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

以前の Speech API の [REST API のドキュメント](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/bingvoiceoutput)を参照する際には、これらの違いに注意してください。

## <a name="authentication"></a>Authentication

音声サービスの REST API に要求を送信するには、アクセス トークンが必要です。 トークンは、以下の表に示すように、リージョンの音声サービスの `issueToken` エンドポイントにサブスクリプション キーを提供することで取得します。 ご利用のサブスクリプション リージョンと一致するエンドポイントを使用してください。

[!include[](../../../includes/cognitive-services-speech-service-endpoints-token-service.md)]

各アクセス トークンは 10 分間有効です。 新しいトークンは、Speech REST API の毎回の要求の最初など、いつでも取得できます。 ただし、ネットワークのトラフィックと待機時間を最小限に抑えるために、同じトークンを 9 分間使用することをお勧めします。

次のセクションで、トークンを取得する方法と、要求でそれを使用する方法について説明します。

### <a name="getting-a-token-http"></a>トークンの取得: HTTP

以下にトークンを取得するためのサンプルの HTTP 要求を示します。 `YOUR_SUBSCRIPTION_KEY` をお使いの音声サービスのサブスクリプションに置き換えます。 お使いのサブスクリプションが米国西部リージョンにない場合は、`Host` ヘッダーをご自身のリージョンのホスト名に置き換えます。

```
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

この要求に対する応答の本文は Java Web トークン (JWT) 形式のアクセス トークンです。

### <a name="getting-a-token-powershell"></a>トークンの取得: PowerShell

以下の Windows PowerShell スクリプトは、アクセス トークンを取得する方法を示しています。 `YOUR_SUBSCRIPTION_KEY` をお使いの音声サービスのサブスクリプションに置き換えます。 お使いのサブスクリプションが米国西部リージョンにない場合は、指定の URI のホスト名を適宜変更します。

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken
 -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

### <a name="getting-a-token-curl"></a>トークンの取得: cURL

cURL は Linux (および Windows Subsystem for Linux) で使用できるコマンドライン ツールです。 以下の cURL スクリプトは、アクセス トークンを取得する方法を示しています。 `YOUR_SUBSCRIPTION_KEY` をお使いの音声サービスのサブスクリプションに置き換えます。 お使いのサブスクリプションが米国西部リージョンにない場合は、指定の URI のホスト名を適宜変更します。

> [!NOTE]
> コマンドは見やすいように複数行で表示されていますが、シェル プロンプトでは 1 行で入力する必要があります。

```
curl -v -X POST 
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" 
 -H "Content-type: application/x-www-form-urlencoded" 
 -H "Content-Length: 0" 
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

### <a name="getting-a-token-c"></a>トークンの取得: C#

以下の C# クラスは、アクセス トークンを取得する方法を示しています。 クラスをインスタンス化するときにお使いの音声サービスのサブスクリプション キーを渡します。 お使いのサブスクリプションが米国西部リージョンにない場合は、`FetchTokenUri` のホスト名を適宜変更します。

```cs
    /*
     * This class demonstrates how to get a valid access token.
     */
    public class Authentication
    {
        public static readonly string FetchTokenUri =
            "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
        private string subscriptionKey;
        private string token;

        public Authentication(string subscriptionKey)
        {
            this.subscriptionKey = subscriptionKey;
            this.token = FetchTokenAsync(FetchTokenUri, subscriptionKey).Result;
        }

        public string GetAccessToken()
        {
            return this.token;
        }

        private async Task<string> FetchTokenAsync(string fetchUri, string subscriptionKey)
        {
            using (var client = new HttpClient())
            {
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                UriBuilder uriBuilder = new UriBuilder(fetchUri);

                var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
                Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
                return await result.Content.ReadAsStringAsync();
            }
        }
    }
```

### <a name="using-a-token"></a>トークンの使用

REST API の要求でトークンを使用するには、`Authorization` ヘッダーで指定し、`Bearer` と続けます。 次にトークンが含まれるサンプルの Text to Speech REST 要求を示します。 `YOUR_ACCESS_TOKEN` を実際のトークンに置き換え、`Host` ヘッダーに正しいホスト名を使用します。

```xml
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.tts.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    Hello, world!
</voice></speak>
```

### <a name="renewing-authorization"></a>認証の更新

認証トークンは10 分後に期限が切れます。 有効期限が切れる前 (9 分後など) に新しいトークンを取得して認証を更新してください。 

次の C# コードは以前に提示したクラスのドロップイン置換です。 `Authentication` クラスはタイマーを使用して 9 分ごとに新しいアクセス トークンを自動的に取得します。 この方法により、プログラムの実行中に有効なトークンが常に利用されます。

> [!NOTE]
> タイマーを使用する代わりに、現在のトークンを取得したときのタイムスタンプを格納して、現在のトークンの有効期限が迫っている場合にのみ新しいトークンを要求できます。 この方法により不要な新しいトークンを要求することを防ぎます。これは、音声の要求を不定期に行うプログラムにより適している可能性があります。

前述のように、`FetchTokenUri` の値をサブスクリプション リージョンと一致させてください。 クラスをインスタンス化するときにサブスクリプション キーを渡します。

```cs
    /*
     * This class demonstrates how to maintain a valid access token.
     */
    public class Authentication
    {
        public static readonly string FetchTokenUri = 
            "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
        private string subscriptionKey;
        private string token;
        private Timer accessTokenRenewer;

        //Access token expires every 10 minutes. Renew it every 9 minutes.
        private const int RefreshTokenDuration = 9;

        public Authentication(string subscriptionKey)
        {
            this.subscriptionKey = subscriptionKey;
            this.token = FetchToken(FetchTokenUri, subscriptionKey).Result;

            // renew the token on set duration.
            accessTokenRenewer = new Timer(new TimerCallback(OnTokenExpiredCallback),
                                           this,
                                           TimeSpan.FromMinutes(RefreshTokenDuration),
                                           TimeSpan.FromMilliseconds(-1));
        }

        public string GetAccessToken()
        {
            return this.token;
        }

        private void RenewAccessToken()
        {
            this.token = FetchToken(FetchTokenUri, this.subscriptionKey).Result;
            Console.WriteLine("Renewed token.");
        }

        private void OnTokenExpiredCallback(object stateInfo)
        {
            try
            {
                RenewAccessToken();
            }
            catch (Exception ex)
            {
                Console.WriteLine(string.Format("Failed renewing access token. Details: {0}", ex.Message));
            }
            finally
            {
                try
                {
                    accessTokenRenewer.Change(TimeSpan.FromMinutes(RefreshTokenDuration), TimeSpan.FromMilliseconds(-1));
                }
                catch (Exception ex)
                {
                    Console.WriteLine(string.Format("Failed to reschedule the timer to renew access token. Details: {0}", ex.Message));
                }
            }
        }

        private async Task<string> FetchToken(string fetchUri, string subscriptionKey)
        {
            using (var client = new HttpClient())
            {
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                UriBuilder uriBuilder = new UriBuilder(fetchUri);

                var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
                Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
                return await result.Content.ReadAsStringAsync();
            }
        }
    }
```

## <a name="next-steps"></a>次の手順

- [Speech 試用版サブスクリプションを取得する](https://azure.microsoft.com/try/cognitive-services/)
- [音響モデルをカスタマイズする](how-to-customize-acoustic-models.md)
- [言語モデルをカスタマイズする](how-to-customize-language-model.md)

