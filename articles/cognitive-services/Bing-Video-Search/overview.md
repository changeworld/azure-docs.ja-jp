---
title: Bing Video Search API とは
titleSuffix: Azure Cognitive Services
description: Bing Video Search API を使用して Web 全体から動画を検索する方法について説明します。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: overview
ms.date: 12/18/2019
ms.openlocfilehash: 5e1586ac15df484cd4110a63c922bc72785a406f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600743"
---
# <a name="what-is-the-bing-video-search-api"></a>Bing Video Search API とは

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関するページを参照してください。

Bing Video Search API を使用すると、サービスやアプリケーションに動画の検索機能を簡単に追加することができます。 この API を使用してユーザーの検索クエリを送信すると、[Bing Video](https://www.bing.com/video) のような関連性のある高品質の動画を取得して表示できます。 この API は、動画のみを含んだ検索結果に使用します。 [Bing Web Search API](../bing-web-search/overview.md) は、Web ページ、動画、ニュース、画像など、他の種類の Web コンテンツを返すことができます。

## <a name="bing-video-search-api-features"></a>Bing Video Search API の機能

| 特徴量                                                                                                                                                                                 | 説明                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [検索用語をリアルタイムで提案する](concepts/sending-requests.md#suggest-search-terms-with-the-bing-autosuggest-api) | [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) を使用して、入力時に候補検索用語を表示することで、アプリのエクスペリエンスを向上します。 |
| [動画の結果をフィルター処理して制限する](concepts/get-videos.md#filtering-videos)                      | 返される動画を、クエリ パラメーターを編集してフィルター処理します。                                                                                                       |
| [サムネイルのクロップ、サイズ変更、表示](../bing-web-search/resize-and-crop-thumbnails.md)                                                | Bing Video Search API から返された動画のサムネイル プレビューを編集して表示します。                                                                                      |
| [急上昇中の動画の取得](trending-videos.md) | 世界で注目を集めている動画を検索します。                                                                                                          |
| [ビデオの分析情報の取得](video-insights.md) | 世界で注目を集めている動画の検索をカスタマイズします。                                                                                                          |

## <a name="workflow"></a>ワークフロー

Bing Video Search API は RESTful Web サービスです。HTTP 要求を作成して JSON を解析できる任意のプログラミング言語から簡単に呼び出すことができます。 [REST API](./quickstarts/csharp.md) または [SDK](./quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp) のいずれかを使用してサービスを使用できます。

1. Bing Search API が利用できる [Cognitive Services API アカウント](../cognitive-services-apis-create-account.md)を作成します。 Azure サブスクリプションをお持ちでない場合は、無料で[アカウントを作成](https://azure.microsoft.com/free/cognitive-services/)できます。
2. 有効な検索クエリを使用して API に要求を送信します。
3. 返された JSON メッセージを解析して API 応答を処理します。


## <a name="next-steps"></a>次のステップ

Bing Video Search API の[対話型デモ](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/)では、検索クエリをカスタマイズし、Web から動画を検索する方法を紹介しています。

[クイック スタート](./quickstarts/csharp.md)で、初めての API 要求を手軽に体験してみましょう。

## <a name="see-also"></a>関連項目

* [Bing Video Search API v7](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference) リファレンス ページには、検索結果を要求する際に使用するエンドポイント、ヘッダー、クエリ パラメーターの一覧が記載されています。

* [Bing の利用と表示の要件](../bing-web-search/use-display-requirements.md)に関するトピックでは、Bing Search API を使用して得られるコンテンツと情報の許可される使用方法が指定されています。

* その他の提供されている API については、[Bing Search API ハブ ページ](../bing-web-search/overview.md)をご覧ください。