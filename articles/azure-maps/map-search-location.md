---
title: Azure Maps を使った検索結果の表示 | Microsoft Docs
description: Azure Maps を使って検索要求を実行し、Javascrip マップに結果を表示する方法
services: azure-maps
keywords: ''
author: jinzh-azureiot
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: codepen
ms.openlocfilehash: f66b1f93d7bc4c2e7c511c10d7091760e8f6d023
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
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

この記事で使われているクラスとメソッドについてさらに詳しく知る。 

* [Azure Maps Fuzzy Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* [マップ](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)
