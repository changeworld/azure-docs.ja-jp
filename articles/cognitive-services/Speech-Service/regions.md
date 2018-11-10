---
title: Speech Service のリージョン
titlesuffix: Azure Cognitive Services
description: Speech Service のリージョンに関するリファレンスです。
services: cognitive-services
author: mahilleb-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mahilleb
ms.openlocfilehash: 088e581da7511797a0f39959d867c6298262462a
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2018
ms.locfileid: "50242332"
---
# <a name="regions-of-the-speech-service"></a>Speech Service のリージョン

Speech Service は、さまざまなリージョンで使用できます。
サブスクリプションを作成する場合は、ニーズに応じて、使用可能なリージョンを選択できます。

そのサブスクリプションを使用する場合は、選択したリージョンを考慮する必要があります。

## <a name="rest-api"></a>REST API

適切なリージョン固有のエンドポイントを選択するには、REST API を使用します。
詳細については、「[REST API](rest-apis.md)」を参照してください。

## <a name="speech-sdk"></a>Speech SDK

[Speech Service SDK](speech-sdk.md) では、リージョンは文字列として (たとえば、C# 用の Speech SDK では `SpeechConfig.FromSubscription` へのパラメーターとして) 指定されます。

### <a name="regions-for-speech-recognition-and-translation"></a>音声認識と翻訳のリージョン

次の表は、**音声認識**と**翻訳**に使用可能なリージョンの一覧です。

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


### <a name="regions-for-intent-recognition"></a>意図認識のリージョン

Speech SDK 経由で**意図認識**に使用可能なリージョンは、[Language Understanding サービスのリージョンのページ](/azure/cognitive-services/luis/luis-reference-regions)に一覧表示されています。
一覧表示されている公開リージョンごとに、対応する Speech SDK リージョン パラメーターがエンドポイントのドメイン名の最初の部分として決定されます。
たとえば、米国西部の公開リージョンを指定するには `westus` を使用します。
