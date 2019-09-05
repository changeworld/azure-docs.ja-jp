---
title: 感情分析コンテナー用の docker pull
titleSuffix: Azure Cognitive Services
description: 感情分析コンテナー用の docker pull コマンド
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: 0954ef88f6917159156fbb73103b7b7af3283c00
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051256"
---
## <a name="pull-the-sentiment-analysis-container"></a>感情分析コンテナーをプルする

Text Analytics のコンテナー イメージは Microsoft コンテナー レジストリで入手できます。

| コンテナー | コンテナー レジストリ / リポジトリ / イメージ名 |
|-----------|------------|
| 感情分析 | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用して Microsoft Container Registry からコンテナー イメージをダウンロードします。

Text Analytics コンテナーで使用可能なタグの詳しい説明については、Docker Hub の[感情分析](https://go.microsoft.com/fwlink/?linkid=2018654)コンテナーを参照してください。

### <a name="docker-pull-for-the-sentiment-analysis-container"></a>感情分析コンテナー用の docker pull

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
