---
title: 音声サービスのリージョン
description: 音声認識サービスのリージョンに関するリファレンスです。
services: cognitive-services
author: mahilleb-msft
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 06/28/2018
ms.author: mahilleb
ms.openlocfilehash: 1cb00035dc8f1cdeabd1beb22ca69f47bf4bd89e
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2018
ms.locfileid: "44379572"
---
# <a name="regions-of-the-speech-service"></a>音声サービスのリージョン

音声サービスは、さまざまなリージョンで使用できます。
サブスクリプションを作成する場合は、ニーズに応じて、使用可能なリージョンを選択できます。

そのサブスクリプションを使用する場合は、選択したリージョンを考慮する必要があります。

## <a name="rest-api"></a>REST API

REST API を使用して、適切なリージョン固有のエンドポイントを選択します。
詳細については、「[REST API](rest-apis.md)」を参照してください。

## <a name="speech-sdk"></a>Speech SDK

[Speech SDK](speech-sdk.md) では、リージョンは文字列として (たとえば、C# 用の Speech SDK では [SpeechFactory.FromSubscription](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechfactory.fromsubscription) へのパラメーターとして) 指定されます。

### <a name="regions-for-speech-recognition-and-translation"></a>音声認識と翻訳のリージョン

次の表は、**音声認識**と**翻訳**に使用可能なリージョンを一覧表示しています。

リージョン| Speech SDK でのリージョン パラメーターの値| ポータル
-|-
米国西部| `westus`| https://westus.cris.ai
米国西部 2| `westus2`| https://westus2.cris.ai
米国東部| `eastus`| https://eastus.cris.ai
米国東部 2| `eastus2`| https://eastus2.cris.ai
東アジア| `eastasia`| https://eastasia.cris.ai
東南アジア| `southeastasia`| https://southeastasia.cris.ai
北ヨーロッパ| `northeurope`| https://northeurope.cris.ai
西ヨーロッパ|  `westeurope`| https://westeurope.cris.ai

### <a name="regions-for-intent-recognition"></a>意図認識のリージョン

Speech SDK 経由で**意図認識**に使用可能なリージョンは、[Language Understanding サービスのリージョンのページ](/azure/cognitive-services/luis/luis-reference-regions)に一覧表示されています。
一覧表示されている公開リージョンごとに、対応する Speech SDK リージョン パラメーターがエンドポイントのドメイン名の最初の部分として決定されます。
たとえば、米国西部の公開リージョンを指定するには `westus` を使用します。
