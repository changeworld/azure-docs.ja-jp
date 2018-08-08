---
title: Cognitive Services Speech SDK のトラブルシューティング
description: Cognitive Services Speech SDK のトラブルシューティング
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: wolfma
ms.openlocfilehash: ff8aba562cfd2d6d54c708ee7fdc4c6ca7185f29
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284124"
---
# <a name="troubleshooting-speech-services-sdk"></a>Speech Services SDK のトラブルシューティング

この記事では、Speech SDK を使用しているときに発生する可能性がある問題の解決に役立つ情報を提供します。

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>エラー `WebSocket Upgrade failed with an authentication error (403).`

リージョンまたはサービスのエンドポイントが間違っている可能性があります。 URI をもう一度調べて、それが正しいことを確認してください。 サブスクリプション キーまたは承認トークンに問題がある可能性もあるため、次のセクションも参照してください。

## <a name="error-http-403-forbidden-or-error-http-401-unauthorized"></a>エラー `HTTP 403 Forbidden` またはエラー `HTTP 401 Unauthorized`

多くの場合、このエラーは、認証の問題が原因で発生します。 有効な `Ocp-Apim-Subscription-Key` または `Authorization` ヘッダーがない接続要求は、状態 401 または 403 で拒否されます。

* 認証にサブスクリプション キーを使用している場合は、以下が原因である可能性があります。

    - サブスクリプション キーが見つからないか無効である
    - サブスクリプションの使用量クォータを超えている

* 認証に承認トークンを使用している場合は、以下が原因である可能性があります。

    - 承認トークンが無効である
    - 承認トークンの有効期限が切れている

### <a name="validate-your-subscription-key"></a>サブスクリプション キーを検証する

次のコマンドのいずれかを実行することで、有効なサブスクリプション キーを持っているかどうかを確認できます。

> [!NOTE]
> `YOUR_SUBSCRIPTION_KEY` と `YOUR_REGION` を、お使いのサブスクリプション キーと関連するリージョンに置き換えてください。

* PowerShell

    ```Powershell
    $FetchTokenHeader = @{
      'Content-type'='application/x-www-form-urlencoded'
      'Content-Length'= '0'
      'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
    }
    $OAuthToken = Invoke-RestMethod -Method POST -Uri https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader
    $OAuthToken
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0"
    ```

### <a name="validate-an-authorization-token"></a>承認トークンを検証する

認証に承認トークンを使用している場合は、次のコマンドのいずれかを実行して、認証トークンがまだ有効であることを確認します。 トークンは 10 分間有効です。

> [!NOTE]
> `YOUR_AUDIO_FILE` を録音済みのオーディオ ファイルのパスに、`YOUR_ACCESS_TOKEN` を前の手順で返された承認トークンに、`YOUR_REGION` を適切なリージョンに置き換えます。

* PowerShell

    ```Powershell
    $SpeechServiceURI =
    'https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US'
    
    # $OAuthToken is the authorization token returned by the token service.
    $RecoRequestHeader = @{
      'Authorization' = 'Bearer '+ $OAuthToken
      'Transfer-Encoding' = 'chunked'
      'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
    }
    
    # Read audio into byte array
    $audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")
    
    $RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes
    
    # Show the result
    $RecoResponse
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Transfer-Encoding: chunked" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
    ```

---

## <a name="error-http-400-bad-request"></a>エラー `HTTP 400 Bad Request`

このエラーは、通常は、要求本文に無効なオーディオ データが含まれているときに発生します。 サポートされているのは `WAV` 形式のみです。 要求のヘッダーもチェックして、適切な `Content-Type` と `Content-Length` が指定されていることを確認します。

## <a name="error-http-408-request-timeout"></a>エラー `HTTP 408 Request Timeout`

このエラーのもっとも可能性が高い原因は、オーディオ データがサービスに送信されていないことです。 このエラーは、ネットワークの問題によっても発生する可能性があります。

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>要求内の `RecognitionStatus` が `InitialSilenceTimeout` である

この問題の原因は、通常はオーディオ データです。 例: 

* オーディオの先頭が長時間サイレント状態になっています。 サービスは数秒後に認識を停止し、`InitialSilenceTimeout` を返します。
* オーディオがサポートされていないコーデック形式を使用しており、これによりオーディオ データが無音として扱われています。

## <a name="next-steps"></a>次の手順

* [リリース ノート](releasenotes.md)

