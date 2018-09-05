---
title: Azure Maps を使った検索結果の表示 | Microsoft Docs
description: Azure Maps を使って検索要求を実行し、Javascrip マップに結果を表示する方法
author: jingjing-z
ms.author: jinzh
ms.date: 08/26/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 540faa04573f7fa66b574f6743d42adfe855c571
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "43095257"
---
# <a name="show-search-results-on-the-map"></a>マップに検索結果を表示する

この記事では、目的地を検索し、地図上に検索結果を表示する方法について説明します。 

## <a name="understand-the-code"></a>コードの理解

<iframe height='305' scrolling='no' title='マップに検索結果を表示する (サービス モジュール)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/zLdYEB/'>Show search results on a map (Service Module)</a>」Pen を表示します。
</iframe>

上記のコードの最初のコード ブロックでは、マップ オブジェクトが作成され、クライアント サービスがインスタンス化されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

2 つ目のコード ブロックでは、あいまい検索 [Azure Maps Fuzzy Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) を使用して POI を検索します。 あいまい検索 API は、あいまい入力の任意の組み合わせを処理できます。 あいまい検索サービスからの応答は、GeoJSON 形式に解析され、ピンがマップに追加され、マップに POI が表示されます。 

コードの最後のブロックでは、Map の [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest) プロパティを使用してマップのカメラ境界を追加します。

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。 

* [Azure Maps Fuzzy Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* [マップ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)
    
マップに追加するコード例の詳細については、次の記事を参照してください。 
* [座標から情報を取得する](./map-get-information-from-coordinate.md)
* [A から B までのルートを表示する ](./map-route.md)
