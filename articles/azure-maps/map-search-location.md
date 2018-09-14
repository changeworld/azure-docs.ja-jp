---
title: Azure Maps を使った検索結果の表示 | Microsoft Docs
description: Azure Maps を使って検索要求を実行し、Javascript マップに結果を表示する方法
author: jingjing-z
ms.author: jinzh
ms.date: 08/31/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7d4eb5f9be4a6bcefe4b544d3f97a9b9391c0d81
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2018
ms.locfileid: "43665779"
---
# <a name="show-search-results-on-the-map"></a>マップに検索結果を表示する

この記事では、目的地を検索し、地図上に検索結果を表示する方法について説明します。 

## <a name="understand-the-code"></a>コードの理解

<iframe height='500' scrolling='no' title='マップに検索結果を表示する (サービス モジュール)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/zLdYEB/'>Show search results on a map (Service Module)</a>」Pen を表示します。
</iframe>

上記のコードの最初のコード ブロックでは、マップ オブジェクトが作成され、クライアント サービスがインスタンス化されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

2 つ目のコード ブロックでは、あいまい検索 [Azure Maps Fuzzy Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) を使用して POI を検索します。 あいまい検索 API は、あいまい入力の任意の組み合わせを処理できます。 あいまい検索サービスからの応答は、[getGeoJsonSearchResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonsearchresponse?view=azure-iot-typescript-latest#geojsonsearchresponse) メソッドを使用して GeoJSON 形式に解析されます。 地図上の目的地を示すピンがマップに追加されます。

コードの最後のブロックでは、Map の [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest) プロパティを使用してマップのカメラ境界を追加します。

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。 

* [Azure Maps Fuzzy Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* [マップ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)
    
マップに追加するコード例の詳細については、次の記事を参照してください。 
* [座標から情報を取得する](./map-get-information-from-coordinate.md)
* [A から B までのルートを表示する ](./map-route.md)
