---
title: Azure Digital Twins のクエリ ユニット
titleSuffix: Azure Digital Twins
description: Azure Digital Twins のクエリ ユニットの請求概念について
author: baanders
ms.author: baanders
ms.date: 9/16/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: eb5e7970c2ce44b5ee96f671464a3029cb9429b1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631003"
---
# <a name="query-units-in-azure-digital-twins"></a>Azure Digital Twins のクエリ ユニット 

Azure Digital Twins の **クエリ ユニット (QU)** は、[クエリ API](/rest/api/digital-twins/dataplane/query) を使用して [Azure Digital Twins クエリ](how-to-query-graph.md)を実行するために使われるオンデマンド計算の単位です。 

Azure Digital Twins でサポートされているクエリ操作を実行するために必要なシステム リソース (CPU、IOPS、メモリなど) を抽象化し、代わりにクエリ ユニットで使用状況を追跡できるようにします。

クエリの実行で消費されるクエリ ユニットの量は以下によって左右されます。

* クエリの複雑性
* 結果セットのサイズ (つまり、結果を 10 個返すクエリは、複雑性が同じながら結果を 1 個だけ返すクエリよりたくさんの QU を消費します)

この記事では、クエリ ユニットの概要と、クエリ ユニットの消費を追跡する方法について説明します。

## <a name="find-the-query-unit-consumption-in-azure-digital-twins"></a>Azure Digital Twins でのクエリ ユニット消費の確認

Azure Digital Twins の[クエリ API](/rest/api/digital-twins/dataplane/query) を使用してクエリを実行するとき、応答ヘッダーを検証して、クエリで消費した QU の数を追跡できます。 Azure Digital Twins から返された応答内の "query-charge" を探します。

[Azure Digital Twins の SDK](concepts-apis-sdks.md) を使用すると、ページング可能な応答から query-charge ヘッダーを抽出できます。 このセクションでは、デジタル ツインをクエリする方法と、ページング可能な応答を反復処理して query-charge ヘッダーを抽出する方法を示します。 

次のコード スニペットは、クエリ API を呼び出すと発生するクエリ料金を抽出する方法を示しています。 まず応答ページを反復処理して query-charge ヘッダーにアクセスした後、各ページ内のデジタル ツイン結果を反復処理します。 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/getQueryCharges.cs":::

## <a name="next-steps"></a>次のステップ

Azure Digital Twins のクエリの詳細については、次を参照してください。

* [クエリ言語](concepts-query-language.md)
* [ツイン グラフにクエリを実行する](how-to-query-graph.md)
* [クエリ API リファレンス ドキュメント](/rest/api/digital-twins/dataplane/query/querytwins)

Azure Digital Twins のクエリ関連の制限については、[Azure Digital Twins サービスの制限](reference-service-limits.md)に関する記事をご覧ください。