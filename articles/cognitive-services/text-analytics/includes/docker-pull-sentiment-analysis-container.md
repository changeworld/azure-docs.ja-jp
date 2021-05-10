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
ms.openlocfilehash: 32a550e120331a8255281d51725d2d5fc8ca1e05
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564508"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>感情分析コンテナー v3 用の docker pull

複数の言語で、感情分析コンテナー v3 のコンテナーを使用できます。 英語のコンテナー用のコンテナーをダウンロードするには、次のコマンドを使用します。 

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en
```

別の言語用のコンテナーをダウンロードするには、`en` を次のいずれかの言語コードに置き換えます。 

| Text Analytics コンテナー | 言語コード |
|--|--|
| 簡体中国語    |   `zh-hans`   |
| 繁体字中国語   |   `zh-hant`   |
| オランダ語                 |     `nl`      |
| 英語               |     `en`      |
| フランス語                |     `fr`      |
| ドイツ語                |     `de`      |
| ヒンディー語                 |    `hi`       |
| イタリア語               |     `it`      |
| 日本語              |     `ja`      |
| 韓国語                |     `ko`      |
| ノルウェー語 (ブークモール)   |     `no`      |
| ポルトガル語 (ブラジル)   |    `pt-BR`    |
| ポルトガル語 (ポルトガル) |    `pt-PT`    |
| スペイン語               |     `es`      |
| トルコ語               |     `tr`      |

Text Analytics コンテナーで使用可能なタグの詳しい説明については、[Docker Hub](https://go.microsoft.com/fwlink/?linkid=2018654) に関する記事を参照してください。
