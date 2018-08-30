---
title: Azure Maps でのトラフィックの表示 | Microsoft Docs
description: Javascript マップ上にトラフィック データを表示する方法
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 2499822db587dbf47dccedf6039d0fb5823c58b5
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746080"
---
# <a name="show-traffic-on-the-map"></a>マップ上にトラフィックを表示する

この記事では、マップ上にトラフィック情報やインシデント情報を表示する方法について説明します。 

## <a name="understand-the-code"></a>コードの理解

<iframe height='456' scrolling='no' title='マップ上にトラフィックを表示する' src='//codepen.io/azuremaps/embed/WMLRPw/?height=456&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Show traffic on a map</a>」Pen を表示します。
</iframe>

上記のコードでは、コードの最初のブロックでマップ オブジェクトを作成しています。 作成方法については、「[Create a map (マップの作成)](map-create.md)」をご覧ください。

コードの 2 番目のブロックでは、マップ クラスの [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#settraffic) 関数を使って、トラフィック フローとインシデントをマップ上にレンダリングしています。

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。 
* [マップ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#settraffic)

マップに追加するコード例の詳細については、次の記事を参照してください。 
* [マップの連動 – マウス イベント](./map-events.md)
* [アクセス可能なマップをビルド](./map-accessibility.md)

より多くのマッピング シナリオの [サンプルのコード ページ](http://aka.ms/AzureMapsSamples) をチェック アウトします。
