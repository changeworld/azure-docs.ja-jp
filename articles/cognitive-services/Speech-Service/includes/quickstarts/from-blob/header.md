---
title: クイック スタート:BLOB ストレージに格納された音声を認識する - Speech Service
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
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 195c4c94f7ab00a8740c9dd14aad0d0fd0daa9b7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506101"
---
このクイックスタートでは、[バッチ文字起こし REST API](../../../batch-transcription.md) を使用して、[SAS BLOB](https://aka.ms/ignite2019/speech/placeholder) に格納された音声を認識します。 いくつかの前提条件を満たすと、わずかな手順で REST API を使用して音声を認識できます。
> [!div class="checklist"]
> * JSON 要求を Speech Service に送信して、音声の文字起こしを開始する。
> * 文字起こしの状態を確認する。
> * 完了したら、文字起こしの結果をダウンロードする。