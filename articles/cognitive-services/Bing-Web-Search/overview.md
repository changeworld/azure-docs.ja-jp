---
title: Bing Web Search とは | Microsoft Docs
description: Bing Web Search API は RESTful サービスです。Web ページ、画像、ビデオ、ニュース、エンティティ、関連する検索クエリ、スペルの修正、単位変換、翻訳、計算など、Bing の Web 検索機能をアプリケーションから使用できるようにするサービスです。
services: cognitive-services
author: erhopf
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: overview
ms.date: 08/14/2018
ms.author: erhopf
ms.openlocfilehash: ad1069a836a3ff9291ca8094fe86e19979bf7e32
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2018
ms.locfileid: "42889284"
---
# <a name="what-is-bing-web-search"></a>Bing Web Search とは

Bing Web Search API は、ユーザーのクエリにすぐに回答する RESTful サービスです。 検索結果は、Web ページ、画像、ビデオ、ニュース、翻訳などを含むように簡単に構成できます。 結果は JSON として提供され、検索の関連性と Bing Web Search のサブスクリプションに基づいています。

この API は、ユーザーの検索クエリに関連するすべてのコンテンツにアクセスする必要があるアプリケーションに最適です。 特定の種類の結果のみを必要とするアプリケーションを構築する場合は、[Bing Image Search API](../Bing-Image-Search/overview.md)、[Bing Video Search API](../Bing-Video-Search/search-the-web.md)、または [Bing News Search API](../Bing-News-Search/search-the-web.md) を使用することを検討してください。 Bing Search API の詳細な一覧については、[Cognitive Services APIs](https://docs.microsoft.com/azure/cognitive-services#cognitive-services-apis) を参照してください。

しくみを確認するには、 [Bing Web Search API のデモ](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/)を試してください。

## <a name="features"></a>機能  

Bing Web Search には、すぐに回答が得られるだけでなく、ユーザーに対する検索結果をカスタマイズできる追加の機能があります。

| Feature | 説明 |
|---------|-------------|
| [検索用語をリアルタイムで提案する](../bing-autosuggest/get-suggested-search-terms.md) | Bing Autosuggest API を使用して、入力時に候補検索用語を表示することで、アプリケーションのエクスペリエンスを向上します。 |
| [コンテンツ タイプで結果をフィルター処理して制限する](filter-answers.md) | Web ページ、画像、動画、セーフ サーチなどのフィルターとクエリ パラメーターを使用して、検索結果をカスタマイズして絞り込みます。 |
| [Unicode 文字でヒットした文字を強調表示する](hit-highlighting.md) | ヒットした文字の強調表示をユーザーに表示する前に、検索結果から不要な Unicode 文字を特定して削除します。 |
| [国、リージョン、または市場別に検索結果をローカライズする](supported-countries-markets.md) | Bing Web Search は、30 を超える国または地域をサポートしています。 特定の国/地域または市場の検索結果を絞り込むには、この機能を使用します。 |
| [Bing Statistics で検索メトリックを分析する](bing-web-stats.md) | Bing Statistics は、通話量、上位のクエリ文字列、地理的分布などの分析を提供する有料サブスクリプションです。 |

## <a name="workflow"></a>ワークフロー

Bing Web Search API は、HTTP 要求を作成して JSON 応答を解析できる任意プログラミング言語から簡単に呼び出すことができます。 このサービスには、[REST API](quickstarts/python.md) または [Bing Web Search SDK](web-sdk-python-quickstart.md) を使用してアクセスできます。  

1. Bing Search API が利用できる [Cognitive Services API アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)を作成します。 Azure サブスクリプションをお持ちでない場合は、[無料のアカウント](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)を作成できます。  
2. [要求を Bing Web Search API](quickstarts/python.md) に送信します。
3. JSON 応答を解析します。

## <a name="next-steps"></a>次の手順

* Bing Web Search API を初めて呼び出す場合は、[Python のクイック スタート](quickstarts/python.md)を参照してください。  
* [シングルページ Web アプリの作成](tutorial-bing-web-search-single-page-app.md)。
* [Web Search API v7 リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)のドキュメントを参照してください。  
* Bing Web Search の[使用と表示に関する要件](UseAndDisplayRequirements.md)の詳細を参照してください。  
