---
title: Azure Maps を使った検索結果の表示 | Microsoft Docs
description: Azure Maps を使って検索要求を実行し、Javascrip マップに結果を表示する方法
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b77737b16b23ed00c8f12f84e6a8558a665a7d15
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34599883"
---
# <a name="show-search-results-on-the-map"></a>マップに検索結果を表示する

この記事では、検索要求を実行し、検索結果をマップに表示する方法について説明します。 

## <a name="understand-the-code"></a>コードの理解

<iframe height='500' scrolling='no' title='マップに検索結果を表示する' src='//codepen.io/azuremaps/embed/KQbaeM/?height=519&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Show search results on a map</a>」Pen を表示します。
</iframe>

上記のコードでは、コードの最初のブロックでマップ オブジェクトを作成しています。 作成方法については、「[Create a map (マップの作成)](./map-create.md)」をご覧ください。

コードの 2 番目のブロックでは、検索ピンのレイヤーを作成し、マップ上に追加しています。 手順については、「[マップにピンを追加する](./map-add-pin.md)」をご覧ください。

コードの 3 番目のブロックでは、[Azure Maps Fuzzy Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) に [XMLHttpRequest](https://xhr.spec.whatwg.org/) を送信しています。

コードの最後のブロックでは、受信した応答を解析しています。 応答が正常に返された場合は、返された各場所の緯度と経度の情報が収集されます。 すべての地点はマップ上にピンとして追加され、マップの境界は、すべてのピンを表示できるように調整されます。


## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。 

* [Azure Maps Fuzzy Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* [マップ](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)
    
マップに追加するコード例の詳細については、次の記事を参照してください。 
* [座標から情報を取得する](./map-get-information-from-coordinate.md)
* [A から B までのルートを表示する ](./map-route.md)
