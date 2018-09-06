---
title: Azure Maps を使ってピンを追加する | Microsoft Docs
description: Javascript のマップにピンを追加する方法
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 0dafb09e1704e8e446b034975f0c25a740050599
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382586"
---
# <a name="add-pins-to-the-map"></a>マップにピンを追加する

この記事では、マップにピンを追加する方法について説明します。  

## <a name="understand-the-code"></a>コードの理解

<iframe height='500' scrolling='no' title='マップにピンを追加する' src='//codepen.io/azuremaps/embed/ZrVpEa/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/ZrVpEa/'>Add a pin to a map</a>」Pen を表示します。
</iframe>

上記のコードでは、コードの最初のブロックでマップ オブジェクトを作成しています。 作成方法については、「[Create a map (マップの作成)](./map-create.md)」をご覧ください。

コードの 2 つ目のブロックでは、ピンが作成され、マップに追加されています。 ピンは、Feature プロパティに [PinProperties](https://docs.microsoft.com/javascript/api/azure-maps-control/models.pinproperties?view=azure-iot-typescript-latest) を使った、[Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) の[フィーチャー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)です。 ピンを作成してそのプロパティを定義するには、`new atlas.data.Feature(new atlas.data.Point())` を使います。 ピン レイヤーは、複数のピンの配列です。 マップにピン レイヤーを追加し、ピン レイヤーのプロパティを定義するには、[addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins) 関数を使います。 ピン レイヤーのプロパティについては、[PinLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.pinlayeroptions?view=azure-iot-typescript-latest) に関する記事をご覧ください。 

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。 
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)
    
マップに追加するコード例の詳細については、次の記事を参照してください。 
* [ポップアップを追加する](./map-add-popup.md)
* [図形を追加する](./map-add-shape.md)

