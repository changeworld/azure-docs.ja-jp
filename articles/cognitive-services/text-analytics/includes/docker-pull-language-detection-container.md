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
ms.openlocfilehash: 3444d6267c09056697abf8339cc688a606b6dc17
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877107"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>言語検出コンテナー用の docker pull

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用して Microsoft Container Registry からコンテナー イメージをダウンロードします。

Text Analytics コンテナーで使用可能なタグの詳しい説明については、Docker Hub の[言語検出](https://go.microsoft.com/fwlink/?linkid=2018759)コンテナーを参照してください。

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
