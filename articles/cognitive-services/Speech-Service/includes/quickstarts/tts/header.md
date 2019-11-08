---
title: クイック スタート:音声を合成する - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech SDK を使用して音声を合成する方法について学習します
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 263a8e98c4c029728272c020efe00e82be20f5e2
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505077"
---
このクイックスタートでは、[Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) を使用して、テキストを合成された音声に変換します。 いくつかの前提条件を満たすと、次の 4 つの手順だけで合成された音声を既定のスピーカーにレンダリングします。
> [!div class="checklist"]
> * サブスクリプション キーとリージョンから ````SpeechConfig```` オブジェクトを作成します。
> * 上記の ````SpeechConfig```` オブジェクトを使用して ````SpeechSynthesizer```` オブジェクトを作成します。
> * ````SpeechSynthesizer```` オブジェクトを使用してテキストを読み上げる
> * 返された ````SpeechSynthesisResult```` でエラーがないか確認する
