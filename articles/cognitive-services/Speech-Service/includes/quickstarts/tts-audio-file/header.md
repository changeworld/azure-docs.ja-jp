---
title: クイック スタート:音声をオーディオ ファイルに合成する - Speech サービス
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 9b13d8fc3b77426a59dea5399223b79c4bb4b1a1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75466728"
---
このクイックスタートでは、[Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) を使用して、テキストをオーディオ ファイルの合成された音声に変換します。 いくつかの前提条件を満たすと、次の 5 つの手順だけで音声をファイルに合成します。
> [!div class="checklist"]
> * サブスクリプション キーとリージョンから ````SpeechConfig```` オブジェクトを作成します。
> * .WAV ファイル名を指定するオーディオ構成オブジェクトを作成します。
> * 上記の構成オブジェクトを使用して ````SpeechSynthesizer```` オブジェクトを作成します。
> * ````SpeechSynthesizer```` オブジェクトを使用して、テキストを合成された音声に変換し、指定されたオーディオ ファイルに保存します。
> * 返された ````SpeechSynthesizer```` でエラーがないか確認します。
