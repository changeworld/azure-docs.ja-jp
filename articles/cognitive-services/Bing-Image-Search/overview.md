---
title: Bing Image Search API とは
titleSuffix: Azure Cognitive Services
description: Bing Image Search API では、アプリケーションで Bing の認知画像検索機能を使用できます。 この API を使用してユーザーの検索クエリを送信すると、Bing Images のような関連性のある高品質の画像を取得して表示できます。
services: cognitive-services
author: aahill
manager: cgronlun
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: overview
ms.date: 10/11/2017
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: eb5f508916ef0944a61d556725ae1ba04123584b
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2018
ms.locfileid: "53258306"
---
# <a name="what-is-the-bing-image-search-api"></a>Bing Image Search API とは

Bing Image Search API では、アプリケーションで Bing の認知画像検索機能を使用できます。 この API を使用してユーザーの検索クエリを送信すると、[Bing Images](https://www.bing.com/images) のような関連性のある高品質の画像を取得して表示できます。

Bing Image Search API では、画像の検索結果のみが提供される点に注意してください。 他の種類の Web コンテンツについては、[Bing Web Search API](../bing-web-search/search-the-web.md)、[Video Search API](https://docs.microsoft.com/azure/cognitive-services/Bing-Video-Search)、[News Search API](https://review.docs.microsoft.com/azure/cognitive-services/bing-news-search) を使用します。

## <a name="bing-image-search-features"></a>Bing Image Search の機能

Bing Image Search は、検索クエリから主に関連する画像を検索して返します。また、Web 上での高度で集中的な画像検索のための追加機能もいくつか提供されています。


| 機能                                                                                                                                                                                 | 説明                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [検索用語をリアルタイムで提案する](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries#using-and-suggesting-search-terms) | [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) を使用して、入力時に候補検索用語を表示することで、アプリのエクスペリエンスを向上します。 |
| [画像の結果をフィルター処理して制限する](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images#filtering-images)                       | クエリ パラメーターを編集して、Bing から返される画像をフィルター処理します。                                                                                                       |
| [サムネイルのクロップ、サイズ変更、表示](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/resize-and-crop-thumbnails)                                                | Bing Image Search から返された画像のサムネイル プレビューを編集して表示します。                                                                                      |
| [ユーザー検索クエリをピボットして展開する](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries#pivoting-the-query)               | Bing で提案された検索用語をクエリに含めて表示することで、検索機能を拡張できます。                                                                    |
| [注目の画像を取得する](https://review.docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images)                                                                     | 世界で注目を集めている画像の検索をカスタマイズします。                                                                                                          |

## <a name="workflow"></a>ワークフロー

Bing Image Search API は RESTfulWeb サービスです。HTTP 要求を作成して JSON を解析できる任意のプログラミング言語から簡単に呼び出すことができます。 [REST API](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp?) または [SDK](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart) のいずれかを使用してサービスを使用できます。

1. Bing Search API が利用できる [Cognitive Services API アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)を作成します。 Azure サブスクリプションをお持ちでない場合は、無料で[アカウントを作成](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)できます。
2. 有効な[検索クエリ](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries)を使用して API に要求を送信します。
3. 返された JSON メッセージを解析して API 応答を処理します。

## <a name="next-steps"></a>次の手順

まず、Bing Image Search API の[対話型のデモ](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)を試してみてください。
このデモは、検索クエリをすばやくカスタマイズし、Web で画像を検索する方法を示しています。

API を呼び出す準備ができたら、[Cognitive Services API アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)を作成します。 Azure サブスクリプションをお持ちでない場合は、無料で[アカウントを作成](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)できます。

最初の API 要求をすぐに開始する場合は、次のページを参照することをお勧めします。

* REST API を使用して [Bing に検索クエリを送信する方法](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp)。または
* SDK を使用して Bing から返される画像を[要求してフィルター処理する方法](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart)。

## <a name="see-also"></a>関連項目

* [Bing Image Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) のリファレンス セクションには、画像ベースの検索結果を要求するために使用できるエンドポイント、ヘッダー、API 応答、およびクエリ パラメーターに関する定義と情報が含まれています。

* [Bing の利用と表示の要件](./useanddisplayrequirements.md)に関するトピックでは、Bing Search API を使用して得られるコンテンツと情報の許可される使用方法が指定されています。

* 「[Bing Image Search API で Web から画像を取得する](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images)」トピックでは、Web から画像を検索して取得する方法について説明しています。

* [検索クエリの送信と使用](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries)に関するトピックでは、クエリの作成、カスタマイズ、およびピボットの方法について説明しています。
