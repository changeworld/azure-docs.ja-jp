---
title: 言語検出コンテナー用の docker pull
titleSuffix: Azure Cognitive Services
description: 言語検出コンテナー用の docker pull コマンド
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 95bcb4b424010f63ac1ee4eb02f9e4793647051a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90905996"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>言語検出コンテナー用の docker pull

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用して Microsoft Container Registry からコンテナー イメージをダウンロードします。

Text Analytics コンテナーで使用可能なタグの詳しい説明については、Docker Hub の[言語検出](https://go.microsoft.com/fwlink/?linkid=2018759)コンテナーを参照してください。

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/language:latest
```
