---
title: クイック スタート:音声をオーディオ ファイルに合成する - Speech Service
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: f69c00f9cf787a192c62f12a707927c5c51a1d31
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504925"
---
このクイックスタートでは、[Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) を使用して、テキストをオーディオ ファイルの合成された音声に変換します。 いくつかの前提条件を満たすと、次の 5 つの手順だけで音声をファイルに合成します。
> [!div class="checklist"]
> * サブスクリプション キーとリージョンから ````SpeechConfig```` オブジェクトを作成します。
> * .WAV ファイル名を指定するオーディオ構成オブジェクトを作成します。
> * 上記の構成オブジェクトを使用して ````SpeechSynthesizer```` オブジェクトを作成します。
> * ````SpeechSynthesizer```` オブジェクトを使用して、テキストを合成された音声に変換し、指定されたオーディオ ファイルに保存します。
> * 返された ````SpeechSynthesizer```` でエラーがないか確認します。
