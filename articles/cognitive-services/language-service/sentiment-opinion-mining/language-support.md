---
title: 感情分析とオピニオン マイニングの言語サポート
titleSuffix: Azure Cognitive Services
description: この記事では、Azure Cognitive Service for Language の感情分析とオピニオン マイニングの機能でサポートされている自然言語について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/08/2021
ms.author: aahi
ms.custom: language-service-sentiment-opinion-mining, ignite-fall-2021
ms.openlocfilehash: a0cbc5bab15e80bb18ca3d4a13efa2d05fe5aeff
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027149"
---
# <a name="sentiment-analysis-and-opinion-mining-language-support"></a>感情分析とオピニオン マイニングの言語サポート 

この記事では、感情分析とオピニオン マイニングでサポートされている自然言語について説明します。

> [!NOTE]
> 新しいモデル バージョンがリリースされると、言語が追加されます。 感情分析の現在の一般公開モデル バージョンは `2020-04-01` です。 最新のプレビュー モデル バージョン (`2021-10-01-preview`) を使用するには、API 呼び出しで指定します。 詳細については、[API の呼び出し方法](how-to/call-api.md#specify-the-sentiment-analysis-model)に関するページを参照してください。

## <a name="sentiment-analysis-language-support"></a>感情分析の言語サポート

| Language              | 言語コード | 次のモデル バージョン以降: |              Notes |
|:----------------------|:-------------:|:--------------------------:|-------------------:|
| 簡体中国語    |   `zh-hans`         |         2019-10-01         | `zh` も可能 |
| 繁体字中国語   |   `zh-hant`         |         2019-10-01         |                    |
| オランダ語                 |     `nl`            |         2019-10-01        |                    |
| 英語               |     `en`            |         2019-10-01         |                    |
| フランス語                |     `fr`            |         2019-10-01         |                    |
| ドイツ語                |     `de`            |         2019-10-01         |                    |
| ヒンディー語                 |    `hi`             |         2020-04-01         |                    |
| イタリア語               |     `it`            |         2019-10-01         |                    |
| 日本語              |     `ja`            |         2019-10-01         |                    |
| 韓国語                |     `ko`            |         2019-10-01         |                    |
| ノルウェー語 (ブークモール)   |     `no`            |         2020-04-01         |                    |
| ポルトガル語 (ブラジル)   |    `pt-BR`          |         2020-04-01         |                    |
| ポルトガル語 (ポルトガル) |    `pt-PT`          |         2019-10-01         | `pt` も可能 |
| スペイン語               |     `es`            |         2019-10-01         |                    |
| トルコ語               |     `tr`             |         2020-04-01        |                    |

### <a name="opinion-mining-language-support"></a>オピニオン マイニングの言語サポート

| Language              | 言語コード | 次のモデル バージョン以降: |              Notes |
|:----------------------|:-------------:|:------------------------------------:|-------------------:|
| 英語               |     `en`      |  2020-04-01              |                    |
| フランス語                |     `fr`      |         2021-10-01-preview         |                    |
| ドイツ語                |     `de`      |         2021-10-01-preview         |                    |
| イタリア語               |     `it`      |         2021-10-01-preview         |                    |
| ポルトガル語 (ブラジル)   |    `pt-BR`    |         2021-10-01-preview         |                    |
| ポルトガル語 (ポルトガル) |    `pt-PT`    |         2021-10-01-preview         | `pt` も可能 |
| スペイン語               |     `es`      |         2021-10-01-preview         |                    |

## <a name="next-steps"></a>次のステップ

[感情分析とオピニオン マイニングの概要](overview.md)
