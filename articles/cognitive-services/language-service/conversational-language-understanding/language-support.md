---
title: Conversational Language Understanding の言語サポート
titleSuffix: Azure Cognitive Services
description: この記事では、Azure Cognitive Service for Language の Conversational Language Understanding 機能でサポートされている自然言語について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: 2ef6215a4923d45796146437a59a91921f72f5bb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091134"
---
# <a name="conversational-language-understanding-language-support"></a>Conversational Language Understanding の言語サポート 

この記事では、Conversational Language Understanding 機能でサポートされている自然言語について説明します。

### <a name="supported-languages-for-conversation-projects"></a>会話プロジェクトでサポートされている言語

CLU で会話プロジェクトを作成するときに、プロジェクトのプライマリ言語を指定できます。 プライマリ言語は、プロジェクトの既定の言語として使われます。

会話プロジェクトでサポートされている言語は次のとおりです。

| **Language** | **言語コード** |
| --- | --- |
| ポルトガル語 (ブラジル) | `pt-br` |
| 中国語 | `zh-cn` |
| オランダ語 | `nl-nl` |
| 英語 | `en-us` |
| フランス語 | `fr-fr` |
| ドイツ語 | `de-de` |
| グジャラート語 | `gu-in` |
| ヒンディー語 | `hi-in` |
| イタリア語 | `it-it` |
| 日本語 | `ja-jp` |
| 韓国語 | `ko-kr` |
| マラーティー語 | `mr-in` |
| スペイン語 | `es-es` |
| タミル語 | `ta-in` |
| テルグ語 | `te-in` |
| トルコ語 | `tr-tr` |

#### <a name="multilingual-conversation-projects"></a>多言語会話プロジェクト

プロジェクトで複数の言語を有効にすると、複数の言語のデータをプロジェクトに追加できます。 また、1 つの言語でプロジェクトをトレーニングし、他の言語ですぐに予測できます。 予測の品質は、言語によって異なる場合があります。また、多言語予測に関しては、特定の言語グループが他のグループよりうまく機能します。


## <a name="supported-languages-for-orchestration-workflow-projects"></a>オーケストレーション ワークフロー プロジェクトでサポートされている言語

|Language| 言語コード |
|---|---|
| ポルトガル語 (ブラジル) | `pt-br` |
| 英語 | `en-us` |
| フランス語 | `fr-fr` |
| ドイツ語 | `de-de` |
| イタリア語 | `it-it` |
| スペイン語 | `es-es` |

オーケストレーション ワークフロー プロジェクトは、複数の言語では使用できません。

## <a name="next-steps"></a>次のステップ

[会話言語理解の概要](overview.md)
