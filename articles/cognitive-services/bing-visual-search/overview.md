---
title: Bing Visual Search とは
titleSuffix: Azure Cognitive Services
description: Bing Visual Search は、似た画像やショッピング ソースなど、画像に関する詳細または分析情報を提供します。
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: overview
ms.date: 04/10/2018
ms.author: scottwhi
ms.openlocfilehash: aab17f8a0213c213920ebdfc2b2fe31248657a91
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742849"
---
# <a name="what-is-the-bing-visual-search-api"></a>Bing Visual Search API とは

Bing Visual Search API は、Bing.com/images に表示されるような画像の詳細を提供します。 この API では、画像をアップロードするか、画像の URL を指定することで、画像に関する詳細を特定することができます (見た目が似ている画像、ショッピング ソース、Web ページのソースのショッピング、その画像が使用されている Web ページなど)。 [Bing Image Search API](../bing-image-search/overview.md) を使用する場合は、画像をアップロードする代わりに、API の検索結果にアタッチされた分析情報トークンを使用できます。

## <a name="insights"></a>洞察

以下に示すのは、Visual Search で取得できる分析情報です。

| 分析情報                              | 説明 |
|--------------------------------------|-------------|
| 見た目が似ている画像              | 入力画像に視覚的に似ている画像の一覧。 |
| 見た目が似ている製品            | 画像の製品と視覚的に似ている製品。            |
| ショッピング ソース                     | 入力画像に示されているアイテムを購入できる場所の一覧。            |
| 関連検索                     | 他のユーザーによって行われた関連検索または画像の内容に基づく関連検索。            |
| 画像を含む Web ページ     | 入力画像を含む Web ページ。            |
| レシピ                              | 入力画像に示されている料理を作るためのレシピを含む Web ページ            |

これらの分析情報だけでなく、Visual Search は入力画像から得られるさまざまな用語セット (タグ) も返します。 これらのタグを使用して、ユーザーは画像で見つかる概念を調べることができます。 たとえば、入力画像が有名なアスリートである場合、そのアスリートの名前や "スポーツ" がタグとして返される可能性があります。 または、入力画像がアップルパイの場合は、"アップルパイ"、"パイ"、"デザート" などがタグとして考えられるので、ユーザーは関連する概念を調べることができます。

Visual Search の結果には、画像内で興味を引く部分を囲むボックスも含まれます。 たとえば、画像に複数の有名人が含まれている場合、結果には画像で認識された各有名人の境界ボックスが含まれる可能性があります。 または、Bing が画像で製品や衣類を認識した場合、結果には認識された製品や衣類のアイテムに対する境界ボックスが含まれることがあります。

> [!IMPORTANT]
> Bing Image Search API を使用して画像に関する分析情報を取得する場合は、より包括的な分析情報が取得できる Bing Visual Search API への切り替えを検討してください。

## <a name="workflow"></a>ワークフロー

Bing Visual Search API は RESTfulWeb サービスです。HTTP 要求を作成して JSON を解析できる任意のプログラミング言語から簡単に呼び出すことができます。 REST API または SDK のいずれかを使用してサービスを使用できます。

1. Bing Search API にアクセスできる Cognitive Services API アカウントを作成します。 Azure サブスクリプションをお持ちでない場合は、無料でアカウントを作成できます。
2. 有効な検索クエリを使用して API に要求を送信します。
3. 返された JSON メッセージを解析して API 応答を処理します。


## <a name="next-steps"></a>次の手順

まず、Bing Image Search API の[対話型のデモ](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/)を試してみてください。
このデモは、検索クエリをすばやくカスタマイズし、Web で画像を検索する方法を示しています。

API を呼び出す準備ができたら、[Cognitive Services API アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)を作成します。 Azure サブスクリプションをお持ちでない場合は、無料で[アカウントを作成](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)できます。

実際に要求を送信してみるには、クイック スタートを参照してください:[C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md)。


## <a name="see-also"></a>関連項目

* [Bing Visual Search のリファレンス](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) ドキュメントには、画像ベースの検索結果を要求するために使用できるエンドポイント、ヘッダー、API 応答、およびクエリ パラメーターに関する定義と情報が含まれています。

* [Bing の利用と表示の要件](./use-and-display-requirements.md)に関するトピックでは、Bing Search API を使用して得られるコンテンツと情報の許可される使用方法が指定されています。
