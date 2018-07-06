---
title: 音声認識サービスのリージョン | Microsoft Docs
description: 音声認識サービスのリージョンに関するリファレンスです。
services: cognitive-services
author: mahilleb-msft
manager: wolmfa61
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 06/27/2018
ms.author: mahilleb
ms.openlocfilehash: a201cc043f673e2285ea48950804d97b96f881ed
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054943"
---
# <a name="regions-and-endpoints-of-the-speech-service"></a>音声認識サービスのリージョンとエンドポイント

> [!NOTE]
> [Speech SDK](speech-sdk.md) のリージョン名は、以下に示すエンドポイントのドメインの最初の部分と一致します。
> たとえば、Speech SDK で米国西部リージョンを指定するには、`westus` を使用します。

## <a name="speech-to-text"></a>音声テキスト変換

[!include[](includes/endpoints-speech-to-text.md)]

## <a name="text-to-speech"></a>テキストから音声へ

[!include[](includes/endpoints-text-to-speech.md)]

## <a name="authentication"></a>認証

[!include[](includes/endpoints-token-service.md)]

承認トークンの取得と更新について詳しくは、[こちら](rest-apis.md#authentication)をご覧ください。

## <a name="language-understanding-speech-sdk-only"></a>Language Understanding (Speech SDK のみ)

Language Understanding サービスのリージョンの一覧は、[こちら](/azure/cognitive-services/luis/luis-reference-regions)をご覧ください。
Speech SDK では、エンドポイントのドメイン名の最初の部分でリージョンを指定します (例: `westus`)。
