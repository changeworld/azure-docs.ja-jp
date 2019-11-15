---
title: 言語の概念 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker は、さまざまな言語のナレッジ ベース コンテンツをサポートしています。 ただし、1 つの言語につき 1 つ QnA Maker サービスを確保する必要があります。 特定の QnA Maker サービスの言語は、そのサービスを対象に作成された 1 つ目のナレッジ ベースによって設定されます。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: diberry
ms.openlocfilehash: a818d84bf3df712190ccc6c1721c26933a9fbcdb
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2019
ms.locfileid: "73793936"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>QnA Maker のナレッジ ベース コンテンツの言語サポート

サービスの言語は、リソースに最初のナレッジ ベースを作成するときに選択します。 そのリソースに追加するすべてのナレッジ ベースは、同じ言語である必要があります。 

言語により、ユーザー クエリに対する応答で QnA Maker によって提供される結果の関連性が決まります。

## <a name="one-language-for-all-knowledge-bases-in-resource"></a>リソース内のすべてのナレッジ ベースに対する 1 つの言語

QnA Maker では、最初のナレッジ ベースを作成するときに、QnA サービスの言語を選択できます。 QnA Maker リソース内のすべてのナレッジ ベースで、同じ言語が使用される必要があります。 言語を変更することはできません。

1 つのリソースで異なる言語を使用してナレッジ ベースを作成すると、ユーザー クエリへの応答で QnA Maker によって提供される結果の関連性に悪影響を及ぼします。

[サポートされている言語](../overview/language-support.md#languages-supported)の一覧と、言語が[マッチングと関連性](#query-matching-and-relevance)に与える影響について、確認してください。 

## <a name="select-language-when-creating-first-knowledge-base"></a>最初のナレッジ ベースを作成するときに言語を選択する

言語の選択は、リソースに最初のナレッジ ベースを作成する手順の一部です。 

![最初のナレッジ ベースの言語を選択する QnA Maker ポータルのスクリーンショット](../media/language-support/select-language-when-creating-knowledge-base.png)

## <a name="query-matching-and-relevance"></a>クエリの一致と関連性
QnA Maker による結果の提供は、[Azure Cognitive Search の言語アナライザー](https://docs.microsoft.com/rest/api/searchservice/language-support)に依存してします。 

Azure Cognitive Search の機能はサポートされている言語に従いますが、QnA Maker には Azure Search の結果より上位に追加のランカーがあります。 このランカー モデルでは、以下の言語においていくつかの特別なセマンティックとワード ベースの機能が使用されます。 

|追加のランカーがある言語|
|--|
|中国語|
|チェコ語|
|オランダ語|
|英語|
|フランス語|
|ドイツ語|
|ハンガリー語|
|イタリア語|
|日本語|
|韓国語|
|ポーランド語|
|ポルトガル語|
|スペイン語|
|スウェーデン語|

この追加のランキングは、QnA Maker のランカーの内部処理です。

## <a name="verify-language"></a>言語を検証する

QnA Maker リソースの言語は、QnA Maker のサービス設定ページで確認できます。

![QnA Maker ポータルのサービス設定ページのスクリーンショット](../media/language-support/language-knowledge-base.png) 


## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Bot Service による QnA ボットの作成](../Tutorials/create-qna-bot.md)
