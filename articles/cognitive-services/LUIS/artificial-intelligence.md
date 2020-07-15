---
title: 人工知能
description: LUIS は、人工知能を使用し、定義されたスキーマに基づいて、データに言語理解を提供します。
ms.topic: conceptual
ms.date: 06/29/2020
ms.openlocfilehash: 4f145585e097a3cf6a2338dbab879f6fce07f71d
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2020
ms.locfileid: "85802639"
---
# <a name="artificial-intelligence-in-language-understanding-luis"></a>Language Understanding (LUIS) における人口知能

LUIS は、人工知能を使用し、定義されたスキーマに基づいて、データに自然言語理解 (NLU) を提供します。

## <a name="natural-language-processing"></a>自然言語処理

自然言語理解 (NLU) は、自然言語処理 (NLP) の特定のサブトピックです。

自然言語処理とは、テキスト データのあらゆる形式の処理を行う、より広範な概念です。これには次のようなものが含まれます。

* トークン化
* 品詞 (pos) のタグ付け
* セグメント化
* 形態論的分析
* セマンティックの類似性
* Discourse
* 翻訳

## <a name="natural-language-processing-in-luis"></a>LUIS での自然言語処理

自然言語処理は、次の方法で LUIS アプリで使用できます。
* [自然言語理解](#natural-language-understanding) (LUIS)
* LUIS の構成可能な NLP の側面:
    * [トークン化](luis-language-support.md#tokenization)
    * 分音記号、句読点、および単語形式の [API 設定](luis-reference-application-settings.md)を使用した形態論
* 他の [Cognitive Services](../Welcome.md) によって提供されるクエリ (発話) の前または後処理。例:
    * [翻訳](../translator/translator-info-overview.md)

## <a name="natural-language-understanding"></a>自然言語の理解

NLU は、言語ステートメントを、ユーザーの言うことを自然に理解できる表現に_変換_する機能です。 自然言語理解は、依然として非常に困難な問題であり、_AI-hard (AI 困難)_ な問題として定義されています。

LUIS は、意図と抽出に重点を置くように意図されています。これには、次を識別できることが含まれます。
* ユーザーが何を欲しているか
* 彼らが何について話しているか

LUIS には、例で明示的に識別されなければ、セマンティクスの類似性などのより広範な _NLP_ の側面に関する知識は、ほとんど、またはまったくありません。 たとえば、次のトークン (単語) は、提供された例の類似コンテキストで使用されるまでは、3 つの異なるものです。

* 買う
* buying (買っている)
* bought (買った)

## <a name="next-steps"></a>次のステップ

* LUIS アプリの[開発ライフサイクル](luis-concept-app-iteration.md)について説明します。