---
title: クイック スタート:オーディオ ファイルから音声を認識する - Speech サービス
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: dapine
ms.openlocfilehash: 2b6270535c0cf69549a7412bd38d9207454e5500
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "76037941"
---
このクイックスタートでは、[Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) を使用して、オーディオ ファイルから音声を認識します。 いくつかの前提条件を満たすと、わずかなステップを実行するだけでファイルの音声が認識されます。
> [!div class="checklist"]
> * サブスクリプション キーとリージョンから `SpeechConfig` オブジェクトを作成します。
> * .WAV ファイル名を指定する `AudioConfig` オブジェクトを作成します。
> * 上記の `SpeechConfig` および `AudioConfig` オブジェクトを使用して `SpeechRecognizer` オブジェクトを作成します。
> * `SpeechRecognizer` オブジェクトを使用して、1 つの発話の認識プロセスを開始します。
> * 返された `SpeechRecognitionResult` を検査します。
