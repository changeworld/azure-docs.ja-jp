---
title: クイック スタート:音声を合成する - Speech サービス
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
ms.openlocfilehash: d19f779f67cc0dea8cc7f06aa275885d75c3092e
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818215"
---
このクイックスタートでは、[Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) を使用して、テキストを合成された音声に変換します。 いくつかの前提条件を満たすと、次の 4 つの手順だけで合成された音声を既定のスピーカーにレンダリングします。
> [!div class="checklist"]
> * サブスクリプション キーとリージョンから ````SpeechConfig```` オブジェクトを作成します。
> * 上記の ````SpeechConfig```` オブジェクトを使用して ````SpeechSynthesizer```` オブジェクトを作成します。
> * ````SpeechSynthesizer```` オブジェクトを使用してテキストを読み上げる
> * 返された ````SpeechSynthesisResult```` でエラーがないか確認する
