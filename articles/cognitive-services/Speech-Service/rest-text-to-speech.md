---
title: Text to Speech API リファレンス (REST) - Speech Services
titleSuffix: Azure Cognitive Services
description: Text to Speech REST API の使用方法について説明します。 この記事では、認可のオプションとクエリのオプション、さらに要求を構築する方法と応答を受信する方法について説明します。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: d67d8462c177d19dfa3cebbd0b4b000fbe3f41b8
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/14/2019
ms.locfileid: "57894953"
---
# <a name="text-to-speech-rest-api"></a>Text to Speech REST API

Speech Services では、REST API を使用してテキストを音声に変換できます。 アクセス可能な各エンドポイントは、リージョンに関連付けられています。 アプリケーションには、使用を検討しているエンドポイントのサブスクリプション キーが必要となります。 Text to Speech REST API ではニューラルと標準のテキスト読み上げ音声が提供され、そのいずれでもロケールで識別される特定の言語と方言がサポートされています。

* 音声の全一覧については、[言語のサポート](language-support.md#text-to-speech)に関するページを参照してください。
* リージョン別の提供状況については、[リージョン](regions.md#text-to-speech)に関するページを参照してください。

> [!IMPORTANT]
> 料金は、標準音声、カスタム音声、ニューラル音声によって異なります。 詳細については、[価格](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)に関するページをご覧ください。

この API を使用する前に、次の点を理解しておいてください。

* Text to Speech REST API には、Authorization ヘッダーが必要です。 つまり、このサービスにアクセスするためには、トークンの交換が済んでいる必要があります。 詳細については、[認証](#authentication)に関するページをご覧ください。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="request-headers"></a>要求ヘッダー

この表は、音声テキスト変換要求の必須のヘッダーと省略可能なヘッダーの一覧です。

| ヘッダー | 説明 | 必須/省略可能 |
|--------|-------------|---------------------|
| `Authorization` | 単語 `Bearer` が前に付いた認証トークン。 詳細については、[認証](#authentication)に関するページをご覧ください。 | 必須 |
| `Content-Type` | 指定したテキストのコンテンツ タイプを指定します。 指定できる値は `application/ssml+xml` です。 | 必須 |
| `X-Microsoft-OutputFormat` | オーディオ出力形式を指定します。 指定できる値の全一覧については、[オーディオ出力](#audio-outputs)に関するセクションを参照してください。 | 必須 |
| `User-Agent` | アプリケーションの名前。 指定する値は 255 文字未満にする必要があります。 | 必須 |

## <a name="audio-outputs"></a>オーディオ出力

以下に示したのは、それぞれの要求の `X-Microsoft-OutputFormat` ヘッダーで送信することができるオーディオ形式の一覧です。 それぞれビットレートとエンコードの種類が含まれています。 Speech Services では、24 kHz、16 kHz、および 8 kHz のオーディオ出力がサポートされます。

|||
|-|-|
| `raw-16khz-16bit-mono-pcm` | `raw-8khz-8bit-mono-mulaw` |
| `riff-8khz-8bit-mono-alaw` | `riff-8khz-8bit-mono-mulaw` |
| `riff-16khz-16bit-mono-pcm` | `audio-16khz-128kbitrate-mono-mp3` |
| `audio-16khz-64kbitrate-mono-mp3` | `audio-16khz-32kbitrate-mono-mp3` |
| `raw-24khz-16bit-mono-pcm` | `riff-24khz-16bit-mono-pcm` |
| `audio-24khz-160kbitrate-mono-mp3` | `audio-24khz-96kbitrate-mono-mp3` |
| `audio-24khz-48kbitrate-mono-mp3` | |

> [!NOTE]
> 選択した音声と出力形式のビット レートが異なる場合、オーディオは必要に応じて再サンプリングされます。 ただし、24 khz の音声は `audio-16khz-16kbps-mono-siren` や `riff-16khz-16kbps-mono-siren` の出力形式をサポートしていません。

## <a name="request-body"></a>要求本文

各 `POST` 要求の本文は[音声合成マークアップ言語 (SSML)](speech-synthesis-markup.md) として送信されます。 SSML では、テキスト読み上げサービスによって返される合成音声の声と言語を選択できます。 サポートされている声の全一覧については、[言語のサポート](language-support.md#text-to-speech)に関するページを参照してください。

> [!NOTE]
> カスタムの音声を使用する場合は、プレーン テキスト (ASCII または UTF-8) として要求の本文を送信できます。

## <a name="sample-request"></a>要求のサンプル

この HTTP 要求は、SSML を使用して音声と言語を指定します。 1,000 文字を超える本文は許可されません。

```http
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

言語固有の例については、クイック スタートを参照してください。

* [.NET Core、C#](quickstart-dotnet-text-to-speech.md)
* [Python](quickstart-python-text-to-speech.md)
* [Node.js](quickstart-nodejs-text-to-speech.md)

## <a name="http-status-codes"></a>HTTP 状態コード

各応答の HTTP 状態コードは、成功または一般的なエラーを示します。

| HTTP 状態コード | 説明 | 考えられる理由 |
|------------------|-------------|-----------------|
| 200 | OK | 要求は成功しました。応答本文はオーディオ ファイルです。 |
| 400 | 正しくない要求 | 必須パラメーターが指定されていない、空、または null です。 または、必須またはオプションのパラメーターに渡された値が無効です。 よくある問題はヘッダーが長すぎることです。 |
| 401 | 権限がありません | 要求が承認されていません。 サブスクリプション キーまたはトークンが有効であり、正しいリージョンにあることを確認してください。 |
| 413 | 要求のエンティティが大きすぎます | SSML 入力が 1024 文字を超えています |
| 429 | 要求が多すぎます | 使用中のサブスクリプションで許可されている要求のクォータまたは速度を超えています。 |
| 502 | 無効なゲートウェイ | ネットワークまたはサーバー側の問題です。 無効なヘッダーを示す場合もあります。 |

HTTP ステータスが `200 OK` の場合、応答の本文には要求された形式のオーディオ ファイルが含まれています。 このファイルは、転送と同時に再生することも、バッファーまたはファイルに保存することもできます。

## <a name="next-steps"></a>次の手順

- [Speech 試用版サブスクリプションを取得する](https://azure.microsoft.com/try/cognitive-services/)
- [音響モデルをカスタマイズする](how-to-customize-acoustic-models.md)
- [言語モデルをカスタマイズする](how-to-customize-language-model.md)
