---
title: Bing Local Business Search API とは
titleSuffix: Azure Cognitive Services
description: RESTful サービスである Bing Local Business Search API を使用すると、アプリケーションで検索クエリに基づいて地元の場所や企業に関する情報を検索できます。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: overview
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 7a5829623707797b98593f837d6cadf009410f31
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487016"
---
# <a name="what-is-bing-local-business-search"></a>Bing Local Business Search とは

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関するページを参照してください。
RESTful サービスである Bing Local Business Search API を使用すると、アプリケーションで検索クエリに基づいて地元企業に関する情報を検索できます。 たとえば、`q=<business-name> in Redmond, Washington`、または `q=Italian restaurants near me` です。 

## <a name="features"></a>特徴
| 機能 | 説明 |  
| -- | -- | 
| [地元の企業と場所を検索する](quickstarts/local-quickstart.md) | Bing Local Business Search API では、クエリからローカライズされた結果が得られます。 結果には、企業の Web サイトの URL と表示テキスト、電話番号、地理的な場所(GPS 座標、市区町村、番地など) が含まれます |  
| [地理的境界を使用してローカル結果をフィルター処理する](specify-geographic-search.md) | 検索パラメーターとして座標を追加し、円領域または正方形の境界ボックスで指定された、特定の地域に結果を制限します。 | 
| [カテゴリ別に地元企業の結果をフィルター処理する](local-categories.md) | カテゴリ別に地元企業の結果を検索します。 このオプションでは、リバース IP の場所または呼び出し元の GPS 座標を使用して、さまざまな企業のカテゴリのローカライズされた結果を返します。|

## <a name="workflow"></a>ワークフロー
HTTP 要求を行って JSON 応答を解析できる任意のプログラミング言語から Bing Local Business Search API を呼び出します。 このサービスには REST API を使用してアクセスできます。
 
1. Bing Search API にアクセスできる [Cognitive Services API アカウント](../cognitive-services-apis-create-account.md)を作成します。 Azure サブスクリプションをお持ちでない場合は、[無料のアカウント](https://azure.microsoft.com/free/cognitive-services/)を作成できます。   
2. `q=""` クエリ パラメーターの検索語句を URL エンコードします。 たとえば、`q=nearby+restaurant` または `q=nearby%20restaurant` です。 必要に応じて、改ページ位置の自動修正も設定します。 
3. [Bing Local Business Search API に要求](quickstarts/local-quickstart.md) を送信します 
4. JSON 応答を解析します 

> [!NOTE]
> Local Business Search の現在のサポート状況は次のとおりです。 
> * サポートされるのは `en-US` 市場のみです。 
> * Bing Autosuggest はサポートされません。 

## <a name="next-steps"></a>次のステップ
- [クエリと応答](local-search-query-response.md)
- [Local Business Search のクイック スタート](quickstarts/local-quickstart.md)
- [Local Business Search API リファレンス](local-search-reference.md)
- [使用と表示の要件](../bing-web-search/use-display-requirements.md)