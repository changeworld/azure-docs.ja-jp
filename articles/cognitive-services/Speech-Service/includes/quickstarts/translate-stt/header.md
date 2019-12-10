---
title: クイック スタート:音声テキスト変換を翻訳する - Speech サービス
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: cd45657beacd04eb2376af3e4297eae051157778
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816927"
---
このクイックスタートでは、[Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) を使用して、ある言語の音声を別の言語のテキストに対話的に翻訳します。 いくつかの前提条件を満たすと、次の 5 つのステップを実行するだけで音声変換を翻訳します。
> [!div class="checklist"]
> * サブスクリプション キーとリージョンから ````SpeechConfig```` オブジェクトを作成します。
> * ````SpeechConfig```` オブジェクトを更新して、ソースとターゲットの言語を指定します。
> * 上記の ````SpeechConfig```` オブジェクトを使用して ````TranslationRecognizer```` オブジェクト作成します。
> * ````TranslationRecognizer```` オブジェクトを使用して、1 つの発話の認識プロセスを開始します。
> * 返された ````TranslationRecognitionResult```` を検査します。
