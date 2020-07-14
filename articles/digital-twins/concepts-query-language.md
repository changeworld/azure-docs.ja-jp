---
title: クエリ言語
titleSuffix: Azure Digital Twins
description: Azure Digital Twins クエリ ストア言語の基本について説明します。
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 3196004015046b4d3d2789745c80d323bacdced9
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2020
ms.locfileid: "85985243"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>Azure Digital Twins 用のクエリ言語について

Azure Digital Twins の中心は、**デジタル ツイン**と**リレーションシップ**から構築された[**ツイン グラフ**](concepts-twins-graph.md)であることを思い出してください。 このグラフに対してクエリを実行し、デジタル ツインとそれに含まれるリレーションシップに関する情報を取得することができます。 これらのクエリは、**Azure Digital Twins クエリ ストア言語**と呼ばれる、SQL に似たカスタム クエリ言語で記述されます。

クライアント アプリからサービスにクエリを送信するには、Azure Digital Twins の **Query API** を使用します。 これにより、開発者はクエリを作成し、フィルターを適用して、ツイン グラフ内のデジタル ツインのセットや、Azure Digital Twins のシナリオに関するその他の情報を検索できます。

## <a name="query-language-features"></a>クエリ言語の機能

Azure Digital Twins では、ツイン グラフに対する広範なクエリ機能が提供されます。 クエリは、多くの同等の機能を備えている [IoT Hub クエリ言語](../iot-hub/iot-hub-devguide-query-language.md)に似たクエリ言語で、SQL に似た構文を使用して記述します。

Azure Digital Twins クエリ ストア言語で使用できる操作は次のとおりです。
* デジタル ツインのプロパティでツインを取得します。
* デジタル ツインのインターフェイスでツインを取得します。
* リレーションシップのプロパティでツインを取得します。
* 複数のリレーションシップの種類に対してツインを取得します (`JOIN` クエリ)。 許可される `JOIN` の数には制限があります (パブリック プレビューの場合は 1 レベル)。
* カスタム関数 `IS_OF_MODEL(twinCollection, twinTypeName)` を使用します。これにより、ツインの[モデル](concepts-models.md)に基づいてフィルター処理を行うことができます。 継承がサポートされています。
* 上記の任意の組み合わせ (`AND`、`OR`、`NOT` 演算子) を使用します。
* スカラー関数を使用します: `IS_BOOL`、`IS_DEFINED`、`IS_NULL`、`IS_NUMBER`、`IS_OBJECT`、`IS_PRIMITIVE`、`IS_STRING`、`STARTS_WITH`、`ENDS_WITH`。
* クエリ比較演算子を使用します: `AND`/`OR`/`NOT`、`IN`/`NOT IN`、`STARTSWITH`/`ENDSWITH`、`=`、`!=`、`<`、`>`、`<=`、`>=`。
* 継続を使用します: クエリ オブジェクトは、ページ サイズ (最大 100) を使用してインスタンス化されます。 `nextAsTwin` メソッドの呼び出しを繰り返すことにより、一度に 1 ページずつデジタル ツインを取得できます。

## <a name="next-steps"></a>次のステップ

クエリの作成方法を学習し、クライアント コードの例を見るには、[ツイン グラフのクエリの実行方法](how-to-query-graph.md)に関するページを参照してください。