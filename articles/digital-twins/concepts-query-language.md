---
title: クエリ言語
titleSuffix: Azure Digital Twins
description: Azure Digital Twins クエリ言語の基本について学習します。
author: baanders
ms.author: baanders
ms.date: 10/27/2021
ms.topic: conceptual
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: 620411b5fc8c657f837f3c27077d4ae3dbf16f1c
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131509690"
---
# <a name="azure-digital-twins-query-language"></a>Azure Digital Twins クエリ言語

この記事では、クエリ言語とその機能の基本について説明します。 Azure Digital Twins の中心は、デジタル ツインとリレーションシップから構築された[ツイン グラフ](concepts-twins-graph.md)であることを思い出してください。 このグラフに対してクエリを実行し、デジタル ツインとそれに含まれるリレーションシップに関する情報を取得することができます。 これらのクエリは、**Azure Digital Twins クエリ言語** と呼ばれる、SQL に似たクエリ言語で記述されます。 この言語は、同等の機能を多数持つ [IoT Hub クエリ言語](../iot-hub/iot-hub-devguide-query-language.md)に似ています。

クエリ構文の詳細な例とクエリ要求の実行方法については、[ツイン グラフへのクエリの実行](how-to-query-graph.md)に関する記事を参照してください。

## <a name="about-the-queries"></a>クエリについて

Azure Digital Twins クエリ言語を使用し、次に応じて、デジタル ツインを取得できます。
* プロパティ ([タグのプロパティ](how-to-use-tags.md)を含む)
* モデル
* リレーションシップ
  - リレーションシップのプロパティ

クライアント アプリからサービスにクエリを送信するには、Azure Digital Twins の [Query API](/rest/api/digital-twins/dataplane/query) を使用します。 API を使用する方法の 1 つとして、[Azure Digital Twins のいずれかの SDK](concepts-apis-sdks.md#overview-data-plane-apis) を使用する方法があります。

[!INCLUDE [digital-twins-query-reference.md](../../includes/digital-twins-query-reference.md)]

## <a name="considerations-for-querying"></a>クエリに関する考慮事項

Azure Digital Twins のクエリを作成する場合は、次の考慮事項に注意してください。
* **大文字と小文字の区別を忘れない**: すべての Azure Digital Twins クエリ操作では大文字と小文字が区別されるため、モデルで定義されている正確な名前を使用するように注意してください。 プロパティ名のスペルが間違っているか、大文字と小文字が正しくない場合、結果セットは空になり、エラーは返されません。
* **単一引用符をエスケープする**: クエリ テキストのデータに単一引用符文字が含まれている場合は、`\` 文字を使用して引用符をエスケープする必要があります。 プロパティ値 *D'Souza* を扱う例を次に示します。

  :::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="EscapedSingleQuote":::

[!INCLUDE [digital-twins-query-latency-note.md](../../includes/digital-twins-query-latency-note.md)]

## <a name="next-steps"></a>次のステップ

クエリの作成方法を学習し、クライアント コードの例を見るには、[ツイン グラフへのクエリの実行](how-to-query-graph.md)に関するページを参照してください。