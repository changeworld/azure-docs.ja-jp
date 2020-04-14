---
title: キー フレーズ抽出コンテナー用の docker pull
titleSuffix: Azure Cognitive Services
description: キー フレーズ抽出コンテナー用の docker pull コマンド
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: db5d0cfecde32d3dd6afe181d67a6ce6c0826eda
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877127"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>キー フレーズ抽出コンテナー用の docker pull

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用して Microsoft Container Registry からコンテナー イメージをダウンロードします。

Text Analytics コンテナーで使用可能なタグの詳しい説明については、Docker Hub の[キー フレーズ抽出](https://go.microsoft.com/fwlink/?linkid=2018757)コンテナーを参照してください。

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
