---
title: サポートされる言語 - QnA Maker - Azure Cognitive Services | Microsoft Docs
description: QnA Maker でサポートされている言語を説明します。
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: b210f59129a962046787b27d003c2872a54f6c8e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377480"
---
# <a name="supported-languages"></a>サポートされている言語

ナレッジ ベースの言語は、[ソース](../Concepts/data-sources-supported.md)から質問と回答を自動的に抽出する QnA Maker の機能、およびユーザー クエリに対して QnA Maker が提供する結果の関連性に影響します。

## <a name="auto-extraction"></a>自動抽出
QnA Maker は任意の言語ページでの質問と回答の抽出をサポートしますが、QnA Maker は質問の識別にキーワードを使うため、抽出の有効性は次の言語の場合に非常に高くなります。

|サポートされている言語| ロケール|
|-----|----|
|英語|en-*|
|フランス語|fr-*|
|イタリア語|it-*|
|ドイツ語|de-*|
|スペイン語|es-*|

## <a name="query-matching-and-relevance"></a>クエリの一致と関連性
QnA Maker は、Azure Search の[言語アナライザー](https://docs.microsoft.com/en-us/rest/api/searchservice/language-support)に依存して結果を提供します。 En-* 言語については、関連性が向上する特別な再ランキング機能を利用できます。

QnA Maker は、作成時にナレッジ ベースの言語を自動検出し、それに応じてアナライザーを設定します。 次の言語でナレッジ ベースを作成できます。 QnA Maker による言語の処理方法について詳しくは、[こちら](../How-To/language-knowledge-base.md)をご覧ください。


> [!Tip]
> 言語アナライザーは、いったん設定されると変更できません。 また、言語アナライザーは [QnA Maker サービス](../How-To/set-up-qnamaker-service-azure.md)内のすべてのナレッジ ベースに適用されます。 異なる言語のナレッジ ベースを使用する場合は、別の QnA Maker サービスで作成する必要があります。

|サポートされている言語|
|-----|
|アラビア語|
|アルメニア語|。
ベンガル語|
|バスク語|
|ブルガリア語|
|カタルニア語|
|簡体字中国語|
|繁体字中国語|
|クロアチア語|
|チェコ語|
|デンマーク語|
|オランダ語|
|英語|
|エストニア語|
|フィンランド語|
|フランス語|
|ガリシア語|
|ドイツ語|
|ギリシャ語|
|グジャラート語|
|ヘブライ語|
|ヒンディー語|
|ハンガリー語|
|アイスランド語|
|インドネシア語|
|アイルランド語|
|イタリア語|
|日本語|
|カンナダ語|
|韓国語|
|ラトビア語|
|リトアニア語|
|マラヤーラム語|
|マレー語|
|ノルウェー語|
|ポーランド語|
|ポルトガル語|
|パンジャーブ語|
|ルーマニア語|
|ロシア語|
|セルビア語 (キリル)|
|セルビア語 (ラテン)|
|スロバキア語|
|スロベニア語|
|スペイン語|
|スウェーデン語|
|タミール語|
|テルグ語|
|タイ語|
|トルコ語|
|ウクライナ語|
|ウルドゥー語|
|ベトナム語|
