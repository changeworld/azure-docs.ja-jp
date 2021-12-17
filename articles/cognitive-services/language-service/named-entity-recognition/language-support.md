---
title: 固有表現認識 (NER) 言語のサポート
titleSuffix: Azure Cognitive Services
description: この記事では、Azure Cognitive Service for Language の NER 機能でサポートされている自然言語について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-ner, ignite-fall-2021
ms.openlocfilehash: 7f80599fe9b0a43b7b472bd6eff71ef11b62fe13
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092559"
---
# <a name="named-entity-recognition-ner-language-support"></a>固有表現認識 (NER) 言語のサポート 

この記事では、Azure Cognitive Service for Language の NER 機能でサポートされている自然言語について説明します。

> [!NOTE]
> * 新しい[モデル バージョン](how-to-call.md#specify-the-ner-model)がリリースされると、言語が追加されます。 
> * \* でマークされている言語では、"Person"、"Location"、および "Organization" エンティティのみが返されます。
> * NER の現在のモデル バージョンは `2021-06-01` です。

## <a name="ner-language-support"></a>NER 言語のサポート

| Language              | 言語コード | 次のモデル バージョン以降: | メモ              |
|:----------------------|:-------------:|:----------------------------:|:------------------:|
| アラビア語*               | `ar`          | 2019-10-01                   |                    |
| 簡体中国語    | `zh-hans`     | 2021-01-15                   | `zh` も可能 |
| 繁体中国語*  | `zh-hant`     | 2019-10-01                   |                    |
| チェコ語*                | `cs`          | 2019-10-01                   |                    |
| デンマーク語*               | `da`          | 2019-10-01                   |                    |
| オランダ語*                | `nl`          | 2019-10-01                   |                    |
| 英語               | `en`          | 2019-10-01                   |                    |
| フィンランド語*              | `fi`          | 2019-10-01                   |                    |
| フランス語                | `fr`          | 2021-01-15                   |                    |
| ドイツ語                | `de`          | 2021-01-15                   |                    |
| ヘブライ語*               | `he`          | 2019-10-01                   |                    |
| ハンガリー語*            | `hu`          | 2019-10-01                   |                    |
| イタリア語               | `it`          | 2021-01-15                   |                    |
| 日本語              | `ja`          | 2021-01-15                   |                    |
| 韓国語                | `ko`          | 2021-01-15                   |                    |
| ノルウェー語 (ブークモール)*  | `no`          | 2019-10-01                   | `nb` も可能 |
| ポーランド語*               | `pl`          | 2019-10-01                   |                    |
| ポルトガル語 (ブラジル)   | `pt-BR`       | 2021-01-15                   |                    |
| ポルトガル語 (ポルトガル) | `pt-PT`       | 2021-01-15                   | `pt` も可能 |
| ロシア語*              | `ru`          | 2019-10-01                   |                    |
| スペイン語               | `es`          | 2020-04-01                   |                    |
| スウェーデン語*              | `sv`          | 2019-10-01                   |                    |
| トルコ語*              | `tr`          | 2019-10-01                   |                    |

## <a name="next-steps"></a>次のステップ

[PII 機能の概要](overview.md)
