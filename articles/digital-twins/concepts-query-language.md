---
title: クエリ言語
titleSuffix: Azure Digital Twins
description: Azure Digital Twins クエリ ストア言語の基本について説明します。
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: b3ed5d6605097b31dfaa58a2d37e71d3a6702bee
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537497"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>Azure Digital Twins 用のクエリ言語について

Azure Digital Twins の中心は、**デジタル ツイン**と**リレーションシップ**から構築された[**ツイン グラフ**](concepts-twins-graph.md)であることを思い出してください。 このグラフに対してクエリを実行し、デジタル ツインとそれに含まれるリレーションシップに関する情報を取得することができます。 これらのクエリは、**Azure Digital Twins クエリ ストア言語**と呼ばれる、SQL に似たカスタム クエリ言語で記述されます。

クライアント アプリからサービスにクエリを送信するには、Azure Digital Twins の [**Query API**](https://docs.microsoft.com/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.query?view=azure-dotnet-preview) を使用します。 これにより、開発者はクエリを作成し、フィルターを適用して、ツイン グラフ内のデジタル ツインのセットや、Azure Digital Twins のシナリオに関するその他の情報を検索できます。

## <a name="query-language-features"></a>クエリ言語の機能

Azure Digital Twins では、ツイン グラフに対する広範なクエリ機能が提供されます。 クエリは、多くの同等の機能を備えている [IoT Hub クエリ言語](../iot-hub/iot-hub-devguide-query-language.md)に似たクエリ言語で、SQL に似た構文を使用して記述します。

> [!NOTE]
> すべての Azure Digital Twins クエリ操作では、大文字と小文字が区別されます。

Azure Digital Twins クエリ ストア言語で使用できる操作は次のとおりです。
* デジタル ツインのプロパティでツインを取得します。
* デジタル ツインのインターフェイスでツインを取得します。
* リレーションシップのプロパティでツインを取得します。
* 複数のリレーションシップの種類に対してツインを取得します (`JOIN` クエリ)。 許可される `JOIN` の数には制限があります (パブリック プレビューの場合は 1 レベル)。
* カスタム関数 `IS_OF_MODEL(twinCollection, twinTypeName)` を使用します。これにより、ツインの[モデル](concepts-models.md)に基づいてフィルター処理を行うことができます。 継承がサポートされています。
* スカラー関数を使用します: `IS_BOOL`、`IS_DEFINED`、`IS_NULL`、`IS_NUMBER`、`IS_OBJECT`、`IS_PRIMITIVE`、`IS_STRING`、`STARTS_WITH`、`ENDS_WITH`。
* クエリ比較演算子を使用します: `IN`/`NIN`、`=`、`!=`、`<`、`>`、`<=`、`>=`。
* 上記の任意の組み合わせ (`AND`、`OR`、`NOT` 演算子) を使用します。
* 継続を使用します: クエリ オブジェクトは、ページ サイズ (最大 100) を使用してインスタンス化されます。 API の後続の呼び出しに継続トークンを指定することで、デジタル ツインを一度に 1 ページずつ取得できます。

## <a name="next-steps"></a>次のステップ

クエリの作成方法を学習し、クライアント コードの例を見るには、[*方法:ツイン グラフにクエリを実行する*](how-to-query-graph.md)方法に関する記事を参照してください。
