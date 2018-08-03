---
title: 音声認識サービスのリージョン | Microsoft Docs
description: 音声認識サービスのリージョンに関するリファレンスです。
services: cognitive-services
author: mahilleb-msft
manager: wolmfa61
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 06/28/2018
ms.author: mahilleb
ms.openlocfilehash: 11360d163fdba057d373d091d46903cde7789a8b
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071421"
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

次の表は、**音声認識**と**翻訳**に使用可能なリージョンを一覧表示しています。

リージョン| Speech SDK でのリージョン パラメーターの値
-|-
米国西部| `westus`
東アジア| `eastasia`
北ヨーロッパ| `northeurope`

Speech SDK 経由で**意図認識**に使用可能なリージョンは、[Language Understanding サービスのリージョンのページ](/azure/cognitive-services/luis/luis-reference-regions)に一覧表示されています。
一覧表示されている公開リージョンごとに、対応する Speech SDK リージョン パラメーターがエンドポイントのドメイン名の最初の部分として決定されます。
たとえば、米国西部の公開リージョンを指定するには `westus` を使用します。
