---
title: 感情分析コンテナー用の docker pull
titleSuffix: Azure Cognitive Services
description: 感情分析コンテナー用の docker pull コマンド
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a3db0e2ffdd4a75f02634ca2227c3c41416d4f65
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588398"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>感情分析コンテナー v3 用の docker pull

複数の言語で、感情分析コンテナー v3 のコンテナーを使用できます。 英語のコンテナー用のコンテナーをダウンロードするには、次のコマンドを使用します。 

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-en
```

別の言語用のコンテナーをダウンロードするには、`en` を次のいずれかの言語コードに置き換えます。 

| Text Analytics コンテナー | 言語コード |
|--|--|
| 英語 | `en` |
| スペイン語 | `es` |
| フランス語 | `fr` |
| イタリア語 | `it` |
| ドイツ語 | `de` |
| 簡体中国語 | `zh` |
| 繁体中国語 | `zht` |
| 日本語 | `ja` |
| Portuguese | `pt` |
| オランダ語 | `nl` |

Text Analytics コンテナーで使用可能なタグの詳しい説明については、[Docker Hub](https://go.microsoft.com/fwlink/?linkid=2018654) に関する記事を参照してください。