---
title: 描画ツール モジュール | Microsoft Azure Maps
description: この記事では、Microsoft Azure Maps Web SDK を使用して描画オプション データを設定する方法について学習します
author: anastasia-ms
ms.author: v-stharr
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
ms.custom: devx-track-js
ms.openlocfilehash: fd861e4ab92235ec4f2b3ec8051e854a08effd3c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131072372"
---
# <a name="use-the-drawing-tools-module"></a>描画ツール モジュールを使用する

Azure Maps の Web SDK は、*描画ツール モジュール* を提供しています。 このモジュールを使用すると、マウスやタッチ スクリーンなどの入力デバイスを使用して、マップ上で簡単に図形の描画や編集ができます。 このモジュールのコア クラスは、[描画マネージャー](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager#setoptions-drawingmanageroptions-)です。 描画マネージャーにより、マップ上で図形を描画したり編集したりするために必要なすべての機能が提供されます。 これは直接使用することができ、カスタム ツール バーの UI に統合されています。 組み込みの[描画ツール バー](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar) クラスを使用することもできます。

## <a name="loading-the-drawing-tools-module-in-a-webpage"></a>Web ページへの描画ツール モジュールの読み込み

1. 新しい HTML ファイルを作成し、[通常のマップを実装します](./how-to-use-map-control.md)。
2. Azure Maps の描画ツール モジュールを読み込みます。 次の 2 つの方法のいずれかで読み込みます。
    - グローバルにホストされている Azure Maps サービス モジュールの Azure Content Delivery Network のバージョンを使用します。 ファイルの `<head>` 要素に JavaScript および CSS スタイルシートへの参照を追加します。

        ```html
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/1/atlas-drawing.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/drawing/1/atlas-drawing.min.js"></script>
        ```

    - または、[azure-maps-drawing-tools](https://www.npmjs.com/package/azure-maps-drawing-tools) npm パッケージを使用して、Azure Maps Web SDK ソース コード用の描画ツール モジュールをローカルに読み込み、アプリを使用してそれをホストできます。 このパッケージには TypeScript 定義も含まれています。 次のコマンドを実行します。

      `npm install azure-maps-drawing-tools`

      次に、ファイルの `<head>` 要素に JavaScript および CSS スタイルシートへの参照を追加します。

      ```html
      <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.css" type="text/css" />
      <script src="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.js"></script>
      ```

## <a name="use-the-drawing-manager-directly"></a>描画マネージャーを直接使用する

描画ツール モジュールがアプリケーションに読み込まれたら、[描画マネージャー](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager#setoptions-drawingmanageroptions-)を使用して、描画機能と編集機能を有効にすることができます。 インスタンス化の間に描画マネージャーのオプションを指定することも、`drawingManager.setOptions()` 関数を使用することもできます。

### <a name="set-the-drawing-mode"></a>描画モードを設定する

次のコードでは、描画マネージャーのインスタンスを作成して、描画モード (**mode**) オプションを設定します。 

```javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

次のコードは、描画マネージャーの描画モードを設定する方法の、実行できる完全な例です。 多角形の描画を開始するには、マップをクリックします。

<br/>

<iframe height="500" scrolling="no" title="多角形を描画する" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>Draw a polygon</a>」Pen を参照してください。
</iframe>


### <a name="set-the-interaction-type"></a>操作の種類を設定する

描画マネージャーでは、図形を描画するためにマップを対話式に操作する 3 つの異なる方法がサポートされています。

* `click` - マウスまたはタッチでクリックしたときに座標が追加されます。
* `freehand ` - マウスまたはタッチでマップ上にドラッグしたときに座標が追加されます。 
* `hybrid` - マウスまたはタッチでクリックまたはドラッグしたときに座標が追加されます。

次のコードでは、多角形描画モードが有効になり、描画マネージャーが `freehand` に従うという描画操作の種類が設定されます。 

```javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

 このコード サンプルでは、マップに多角形を描画する機能を実装します。 マウスの左ボタンを押したまま、自由にドラッグするだけです。

<br/>

<iframe height="500" scrolling="no" title="フリーハンドの描画" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>Free-hand drawing</a>」Pen を参照してください。
</iframe>


### <a name="customizing-drawing-options"></a>描画オプションをカスタマイズする

前の例では、描画マネージャーをインスタンス化するときに描画オプションをカスタマイズする方法を示しました。 `drawingManager.setOptions()` 関数を使用して、描画マネージャーのオプションを設定することもできます。 次に示すのは、setOptions 関数を使用した描画マネージャーのすべてのオプションのカスタマイズをテストするツールです。

<br/>

<iframe height="685" title="描画マネージャーをカスタマイズする" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true"><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>Get shape data</a>」Pen を参照してください。
</iframe>


### <a name="put-a-shape-into-edit-mode"></a>シェイプを編集モードにする

既存のシェイプを描画マネージャーの `edit` 関数に渡すことにより、プログラムでそれを編集モードにします。 シェイプが GeoJSON の機能の場合は、渡す前に `atls.Shape` クラスでラップします。

プログラムでシェイプの編集モードを解除するには、描画マネージャーのモードを `idle` に設定します。

```javascript
//If you are starting with a GeoJSON feature, wrap it with the atlas.Shape class.
var feature = { 
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [0,0]
        },
    "properties":  {}
};

var shape = new atlas.Shape(feature);

//Pass the shape into the edit function of the drawing manager.
drawingManager.edit(shape);

//Later, to programmatically take shape out of edit mode, set mode to idle. 
drawingManager.setOptions({ mode: 'idle' });
```

> [!NOTE]
> 描画マネージャーの `edit` 関数に渡されたシェイプは、描画マネージャーによって保持されているデータ ソースに追加されます。 シェイプが以前は別のデータ ソースにあった場合は、そのデータ ソースから削除されます。

シェイプを描画マネージャーに追加して、エンド ユーザーにはその表示と編集を許可し、プログラムではそれを編集モードにできないようにするには、描画マネージャーからデータ ソースを取得して、シェイプをそれに追加します。

```javascript
//The shape(s) you want to add to the drawing manager so 
var shape = new atlas.Shape(feature);

//Retrieve the data source from the drawing manager.
var source = drawingManager.getSource();

//Add your shape.
source.add(shape);

//Alternatively, load in a GeoJSON feed using the sources importDataFromUrl function.
source.importDataFromUrl('yourFeatures.json');
```

次の表では、さまざまな種類のシェイプ機能でサポートされる編集の種類を示します。

| シェイプの機能 | 点の編集 | Rotate | シェイプの削除 |
|---------------|:-----------:|:------:|:------------:|
| ポイント         | ✓           |        | ✓           |
| LineString    | ✓           | ✓      | ✓           |
| 多角形       | ✓           | ✓      | ✓           |
| MultiPoint    |             | ✓      | ✓           |
| MultiLineString |           | ✓      | ✓           |
| MultiPolygon  |             | ✓      | ✓           |
| Circle        | ✓           |        | ✓           |
| Rectangle     | ✓           | ✓      | ✓           |

## <a name="next-steps"></a>次のステップ

描画ツール モジュールのその他の機能の使用方法を確認します。

> [!div class="nextstepaction"]
> [描画ツールバーを追加する](map-add-drawing-toolbar.md)

> [!div class="nextstepaction"]
> [図形データを取得する](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [描画イベントに応答する](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [対話式操作の種類とキーボード ショートカット](drawing-tools-interactions-keyboard-shortcuts.md)

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [描画マネージャー](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)

> [!div class="nextstepaction"]
> [描画ツール バー](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)
