---
title: Bing Autosuggest とは
titleSuffix: Azure Cognitive Services
description: Bing Autosuggest API は、検索ボックス内の部分的なクエリ文字列に基づいて、候補となるクエリの一覧を返します。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 12/18/2019
ms.openlocfilehash: f4df9d3c8cb17561cb822b2bd1ac43396470af52
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128618763"
---
# <a name="what-is-bing-autosuggest"></a>Bing Autosuggest とは

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関する記事を参照してください。

アプリケーションから Bing Search API のいずれかにクエリを送信する場合、ユーザーの検索エクスペリエンスを向上させるために Bing Autosuggest API を使用することができます。 Bing Autosuggest API は、検索ボックス内の部分的なクエリ文字列に基づいて、候補となるクエリの一覧を返します。 検索ボックスに文字を入力すると、ドロップダウン リストに候補が表示されます。

## <a name="bing-autosuggest-api-features"></a>Bing Autosuggest API の機能

| 特徴量                                                                                                                                                                                 | 説明                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [検索用語をリアルタイムで提案する](concepts/get-suggestions.md) | Autosuggest API を使用して、入力時に検索語句の候補を表示することで、アプリのエクスペリエンスを向上させます。 |

## <a name="workflow"></a>ワークフロー

Bing Autosuggest API は RESTful Web サービスです。HTTP 要求を実行して JSON を解析できる任意のプログラミング言語から簡単に呼び出すことができます。

1. Bing Search API が利用できる [Cognitive Services API アカウント](../cognitive-services-apis-create-account.md)を作成します。 Azure サブスクリプションをお持ちでない場合は、無料で[アカウントを作成](https://azure.microsoft.com/free/cognitive-services/)できます。
2. アプリケーションの検索ボックスにユーザーが新しい文字を入力するたびに、この API に要求を送信します。
3. 返された JSON メッセージを解析して API 応答を処理します。

通常は、アプリケーションの検索ボックスにユーザーが新しい文字を入力するたびに、この API を呼び出します。 新たに文字が入力されるたびに、より関連性の高い検索クエリ候補が API から返されます。 たとえば、`s` の 1 文字について API から返される候補は、`sail` で返される候補よりも関連性が低くなることが考えられます。

Bing Autosuggest API からの検索語の候補を含んだドロップダウン検索ボックスの例を次に示します。

![ドロップダウン検索ボックスによる自動提案リスト](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

ユーザーがドロップダウン リストから候補を選択したら、Bing Search API のいずれかでその候補を使用して検索を開始するか、Bing の検索結果ページに直接移動することができます。

## <a name="next-steps"></a>次のステップ

実際に要求を送信してみるには、[最初のクエリを実行する](quickstarts/csharp.md)に関するページを参照してください。

[Bing Autosuggest API v7](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference) リファレンスを活用してください。 このリファレンスには、クエリ用語候補を要求する際に使用するエンドポイント、ヘッダー、クエリ パラメーターの一覧と、応答オブジェクトの定義が記載されています。

その他の提供されている API については、[Bing Search API ハブ ページ](../bing-web-search/overview.md)をご覧ください。


[Bing Web Search API](../bing-web-search/overview.md) を使用して Web を検索し、他の [Bing Search API](../bing-web-search/index.yml) を探す方法について説明します。

検索結果の使用に関するルールを逸脱しないよう、[Bing の使用上および表示上の要件](../bing-web-search/use-display-requirements.md)に関するページを必ず読んでください。