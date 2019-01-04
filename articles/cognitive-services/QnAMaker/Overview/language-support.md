---
title: 言語サポート - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker のナレッジ ベースでサポートされるカルチャと自然言語の一覧。 同じナレッジ ベースに複数の言語を混在させないでください。
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 11/09/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: d51af0f59ffc0189ddaba4f8197aca79becf779e
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53106364"
---
# <a name="language-and-region-support-for-qna-maker"></a>QnA Maker の言語と地域のサポート

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
QnA Maker は、Azure Search の[言語アナライザー](https://docs.microsoft.com/rest/api/searchservice/language-support)に依存して結果を提供します。 En-* 言語については、関連性が向上する特別な再ランキング機能を利用できます。

Azure Search の機能はサポートされている言語に従いますが、QnA Maker には Azure Search の結果より上位に追加のランカーがあります。 このランカー モデルでは、en-* においてはいくつかの特別なセマンティックと単語に基づく機能を使用しますが、他の言語ではまだ使用できません。 これらはランカーの内部動作の一部であるため、これらを使用できるようにはしません。 

QnA Maker は、作成時にナレッジ ベースの言語を自動検出し、それに応じてアナライザーを設定します。 次の言語でナレッジ ベースを作成できます。 QnA Maker による言語の処理方法について詳しくは、[こちら](../How-To/language-knowledge-base.md)をご覧ください。


> [!Tip]
> 言語アナライザーは、いったん設定されると変更できません。 また、言語アナライザーは [QnA Maker サービス](../How-To/set-up-qnamaker-service-azure.md)内のすべてのナレッジ ベースに適用されます。 異なる言語のナレッジ ベースを使用する場合は、別の QnA Maker サービスで作成する必要があります。

|サポートされている言語|
|-----|
|アラビア語|
|アルメニア語|,
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
