---
title: Azure Maps を使ってピンを追加する | Microsoft Docs
description: Javascript のマップにピンを追加する方法
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
ms.openlocfilehash: 189b30ff0b5c47d0a4bd3181c8eb2143213ad6cd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="add-pins-to-the-map"></a>マップにピンを追加する

この記事では、マップにピンを追加する方法について説明します。  

## <a name="understand-the-code"></a>コードの理解

<iframe height='500' scrolling='no' title='マップにピンを追加する' src='//codepen.io/azuremaps/embed/ZrVpEa/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/ZrVpEa/'>Add a pin to a map</a>」Pen を表示します。
</iframe>

上記のコードでは、コードの最初のブロックでマップ オブジェクトを作成しています。 作成方法については、「[Create a map (マップの作成)](./map-create.md)」をご覧ください。

コードの 2 つ目のブロックでは、ピンが作成され、マップに追加されています。 ピンは、Feature プロパティに [PinProperties](https://docs.microsoft.com/javascript/api/azure-maps-javascript/pinproperties?view=azure-iot-typescript-latest) を使った、[Point](https://docs.microsoft.com/javascript/api/azure-maps-javascript/point?view=azure-iot-typescript-latest) の[フィーチャー](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest)です。 ピンを作成してそのプロパティを定義するには、`new atlas.data.Feature(new atlas.data.Point())` を使います。 ピン レイヤーは、複数のピンの配列です。 マップにピン レイヤーを追加し、ピン レイヤーのプロパティを定義するには、[addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins) 関数を使います。 ピン レイヤーのプロパティについては、[PinLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/pinlayeroptions?view=azure-iot-typescript-latest) に関する記事をご覧ください。 

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドについてさらに詳しく知る。 
* [マップ](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)
