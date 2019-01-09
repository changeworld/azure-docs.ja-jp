---
title: リージョン - Speech Services
titlesuffix: Azure Cognitive Services
description: Speech Service のリージョンに関するリファレンスです。
services: cognitive-services
author: mahilleb-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mahilleb
ms.custom: seodec18
ms.openlocfilehash: d41213d72d40555d8dc5aeab76040fc556dae774
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53091647"
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

Speech SDK の**意図認識**は、リージョンのサポートを LUIS と共有します。 使用できるリージョンの詳細な一覧については、[公開リージョンとエンドポイント - LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions) に関するページを参照してください。

Speech SDK 経由で**意図認識**に使用可能なリージョンは、[Language Understanding サービスのリージョンのページ](/azure/cognitive-services/luis/luis-reference-regions)に一覧表示されています。

一覧の各公開リージョンについては、提供されている **API リージョン名**を使用します。 たとえば、米国西部の場合は `westus` を使用します。

## <a name="rest-apis"></a>REST API

Speech Service は、音声テキスト変換要求とテキスト読み上げ要求用の REST エンドポイントも公開しています。

### <a name="speech-to-text"></a>音声テキスト変換

音声テキスト変換のリファレンス ドキュメントについては、[REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#speech-to-text) に関するページを参照してください。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>テキスト読み上げ

テキスト読み上げのリファレンス ドキュメントについては、[REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#speech-to-text) に関するページを参照してください。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
