---
title: 音声サービスの REST API
description: 音声サービスの REST API のリファレンス。
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: erhopf
ms.openlocfilehash: 7f3daf71f4d94371af5f7d98c4e03761d7217a2a
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2018
ms.locfileid: "50025839"
---
# <a name="speech-service-rest-apis"></a>音声サービスの REST API

Azure Cognitive Services 音声サービスの REST API は、[Bing Speech API](https://docs.microsoft.com/azure/cognitive-services/Speech) によって提供される API と類似しています。 エンドポイントは Bing 音声サービスで使用されるものと異なります。 リージョンのエンドポイントが利用可能で、使用しているエンドポイントに対応したサブスクリプション キーを使用する必要があります。

## <a name="speech-to-text"></a>音声テキスト変換

Speech to Text REST API のエンドポイントを次の表に示します。 ご利用のサブスクリプション リージョンと一致するものを使用してください。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

> [!NOTE]
> 音響モデルまたは言語モデル、つまり発音をカスタマイズした場合は、代わりにカスタム エンドポイントを使用してください。

この API は短い発話のみをサポートします。 要求には最大で 10 秒の音声が含まれ、全体を通して最大で 14 秒に達することがあります。 REST API は最終的な結果のみを返し、部分的または中間の結果は返しません。 音声サービスには、より長いオーディオを文字起こしできる[バッチ文字起こし](batch-transcription.md) API もあります。


### <a name="query-parameters"></a>クエリ パラメーター

次のパラメーターは、R​​EST 要求のクエリ文字列に含めることができます。

|パラメーター名|必須/省略可能|意味|
|-|-|-|
|`language`|必須|認識される言語の識別子。 [サポートされている言語](language-support.md#speech-to-text)を参照してください。|
|`format`|省略可能<br>既定値: `simple`|結果の形式、`simple` または `detailed`。 単純な結果には `RecognitionStatus`、`DisplayText`、`Offset`、および期間が含まれます。 詳細な結果には、信頼度の値と 4 つの異なる表現を持った複数の候補が含まれます。|
|`profanity`|省略可能<br>既定値: `masked`|認識結果内の不適切な表現をどう扱うか。 `masked` (不適切な表現をアスタリスクに置き換える)、`removed` (すべての不適切な表現を除去する)、または `raw` (不適切な表現を含める) を指定できます。

### <a name="request-headers"></a>要求ヘッダー

次のフィールドは HTTP 要求ヘッダーで送信されます。

|ヘッダー|意味|
|------|-------|
|`Ocp-Apim-Subscription-Key`|音声サービスのサブスクリプション キー。 このヘッダーまたは `Authorization` のどちらかを指定する必要があります。|
|`Authorization`|単語 `Bearer` が前に付いた認証トークン。 このヘッダーまたは `Ocp-Apim-Subscription-Key` のどちらかを指定する必要があります。 [認証](#authentication)に関するページを参照してください。|
|`Content-type`|オーディオ データの形式とコーデックを記述します。 現在、この値は `audio/wav; codec=audio/pcm; samplerate=16000` である必要があります。|
|`Transfer-Encoding`|省略可能。 指定する場合、1 つのファイルではなく複数の小さなチャンクでオーディオ データを送信できるよう、`chunked` である必要があります。|
|`Expect`|チャンク転送を使用する場合、`Expect: 100-continue` を送信します。 音声サービスは最初の要求を確認し、追加のデータを待ちます。|
|`Accept`|省略可能。 音声サービスは結果を JSON 形式で提供するため、指定する場合は `application/json` を含める必要があります。 (指定しない場合、一部の Web 要求フレームワークでは互換性のない既定値が提供されるため、常に `Accept` を含めることをお勧めします。)|

### <a name="audio-format"></a>音声の形式

オーディオは HTTP `POST` 要求の本文で送信されます。 次の形式/エンコードの、16 KHz PCM シングル チャンネル (モノラル) の 16 ビット WAV 形式である必要があります。

* PCM コーデックの WAV 形式
* OPUS コーデックの Ogg 形式

>[!NOTE]
>上の形式は、Speech Service の REST API と WebSocket を介してサポートされます。 現在、[Speech SDK](/index.yml) では PCM コーデックの WAV 形式のみがサポートされています。

### <a name="chunked-transfer"></a>チャンク転送

チャンク転送 (`Transfer-Encoding: chunked`) を使用すると、オーディオ ファイルの送信中に音声サービスがオーディオ ファイルの処理を開始できるようになるため、認識待ち時間の短縮に役立ちます。 REST API は部分的または中間的な結果を提供しません。 このオプションは、単に応答性を向上させるためのものです。

次のコードは、オーディオをチャンクで送信する方法を示しています。 最初のチャンクだけに、オーディオ ファイルのヘッダーが含まれている必要があります。 `request` は、適切な REST エンドポイントに接続された HTTPWebRequest オブジェクトです。 `audioFile` はディスク上のオーディオ ファイルのパスです。

```csharp
using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{

    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```

### <a name="example-request"></a>要求の例

一般的な要求を次に示します。

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="http-status"></a>HTTP の状態

応答の HTTP ステータスは、成功または一般的なエラー状態を示します。

HTTP コード|意味|考えられる理由
-|-|-|
100|Continue|最初の要求が受け付けられました。 残りのデータの送信を続行します。 (チャンク転送で使用されます。)
200|OK|要求は成功しました。応答本文は JSON オブジェクトです。
400|正しくない要求|言語コードが提供されていないか、サポートされていない言語です。無効なオーディオファイルです。
401|権限がありません|サブスクリプション キーまたは認証トークンが指定のリージョンで無効であるか、または無効なエンドポイントです。
403|許可されていません|サブスクリプション キーまたは認証トークンがありません。

### <a name="json-response"></a>JSON 応答

結果は JSON 形式で返されます。 `simple` 形式には、次の最上位レベルのフィールドのみが含まれます。

|フィールド名|コンテンツ|
|-|-|
|`RecognitionStatus`|認識成功を表す `Success` などのステータス。 次の表を参照してください。|
|`DisplayText`|大文字化、句読点、逆テキスト正規化 (「two hundred」から「200」に、または「doctor smith」から「Dr. Smith」のように、音声テキストをより短い形式に変換すること)、および不適切な表現のマスキングの後に認識されたテキスト。 成功時にのみ存在します。|
|`Offset`|認識された音声がオーディオ ストリーム内で開始する時間 (100 ナノ秒単位)。|
|`Duration`|認識された音声のオーディオ ストリーム内での持続時間 (100 ナノ秒単位)。|

`RecognitionStatus` フィールドには次の値が含まれる可能性があります。

|ステータス値|説明
|-|-|
| `Success` | 認識が成功し、DisplayText フィールドが存在する。 |
| `NoMatch` | 音声ストリーム内に音声が検出されたが、ターゲット言語の単語は見つからなかった。 通常は、認識言語がユーザーが話している言語とは異なる言語であることを意味します。 |
| `InitialSilenceTimeout` | 音声ストリームの先頭に無音状態しか含まれておらず、音声の待機中にサービスがタイムアウトした。 |
| `BabbleTimeout` | 音声ストリームの先頭に雑音しか含まれておらず、音声の待機中にサービスがタイムアウトした。 |
| `Error` | 認識サービスで内部エラーが発生し、処理を継続できない。 可能であれば再試行してください。 |

> [!NOTE]
> オーディオが不適切な表現のみで構成されており、`profanity` クエリ パラメーターが `remove` に設定されている場合、サービスは音声結果を返しません。


`detailed` 形式には、`simple` 形式と同じフィールドに加えて `NBest` フィールドが含まれています。 `NBest` フィールドは同じ音声の代替解釈のリストであり、最も可能性が高いものから最も可能性が低いものまでランク付けされています。 最初のエントリはメイン認識結果と同じです。 各エントリには次のフィールドが含まれます。

|フィールド名|コンテンツ|
|-|-|
|`Confidence`|0.0 (信頼度なし) から1.0 (完全信頼) までのエントリの信頼度スコア
|`Lexical`|認識されたテキストの語彙形式: 認識された実際の単語。
|`ITN`|認識されたテキストの逆テキスト正規化 ("カノニカル") 形式。電話番号、数字、略語 (「doctor smith」から「dr smith」)、およびその他の変換を適用したものです。
|`MaskedITN`| 要求された場合、不適切な表現のマスキングを適用した ITN 形式。
|`Display`| 認識されたテキストの表示形式。句読点と大文字化を追加したものです。 最上位の結果では `DisplayText` と同じです。

### <a name="sample-responses"></a>応答のサンプル

次に示すのは、`simple` 認識の典型的な応答です。

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

次に示すのは、`detailed` 認識の典型的な応答です。

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="text-to-speech"></a>テキストから音声へ

次に示すのは、音声サービスの Text to Speech API の REST エンドポイントです。 ご利用のサブスクリプション リージョンと一致するエンドポイントを使用してください。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

音声サービスは、Bing Speech でサポートされている 1​6 KHz 出力に加えて、24 KHz オーディオ出力をサポートします。 `X-Microsoft-OutputFormat` HTTP ヘッダーで使用するために 4 つの 24 KHz 出力形式が利用可能であり、内訳は 2 つの 24 KHz 音声、`Jessa24kRUS`、`Guy24kRUS` です。

ロケール | Language   | 性別 | サービス名のマッピング
-------|------------|--------|------------
en-US  | 英語 (米国) | 女性 | "Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)"
en-US  | 英語 (米国) | 男性   | "Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)"

利用可能なすべての音声の一覧については、[サポートされている言語](language-support.md#text-to-speech)を参照してください。

### <a name="request-headers"></a>要求ヘッダー

次のフィールドは HTTP 要求ヘッダーで送信されます。

|ヘッダー|意味|
|------|-------|
|`Authorization`|単語 `Bearer` が前に付いた認証トークン。 必須。 [認証](#authentication)に関するページを参照してください。|
|`Content-Type`|入力コンテンツの種類: `application/ssml+xml`。|
|`X-Microsoft-OutputFormat`|出力オーディオの形式。 次の表を参照してください。|
|`User-Agent`|アプリケーション名。 必須。255 文字未満である必要があります。|

使用可能なオーディオ出力形式 (`X-Microsoft-OutputFormat`) には、ビット レートとエンコードの両方が組み込まれています。

|||
|-|-|
`raw-16khz-16bit-mono-pcm`         | `raw-8khz-8bit-mono-mulaw`
`riff-8khz-8bit-mono-mulaw`     | `riff-16khz-16bit-mono-pcm`
`audio-16khz-128kbitrate-mono-mp3` | `audio-16khz-64kbitrate-mono-mp3`
`audio-16khz-32kbitrate-mono-mp3`  | `raw-24khz-16bit-mono-pcm`
`riff-24khz-16bit-mono-pcm`        | `audio-24khz-160kbitrate-mono-mp3`
`audio-24khz-96kbitrate-mono-mp3`  | `audio-24khz-48kbitrate-mono-mp3`

> [!NOTE]
> 選択した音声と出力形式のビット レートが異なる場合、オーディオは必要に応じて再サンプリングされます。 ただし、24 khz の音声は `audio-16khz-16kbps-mono-siren` や `riff-16khz-16kbps-mono-siren` の出力形式をサポートしていません。

### <a name="request-body"></a>要求本文

音声に変換されるテキストは、プレーン テキスト (ASCII または UTF-8) または[音声合成マークアップ言語](speech-synthesis-markup.md) (SSML) 形式 (UTF-8) のどちらかで、HTTP `POST` 要求の本文として送信されます。 プレーン テキストの要求は、サービスの既定の音声と言語を使用します。 別の音声を使用するには、SSML を送信します。

### <a name="sample-request"></a>要求のサンプル

次の HTTP 要求は、SSML 本文を使用して音声を選択します。 本文は 1,000 文字以内にする必要があります。

```xml
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-16khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

### <a name="http-response"></a>HTTP 応答

応答の HTTP ステータスは、成功または一般的なエラー状態を示します。

HTTP コード|意味|考えられる理由
-|-|-|
200|OK|要求は成功しました。応答本文はオーディオ ファイルです。
400 |正しくない要求 |必須パラメーターが指定されていない、空、または null です。 または、必須またはオプションのパラメーターに渡された値が無効です。 よくある問題はヘッダーが長すぎることです。
401|権限がありません |要求が承認されていません。 サブスクリプション キーまたはトークンが有効であり、正しいリージョンにあることを確認してください。
413|要求のエンティティが大きすぎます|SSML 入力が 1024 文字を超えています
429|要求が多すぎます|使用中のサブスクリプションで許可されている要求のクォータまたは速度を超えています。
502|無効なゲートウェイ | ネットワークまたはサーバー側の問題です。 無効なヘッダーを示す場合もあります。

HTTP ステータスが `200 OK` の場合、応答の本文には要求された形式のオーディオ ファイルが含まれています。 このファイルは、転送後すぐに再生することも、後で再生または別の用途に使用するためにバッファーまたはファイルに保存することもできます。

## <a name="authentication"></a>Authentication

音声サービスの REST API に要求を送信するには、サブスクリプション キーまたはアクセス トークンのどちらかが必要です。 一般に、サブスクリプション キーを直接送信するのが最も簡単です。 その後、音声サービスが自動的にアクセス トークンを取得します。 応答時間を最小限にするために、代わりにアクセス トークンを使用することができます。

トークンを取得するには、次の表に示すように、リージョンの音声サービスの `issueToken` エンドポイントにサブスクリプション キーを提供します。 ご利用のサブスクリプション リージョンと一致するエンドポイントを使用してください。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-token-service.md)]

各アクセス トークンは 10 分間有効です。 いつでも新しいトークンを取得できます。 必要な場合は、毎回の Speech REST API 要求の直前にトークンを取得できます。 ネットワークのトラフィックと待機時間を最小限に抑えるために、同じトークンを 9 分間使用することをお勧めします。

次のセクションで、トークンを取得する方法と、要求でそれを使用する方法について説明します。

### <a name="get-a-token-http"></a>トークンを取得する: HTTP

次の例は、トークンを取得するためのサンプルの HTTP 要求です。 `YOUR_SUBSCRIPTION_KEY` をお使いの音声サービスのサブスクリプションに置き換えます。 お使いのサブスクリプションが米国西部リージョンにない場合は、`Host` ヘッダーをご自身のリージョンのホスト名に置き換えます。

```
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

この要求に対する応答の本文は Java Web トークン (JWT) 形式のアクセス トークンです。

### <a name="get-a-token-powershell"></a>トークンを取得する: PowerShell

次の Windows PowerShell スクリプトは、アクセス トークンを取得する方法を示しています。 `YOUR_SUBSCRIPTION_KEY` をお使いの音声サービスのサブスクリプションに置き換えます。 お使いのサブスクリプションが米国西部リージョンにない場合は、指定の URI のホスト名を適宜変更します。

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

### <a name="get-a-token-curl"></a>トークンを取得する: cURL

cURL は Linux (および Windows Subsystem for Linux) で使用できるコマンドライン ツールです。 次の cURL スクリプトは、アクセス トークンを取得する方法を示しています。 `YOUR_SUBSCRIPTION_KEY` をお使いの音声サービスのサブスクリプションに置き換えます。 お使いのサブスクリプションが米国西部リージョンにない場合は、指定の URI のホスト名を適宜変更します。

> [!NOTE]
> コマンドは見やすいように複数行で表示されていますが、シェル プロンプトでは 1 行で入力します。

```
curl -v -X POST
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
 -H "Content-type: application/x-www-form-urlencoded"
 -H "Content-Length: 0"
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

### <a name="get-a-token-c"></a>トークンを取得する: C#

次の C# クラスは、アクセス トークンを取得する方法を示しています。 クラスをインスタンス化するときに、お使いの音声サービスのサブスクリプション キーを渡します。 お使いのサブスクリプションが米国西部リージョンにない場合は、`FetchTokenUri` のホスト名を適宜変更します。

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

### <a name="use-a-token"></a>トークンを使用する

REST API の要求でトークンを使用するには、`Authorization` ヘッダーで指定し、`Bearer` と続けます。 次に示すのは、トークンが含まれるサンプルの Text to Speech REST 要求です。 `YOUR_ACCESS_TOKEN` は実際のトークンに置き換えます。 `Host` ヘッダーでは正しいホスト名を使用します。

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

### <a name="renew-authorization"></a>承認の更新

認証トークンは10 分後に期限が切れます。 有効期限が切れる前に新しいトークンを取得して認証を更新してください。 たとえば、9 分後に新しいトークンを取得できます。

次の C# コードは以前に提示したクラスのドロップイン置換です。 `Authentication` クラスはタイマーを使用して 9 分ごとに新しいアクセス トークンを自動的に取得します。 この方法により、プログラムの実行中に有効なトークンが常に利用されます。

> [!NOTE]
> タイマーを使用する代わりに、最後のトークンが取得されたときのタイムスタンプを保存することができます。 その場合、期限切れが近い場合にのみ新しいトークンを要求できます。 この方法により不要な新しいトークンを要求することを防ぎます。これは、音声の要求を不定期に行うプログラムにより適している可能性があります。

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
