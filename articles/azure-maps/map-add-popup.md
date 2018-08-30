---
title: Azure Maps を使用してポップアップを追加する | Microsoft Docs
description: JavaScript マップにポップアップを追加する方法
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 0f86578e33e5c6a2d6528e2deb1c8068a0c94d01
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2018
ms.locfileid: "42747107"
---
# <a name="add-a-popup-to-the-map"></a>マップにポップアップを追加する

この記事では、マップにポップアップを追加する方法について説明します。  

## <a name="understand-the-code"></a>コードの理解

<a id="addAPopup"></a>

<iframe height='500' scrolling='no' title='マップにポップアップを追加する' src='//codepen.io/azuremaps/embed/zRyKxj/?height=545&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> で Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) を使用して<a href='https://codepen.io/azuremaps/pen/zRyKxj/'>ポップアップをマップに追加する</a> Pen の例です。
</iframe>

上記のコードの最初のコード ブロックでは、マップ オブジェクトが作成されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

2 つ目のコード ブロックでは、ピンが作成され、マップに追加されます。 手順については、[マップにピンを追加する方法](./map-add-pin.md)に関する記事を参照してください。

3 つ目のコード ブロックでは、ポップアップ内に表示されるコンテンツが作成されます。 ポップアップ コンテンツは HTML 要素です。 

コードの 4 つ目のブロックでは、`new atlas.Popup()` 経由で[ポップアップ オブジェクト](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)が作成されます。 コンテンツや位置などのポップアップのプロパティは、[PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.popupoptions?view=azure-iot-typescript-latest) の一部です。 PopupOptions は、ポップアップ コンストラクターで、またはポップアップ クラスの [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions) 関数を介して定義できます。

コードの最後のブロックでは、ピンに対するマウスオーバー イベントをリッスンするためにマップ クラスの [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addeventlistener) クラスが使用されます。また、イベントが発生した場合にポップアップを開くためにポップアップ クラスの [open](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) 関数が使用されます。


## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。 

* [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)
    * [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addeventlistener)
* [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
    * [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions)
    * [open](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open)
    * [close](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#close)
    
マップに追加するコード例の詳細については、次の記事を参照してください。 
* [シェイプを追加する](./map-add-shape.md)
* [カスタム HTML を追加する](./map-add-custom-html.md)
