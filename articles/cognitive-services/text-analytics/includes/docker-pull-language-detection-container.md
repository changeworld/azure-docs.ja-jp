---
title: 言語検出コンテナー用の docker pull
titleSuffix: Azure Cognitive Services
description: 言語検出コンテナー用の docker pull コマンド
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: da6f04f1042d1a02178f345c5fe67387ae0a7d0f
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051296"
---
## <a name="pull-the-language-detection-container"></a>言語検出コンテナーをプルする

Text Analytics のコンテナー イメージは Microsoft コンテナー レジストリで入手できます。

| コンテナー | コンテナー レジストリ / リポジトリ / イメージ名 |
|-----------|------------|
| 言語検出 | `mcr.microsoft.com/azure-cognitive-services/language` |

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用して Microsoft Container Registry からコンテナー イメージをダウンロードします。

Text Analytics コンテナーで使用可能なタグの詳しい説明については、Docker Hub の[言語検出](https://go.microsoft.com/fwlink/?linkid=2018759)コンテナーを参照してください。


### <a name="docker-pull-for-the-language-detection-container"></a>言語検出コンテナー用の docker pull

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
