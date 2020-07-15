---
title: リージョン - Speech サービス
titleSuffix: Azure Cognitive Services
description: 音声テキスト変換、テキスト読み上げ、音声翻訳など、Speech サービスで利用可能なリージョンとエンドポイントのリスト。
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 27e26bb37b444b49797d46dd4e12b61f8fe11b16
ms.sourcegitcommit: 52d2f06ecec82977a1463d54a9000a68ff26b572
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2020
ms.locfileid: "84782536"
---
# <a name="speech-service-supported-regions"></a>Speech サービスがサポートされているリージョン

Speech Service を使用すると、アプリケーションで音声をテキストに変換し、音声翻訳を実行し、テキストを音声に変換することができます。 このサービスは、Speech SDK および REST API に固有のエンドポイントを使用して複数のリージョンで利用できます。

すべてのリージョンの音声エクスペリエンスにカスタム構成を実行するための Speech ポータルについては、 https://speech.microsoft.com を参照してください

Speech サービスを呼び出す場合は、呼び出しがサブスクリプションのリージョンと一致していることを確認してください。

## <a name="speech-sdk"></a>Speech SDK

[Speech SDK](speech-sdk.md) では、リージョンは文字列として (たとえば、C# 用の Speech SDK では `SpeechConfig.FromSubscription` へのパラメーターとして) 指定されます。

### <a name="speech-to-text-text-to-speech-and-translation"></a>音声変換、テキスト読み上げ、翻訳

音声カスタマイズ ポータルについては、 https://speech.microsoft.com を参照してください

Speech サービスは、以下のリージョンで**音声認識**、**テキスト読み上げ**、**翻訳**に利用できます。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

[Speech SDK](speech-sdk.md) を使用する場合、リージョンは**リージョン識別子**によって (たとえば、`SpeechConfig.FromSubscription` へのパラメーターとして) 指定されます。 リージョンがサブスクリプションのリージョンと一致していることを確認してください。

### <a name="intent-recognition"></a>意図認識

Speech SDK を介して**意図認識**を使用できるリージョンは以下の通りです。

| グローバル リージョン | リージョン           | リージョン識別子 |
| ------------- | ---------------- | -------------------- |
| アジア          | 東アジア        | `eastasia`           |
| アジア          | 東南アジア   | `southeastasia`      |
| オーストラリア     | オーストラリア東部   | `australiaeast`      |
| ヨーロッパ        | 北ヨーロッパ     | `northeurope`        |
| ヨーロッパ        | 西ヨーロッパ      | `westeurope`         |
| 北米 | 米国東部          | `eastus`             |
| 北米 | 米国東部 2        | `eastus2`            |
| 北米 | 米国中南部 | `southcentralus`     |
| 北米 | 米国中西部  | `westcentralus`      |
| 北米 | 米国西部          | `westus`             |
| 北米 | 米国西部 2        | `westus2`            |
| 南アメリカ | ブラジル南部     | `brazilsouth`        |

これは、[Language Understanding サービス (LUIS)](/azure/cognitive-services/luis/luis-reference-regions) でサポートされている公開リージョンのサブセットです。

### <a name="voice-assistants"></a>音声アシスタント

[Speech SDK](speech-sdk.md) は、以下のリージョンで**音声アシスタント**機能をサポートしています。

| リージョン         | リージョン識別子 |
| -------------- | -------------------- |
| 米国西部        | `westus`             |
| 米国西部 2      | `westus2`            |
| 米国東部        | `eastus`             |
| 米国東部 2      | `eastus2`            |
| 西ヨーロッパ    | `westeurope`         |
| 北ヨーロッパ   | `northeurope`        |
| 東南アジア | `southeastasia`      |

### <a name="speaker-recognition"></a>Speaker Recognition

現在、Speaker Recognition は `westus` リージョンでのみ使用できます。

## <a name="rest-apis"></a>REST API

Speech Service は、音声テキスト変換要求とテキスト読み上げ要求用の REST エンドポイントも公開しています。

### <a name="speech-to-text"></a>音声テキスト変換

音声テキスト変換のリファレンス ドキュメントについては、「[Speech to Text REST API](rest-speech-to-text.md)」を参照してください。

REST API のエンドポイントの形式は次のとおりです。

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

次の表に示す、ご利用のサブスクリプションのリージョンと一致する識別子で `<REGION_IDENTIFIER>` を置き換えてください。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> 4xx HTTP エラーを受け取らないためには、URL に言語パラメーターを付加する必要があります。 たとえば、米国西部エンドポイントを使用する米国英語に設定される言語は `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US` です。

### <a name="text-to-speech"></a>テキスト読み上げ

テキスト読み上げのリファレンス ドキュメントについては、「[Text to Speech REST API](rest-text-to-speech.md)」を参照してください。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
