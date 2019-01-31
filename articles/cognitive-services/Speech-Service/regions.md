---
title: リージョン - Speech Services
titlesuffix: Azure Cognitive Services
description: Speech Service のリージョンに関するリファレンスです。
services: cognitive-services
author: mahilleb-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: mahilleb
ms.custom: seodec18
ms.openlocfilehash: 32a8b4cbfd0d8b43d294155432a0feaf29036c4c
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55218140"
---
# <a name="speech-service-supported-regions"></a>Speech Service がサポートされているリージョン

Speech Service を使用すると、アプリケーションで音声をテキストに変換し、音声翻訳を実行し、テキストを音声に変換することができます。 このサービスは、Speech SDK および REST API に固有のエンドポイントを使用して複数のリージョンで利用できます。

必ず、ご利用のサブスクリプションのリージョンと一致するエンドポイントを選択してください。

## <a name="speech-sdk"></a>Speech SDK

[Speech Service SDK](speech-sdk.md) では、リージョンは文字列として (たとえば、C# 用の Speech SDK では `SpeechConfig.FromSubscription` へのパラメーターとして) 指定されます。

### <a name="speech-recognition-and-translation"></a>音声認識と翻訳

Speech SDK は、**音声認識**と**翻訳**のために以下のリージョンで使用できます。

  リージョン | Speech SDK パラメーター | 音声カスタマイズ ポータル
 ------|-------|--------
 米国西部 | `westus` | https://westus.cris.ai
 米国西部 2 | `westus2` | https://westus2.cris.ai
 米国東部 | `eastus` | https://eastus.cris.ai
 米国東部 2 | `eastus2` | https://eastus2.cris.ai
 東アジア | `eastasia` | https://eastasia.cris.ai
 東南アジア | `southeastasia` | https://southeastasia.cris.ai
 北ヨーロッパ | `northeurope` | https://northeurope.cris.ai
 西ヨーロッパ | `westeurope` | https://westeurope.cris.ai


### <a name="intent-recognition"></a>意図認識

Speech SDK を介して**意図認識**を使用できるリージョンは以下の通りです。

 グローバル リージョン | リージョン | Speech SDK パラメーター
 ------|-------|--------
 アジア | 東アジア | `eastasia`
 アジア | 東南アジア | `southeastasia`
 オーストラリア | オーストラリア東部 | `australiaeast`
 ヨーロッパ | 北ヨーロッパ | `northeurope`
 ヨーロッパ | 西ヨーロッパ | `westeurope`
 北米 | 米国東部 | `eastus`
 北米 | 米国東部 2 | `eastus2`
 北米 | 米国中南部 | `southcentralus`
 北米 | 米国中西部 | `westcentralus`
 北米 | 米国西部 | `westus`
 北米 | 米国西部 2 | `westus2`
 南アメリカ | ブラジル南部 | `brazilsouth`

これは、[Language Understanding サービス (LUIS)](/azure/cognitive-services/luis/luis-reference-regions) でサポートされている公開リージョンのサブセットです。

## <a name="rest-apis"></a>REST API

Speech Service は、音声テキスト変換要求とテキスト読み上げ要求用の REST エンドポイントも公開しています。

### <a name="speech-to-text"></a>音声テキスト変換

音声テキスト変換のリファレンス ドキュメントについては、[REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) に関するページを参照してください。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>テキスト読み上げ

テキスト読み上げのリファレンス ドキュメントについては、[REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) に関するページを参照してください。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
