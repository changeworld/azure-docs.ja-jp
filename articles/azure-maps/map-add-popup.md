---
title: マップ上のポイントにポップアップを追加する | Microsoft Azure Maps
description: この記事では、Microsoft Azure Maps Web SDK を使用して、ポイントにポップアップを追加する方法について説明します。
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 45d210725f7f09663b126528479655d7f4d9c19f
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2020
ms.locfileid: "76933354"
---
# <a name="add-a-popup-to-the-map"></a>マップにポップアップを追加する

この記事では、マップ上のポイントにポップアップを追加する方法について説明します。

## <a name="understand-the-code"></a>コードの理解

次のコードでは、シンボル レイヤーを使用して、`name` プロパティと `description` プロパティを持つポイント フィーチャーをマップに追加します。 [Popup クラス](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)のインスタンスが作成されますが、表示されません。 ポップアップのオープンとクローズをトリガーするためのマウス イベントがシンボル レイヤーに追加されます。 マーカー シンボルをマウスでポイントすると、ポップアップの `position` プロパティがマーカーの位置に応じて更新され、マウスでポイントされているポイント フィーチャーの `name` プロパティと `description` プロパティをラップしている HTML に応じて `content` オプションが更新されます。 次に、その `open` 関数を使用してマップにポップアップが表示されます。

```javascript
//Define an HTML template for a custom popup content laypout.
var popupTemplate = '<div class="customInfobox"><div class="name">{name}</div>{description}</div>';

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

dataSource.add(new atlas.data.Feature(new atlas.data.Point([-122.1333, 47.63]), {
  name: 'Microsoft Building 41', 
  description: '15571 NE 31st St, Redmond, WA 98052'
}));

//Create a layer to render point data.
var symbolLayer = new atlas.layer.SymbolLayer(dataSource);

//Add the polygon and line the symbol layer to the map.
map.layers.add(symbolLayer);

//Create a popup but leave it closed so we can update it and display it later.
popup = new atlas.Popup({
  pixelOffset: [0, -18],
  closeButton: false
});

//Add a hover event to the symbol layer.
map.events.add('mouseover', symbolLayer, function (e) {
  //Make sure that the point exists.
  if (e.shapes && e.shapes.length > 0) {
    var content, coordinate;
    var properties = e.shapes[0].getProperties();
    content = popupTemplate.replace(/{name}/g, properties.name).replace(/{description}/g, properties.description);
    coordinate = e.shapes[0].getCoordinates();

    popup.setOptions({
      //Update the content of the popup.
      content: content,

      //Update the popup's position with the symbol's coordinate.
      position: coordinate

    });
    //Open the popup.
    popup.open(map);
  }
});

map.events.add('mouseleave', symbolLayer, function (){
  popup.close();
});
```

上記の機能の完全な実行コード サンプルを以下に示します。

<br/>

<iframe height='500' scrolling='no' title='Azure Maps を使用してポップアップを追加する' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> で Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) により <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Azure Maps を使用してポップアップを追加する</a> Pen の例です。
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>複数のポイントでポップアップを再利用する

多数のポイントがあり、一度に 1 つのポップアップのみを表示する場合、最善の方法は、ポップアップを 1 つ作成して再利用することです。 ポップアップを再利用することで、アプリケーションによって作成される DOM 要素数が大幅に削減され、パフォーマンスが向上します。 次のサンプルでは、3 ポイント フィーチャーを作成します。 そのいずれかをクリックすると、そのポイント フィーチャーのコンテンツを含むポップアップが表示されます。

<br/>

<iframe height='500' scrolling='no' title='複数のピンでポップアップを再利用する' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/rQbjvK/'>Reusing Popup with Multiple Pins</a>」Pen を表示します。
</iframe>

## <a name="customizing-a-popup"></a>ポップアップのカスタマイズ

既定で、ポップアップの背景は白であり、下部にポインターの矢印、右上にある [閉じる] ボタンがあります。 次のサンプルでは、ポップアップの `fillColor` オプションを使用して背景色を黒に変更します。 `CloseButton` オプションを false に設定すると、[閉じる] ボタンが削除されます。 ポップアップの HTML コンテンツでは、ポップアップの端から 10 ピクセルのスペースが使用されます。 テキストは白で表示されるため、黒の背景に適切に表示されます。  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="カスタマイズされたポップアップ" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen の<a href='https://codepen.io/azuremaps/pen/ymKgdg/'>カスタマイズ ポップアップ</a>を参照してください。
</iframe>

## <a name="popup-events"></a>ポップアップ イベント

ポップアップは、開く、閉じる、ドラッグすることができます。 popup クラスには、開発者がこれらのイベントに応答するために役立つイベントが用意されています。 次の例では、ユーザーがポップアップを開いたり、閉じたり、ドラッグしたりしたときに発生するイベントが強調されています。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="ポップアップ イベント" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen の<a href='https://codepen.io/azuremaps/pen/BXrpvB/'>ポップアップ イベント</a>を参照してください。
</iframe>

## <a name="next-steps"></a>次のステップ

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

完全なコードのサンプルについては、次の優れた記事をご覧ください。

> [!div class="nextstepaction"]
> [シンボル レイヤーを追加する](./map-add-pin.md)

> [!div class="nextstepaction"]
> [HTML マーカーを追加する](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [線レイヤーを追加する](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [多角形レイヤーを追加する](map-add-shape.md)
