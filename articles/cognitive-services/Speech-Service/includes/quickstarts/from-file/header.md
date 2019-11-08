---
title: クイック スタート:オーディオ ファイルから音声を認識する - Speech Service
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
ms.openlocfilehash: bbd3880ea679dc5fc86c0fc1ece101ca3fca74d9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506285"
---
このクイックスタートでは、[Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) を使用して、オーディオ ファイルから音声を認識します。 いくつかの前提条件を満たすと、5 つのステップを実行するだけでファイルから音声を認識します。
> [!div class="checklist"]
> * サブスクリプション キーとリージョンから ````SpeechConfig```` オブジェクトを作成します。
> * .WAV ファイル名を指定する ````AudioConfig```` オブジェクトを作成します。
> * 上記の ````SpeechConfig```` および ````AudioConfig```` オブジェクトを使用して ````SpeechRecognizer```` オブジェクトを作成します。
> * ````SpeechRecognizer```` オブジェクトを使用して、1 つの発話の認識プロセスを開始します。
> * 返された ````SpeechRecognitionResult```` を検査します。
