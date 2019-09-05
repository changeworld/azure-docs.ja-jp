---
title: キー フレーズ抽出コンテナー用の docker pull
titleSuffix: Azure Cognitive Services
description: キー フレーズ抽出コンテナー用の docker pull コマンド
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: e71db5455a3d6726cd6567d568a13fcdcf9cb520
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051336"
---
## <a name="pull-the-key-phrase-extraction-container"></a>キー フレーズ抽出コンテナーをプルする

Text Analytics のコンテナー イメージは Microsoft コンテナー レジストリで入手できます。

| コンテナー | コンテナー レジストリ / リポジトリ / イメージ名 |
|-----------|------------|
| キー フレーズ抽出 | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用して Microsoft Container Registry からコンテナー イメージをダウンロードします。

Text Analytics コンテナーで使用可能なタグの詳しい説明については、Docker Hub の[キー フレーズ抽出](https://go.microsoft.com/fwlink/?linkid=2018757)コンテナーを参照してください。

### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>キー フレーズ抽出コンテナー用の docker pull

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
