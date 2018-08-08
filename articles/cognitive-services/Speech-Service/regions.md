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
ms.openlocfilehash: 082002b25b02e1e496221f4686d0e636630dd438
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39324393"
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

リージョン| Speech SDK でのリージョン パラメーターの値
-|-
米国西部| `westus`
東アジア| `eastasia`
北ヨーロッパ| `northeurope`

### <a name="regions-for-intent-recognition"></a>意図認識のリージョン

Speech SDK 経由で**意図認識**に使用可能なリージョンは、[Language Understanding サービスのリージョンのページ](/azure/cognitive-services/luis/luis-reference-regions)に一覧表示されています。
一覧表示されている公開リージョンごとに、対応する Speech SDK リージョン パラメーターがエンドポイントのドメイン名の最初の部分として決定されます。
たとえば、米国西部の公開リージョンを指定するには `westus` を使用します。
