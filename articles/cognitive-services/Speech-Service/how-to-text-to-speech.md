---
title: Speech Service で Text to Speech を使用する
titleSuffix: Azure Cognitive Services
description: 音声サービスで Text to Speech を使用する方法について説明します。
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 09/08/2018
ms.author: erhopf
ms.openlocfilehash: 162b690d4b371cfe76738cd83ce484a3062d139f
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2018
ms.locfileid: "49469919"
---
# <a name="use-text-to-speech-in-speech-service"></a>Speech Service で "Text to Speech" を使用する

音声サービスには、簡単な HTTP 要求を通じて利用できる、Text to Speech 機能が用意されています。 読み上げられるテキストを適切なエンドポイントに `POST` し、合成された音声を含むオーディオ ファイル (`.wav`) がサービスから返されます。 こうすることで、アプリケーションがこのオーディオを適宜使用できるようになります。

Text to Speech の POST 要求の本文は、プレーンテキスト (ASCII か UTF8) または [SSML](speech-synthesis-markup.md) ドキュメントのいずれかで指定できます。 プレーン テキストの要求は、既定の音声で読み上げられます。 ほとんどの場合は SSML 本文を使用します。 HTTP 要求には[承認](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#authentication)トークンが含まれている必要があります。

Text to Speech のエンドポイントがリージョン別に表示されています。 お使いのサブスクリプションに該当するものを使用してください。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

## <a name="specify-a-voice"></a>音声を指定する

音声を指定するには、`<voice>` [SSML](speech-synthesis-markup.md) タグを使用します。 例: 

```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
  <voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Hello, world!
  </voice>
</speak>
```

利用可能な音声とその名前については、[Text to Speech の音声](language-support.md#text-to-speech)一覧で確認してください。

## <a name="make-a-request"></a>要求を行う

Text to Speech の HTTP 要求は POST モードで行われ、要求の本文は読み上げられるテキストで構成されます。 HTTP 要求の本体の最大長は 1024 文字です。 要求には次のヘッダーが必要です。

ヘッダー|値|説明
-|-|-
|`Content-Type` | `application/ssml+xml` | 入力するテキストの形式。
|`X-Microsoft-OutputFormat`|     `raw-16khz-16bit-mono-pcm`<br>`riff-16khz-16bit-mono-pcm`<br>`raw-8khz-8bit-mono-mulaw`<br>`riff-8khz-8bit-mono-mulaw`<br>`audio-16khz-128kbitrate-mono-mp3`<br>`audio-16khz-64kbitrate-mono-mp3`<br>`audio-16khz-32kbitrate-mono-mp3`<br>`raw-24khz-16bit-mono-pcm`<br>`riff-24khz-16bit-mono-pcm`<br>`audio-24khz-160kbitrate-mono-mp3`<br>`audio-24khz-96kbitrate-mono-mp3`<br>`audio-24khz-48kbitrate-mono-mp3` | 出力オーディオの形式。
|`User-Agent`   |アプリケーション名 | アプリケーション名は必須で、255 文字未満にする必要があります。
| `Authorization`   | トークン サービスにサブスクリプション キーを提示することで取得した認証トークン。 各トークンは 10 分間有効です。 「[REST API: 認証](rest-apis.md#authentication)」をご覧ください。

> [!NOTE]
> 選択した音声と出力形式のビット レートが異なる場合、オーディオは必要に応じて再サンプリングされます。

サンプルの要求を以下に示します。

```xml
POST /cognitiveservices/v1
HTTP/1.1
Host: westus.tts.speech.microsoft.com
X-Microsoft-OutputFormat: riff-24khz-16bit-mono-pcm
Content-Type: application/ssml+xml
User-Agent: Test TTS application
Authorization: (authorization token)

<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Hello, world!
</voice> </speak>
```

状態が 200 の応答の本文には指定した出力形式のオーディオが含まれています。

```
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

エラーが発生した場合は、以下の状態コードが使用されます。 エラーの応答の本文には問題の説明も含まれています。

|コード|説明|問題点|
|-|-|-|
400 |正しくない要求 |必須パラメーターが指定されていない、空、または null です。 または、必須またはオプションのパラメーターに渡された値が無効です。 よくある問題はヘッダーが長すぎることです。
401|権限がありません |要求が承認されていません。 サブスクリプション キーまたはトークンが有効であることを確認してください。
413|要求のエンティティが大きすぎます|入力 SSML が大きすぎるか、含まれている `<voice>` 要素が 3 個を超えています。
429|要求が多すぎます|使用中のサブスクリプションで許可されている要求のクォータまたは速度を超えています。
|502|無効なゲートウェイ    | ネットワークまたはサーバー側の問題です。 無効なヘッダーを示す場合もあります。

Text to Speech REST API について詳しくは、「[REST API](rest-apis.md#text-to-speech)」をご覧ください。

## <a name="next-steps"></a>次の手順

- [Speech 試用版サブスクリプションを取得する](https://azure.microsoft.com/try/cognitive-services/)
- [C++ で音声を認識する](quickstart-cpp-windows.md)
- [C# で音声を認識する](quickstart-csharp-dotnet-windows.md)
- [Java で音声を認識する](quickstart-java-android.md)
