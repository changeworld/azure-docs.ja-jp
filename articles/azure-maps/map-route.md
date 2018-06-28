---
title: Azure Maps を使った道順の表示 | Microsoft Docs
description: 2 つの場所の間の道順を Javascript マップ上に表示する方法
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 5e9ab73ddc16517e17894cddd9bc102f3941f00c
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "35778801"
---
# <a name="show-directions-from-a-to-b"></a>A から B までのルートを表示する 

この記事では、ルート要求を実行し、マップにルートを表示する方法について説明します。 

## <a name="understand-the-code"></a>コードの理解

<iframe height='500' scrolling='no' title='A から B までのルートをマップに表示する' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/zRyNmP/'>Show directions from A to B on a map</a>」Pen を表示します。
</iframe>

上記のコードでは、コードの最初のブロックでマップ オブジェクトを作成しています。 作成方法については、「[Create a map (マップの作成)](./map-create.md)」をご覧ください。

コードの 2 番目のブロックでは、ルートの起点と終点を表すピンを作成し、マップ上に追加しています。 手順については、「[マップにピンを追加する](map-add-pin.md)」をご覧ください。

コードの 3 番目のブロックでは、マップ クラスの [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds) 関数を使い、ルートの起点と終点に基づいて、マップの境界ボックスを設定しています。

コードの 4 番目のブロックでは、[Azure Maps Route API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) に [XMLHttpRequest](https://xhr.spec.whatwg.org/) を送信しています。

コードの最後のブロックでは、受信した応答を解析しています。 応答が正常に返された場合は、各通過地点の緯度と経度の情報が収集されます。 各通過地点をその後続の通過地点に接続することで、線の配列が作成されます。 それらすべての線をマップ上に追加して、ルートがレンダリングされます。 手順については、「[線を追加する](./map-add-shape.md#addALine)」をご覧ください。

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。 

* [マップ](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds)
    * [addLinestrings](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addlinestrings)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)

マップに追加するコード例の詳細については、次の記事を参照してください。 
* [マップ上にトラフィックを表示する](./map-show-traffic.md)
* [マップの連動 – マウス イベント](./map-events.md)
