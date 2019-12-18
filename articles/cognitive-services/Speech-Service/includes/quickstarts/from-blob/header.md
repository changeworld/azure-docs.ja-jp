---
title: クイック スタート:BLOB ストレージに格納された音声を認識する - Speech サービス
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
ms.openlocfilehash: ef61139056ad194d89e2a67cb4ac1b1414a3380b
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74828746"
---
このクイックスタートでは、[バッチ文字起こし REST API](../../../batch-transcription.md) を使用して、[SAS BLOB](https://aka.ms/ignite2019/speech/placeholder) に格納された音声を認識します。 いくつかの前提条件を満たすと、わずかな手順で REST API を使用して音声を認識できます。
> [!div class="checklist"]
> * JSON 要求を Speech サービスに送信して、音声の文字起こしを開始する。
> * 文字起こしの状態を確認する。
> * 完了したら、文字起こしの結果をダウンロードする。