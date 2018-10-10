---
title: 言語サポート - Text Analytics API
titleSuffix: Azure Cognitive Services
description: Text Analytics API でサポートされる自然言語の一覧。 この記事では、各操作 (感情分析、キー フレーズ抽出、言語検出) でサポートされている言語について説明します。
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: article
ms.date: 09/25/2018
ms.author: ashmaka
ms.openlocfilehash: e9f466ac6bce98a6a9f2d79a443c9602ca40bb26
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2018
ms.locfileid: "47435373"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Text Analytics API の言語と地域のサポート

この記事では、各操作 (感情分析、キー フレーズ抽出、言語検出) でサポートされている言語について説明します。

## <a name="language-detection"></a>言語検出

Text Analytics API は最大 120 の異なる言語を検出できます。 言語検出は言語の "スクリプト" を返します。 たとえば、"I have a dog" という表現の場合、`en-US` ではなく `en` が返されます。 唯一の特殊なケースは中国語で、テキストが与えられたときスクリプトを決定できる場合、言語検出機能は `zh_CHS` または `zh_CHT` を返します。 中国語のドキュメントについて、特定のスクリプトを識別できない場合、`zh` が返されます。

## <a name="sentiment-analysis-key-phrase-extraction-and-entity-linking"></a>感情分析、キー フレーズ抽出、エンティティ リンク設定

感情分析、キー フレーズ抽出、エンティティ リンク設定については、追加言語の言語規則に対応する目的でアナライザーの精度が上げられるため、サポートされる言語の一覧は対象がさらにしぼられます。

## <a name="language-list-and-status"></a>言語の一覧と状態

言語サポートは、互いに関係なく、また、Text Analytics サービス全体に関係なく、最初にプレビューでロールアウトされ、徐々に一般公開 (GA) 状態に移行されます。 Text Analytics API が一般公開に移っているときでも、言語をプレビューのままにすることができます。

| Language    | 言語コード | センチメント | キー フレーズ | Entity Linking |   メモ  |
|:----------- |:-------------:|:---------:|:-----------:|:-----------:|:-----------:
| デンマーク語      | `da`          | ✔ \*     | ✔           |             |     |
| オランダ語       | `nl`          | ✔ \*     | ✔          |             |     |
| 英語     | `en`          | ✔        | ✔           |  ✔ \*   |      |
| フィンランド語     | `fi`          | ✔ \*     | ✔           |             |     |
| フランス語      | `fr`          | ✔        | ✔           |             |     |
| ドイツ語      | `de`          | ✔ \*     | ✔           |            |     |
| ギリシャ語       | `el`          | ✔ \*     |             |            |     |
| イタリア語     | `it`          | ✔ \*     | ✔           |             |     |
| 日本語    | `ja`          |          | ✔           |            |     |
| 韓国語      | `ko`          |          | ✔           |            |     |
| ノルウェー語 (ブークモール) | `no`          | ✔ \*     |  ✔          |             |     |
| ポーランド語      | `pl`          | ✔ \*     |  ✔          |             |     |
| ポルトガル語 (ポルトガル) | `pt-PT`| ✔        |  ✔          |       |`pt` も可能|
| ポルトガル語 (ブラジル)   | `pt-BR`|          |  ✔   |         |     |
| ロシア語     | `ru`          | ✔ \*     | ✔           |             |     |
| スペイン語     | `es`          | ✔        | ✔           |     |     |
| スウェーデン語     | `sv`          | ✔ \*     | ✔           |             |     |
| トルコ語     | `tr`          | ✔ \*     |             |             |     |

\* はプレビュー状態の言語サポートを示します

## <a name="see-also"></a>関連項目

[Cognitive Services のドキュメント ページ](https://docs.microsoft.com/azure/cognitive-services/)   
[Cognitive Services の製品ページ](https://azure.microsoft.com/services/cognitive-services/)
