---
title: Azure Maps で描画ツール モジュールを使用する | Microsoft Docs
description: Azure Maps Web SDK を使用して描画オプション データを設定する方法
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 0ac9bc775798a14e6431718bc602d8ff41288c10
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75408162"
---
# <a name="use-the-drawing-tools-module"></a>描画ツール モジュールを使用する

Azure Maps の Web SDK は、*描画ツール モジュール*を提供しています。 このモジュールを使用すると、マウスやタッチ スクリーンなどの入力デバイスを使用して、マップ上で簡単に図形の描画や編集ができます。 このモジュールのコア クラスは[描画マネージャー](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-)であり、マップ上で図形を描画したり編集したりするために必要なすべての機能を提供します。 描画マネージャーは、直接使用することも、カスタム ツールバーの UI と統合することもできます。また、組み込みの[描画ツールバー](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) クラスを使用することもできます。 

## <a name="loading-the-drawing-tools-module-in-a-webpage"></a>Web ページへの描画ツール モジュールの読み込み

1. 新しい HTML ファイルを作成し、[通常のマップを実装します](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)。
2. Azure Maps の描画ツール モジュールを読み込みます。 次の 2 つの方法のいずれかで読み込みます。
    - グローバルにホストされている Azure Maps サービス モジュールの Azure Content Delivery Network のバージョンを使用します。 ファイルの `<head>` 要素に JavaScript および CSS スタイルシートへの参照を追加します。

        ```html
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0.1/atlas-drawing.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0.1/atlas-drawing.min.js"></script>
        ```

    - または、[azure-maps-drawing-tools](https://www.npmjs.com/package/azure-maps-drawing-tools) npm パッケージを使用して、Azure Maps Web SDK ソース コード用の描画ツール モジュールをローカルに読み込み、アプリを使用してそれをホストします。 このパッケージには TypeScript 定義も含まれています。 次のコマンドを実行します。
    
        > **npm install azure-maps-drawing-tools**
    
        次に、ファイルの `<head>` 要素に JavaScript および CSS スタイルシートへの参照を追加します。

         ```html
        <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.css" type="text/css" />
        <script src="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.js"></script>
         ```

## <a name="use-the-drawing-manager-directly"></a>描画マネージャーを直接使用する

描画ツール モジュールがアプリケーションに読み込まれたので、[描画マネージャー](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-)を使用して、マップ内の描画機能と編集機能を有効にすることができます。 インスタンス化の間に描画マネージャーのオプションを指定することも、`drawingManager.setOptions()` 関数を使用することもできます。

### <a name="set-the-drawing-mode"></a>描画モードを設定する

次のコードでは、描画マネージャーのインスタンスを作成して、描画モード (**mode**) オプションを設定します。 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

次のコードは、描画マネージャーの描画モードを設定する方法の、実行できる完全な例です。 多角形の描画を開始するには、マップをクリックします。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="多角形を描画する" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>Draw a polygon</a>」Pen を参照してください。
</iframe>


### <a name="set-the-interaction-type"></a>操作の種類を設定する

描画マネージャーでは、図形を描画するためにマップを対話式に操作する 3 つの異なる方法がサポートされています。

* `click` - マウスまたはタッチでクリックしたときに座標が追加されます。
* `freehand ` - マウスまたはタッチでマップ上にドラッグしたときに座標が追加されます。 
* `hybrid` - マウスまたはタッチでクリックまたはドラッグしたときに座標が追加されます。

次のコードでは、多角形描画モードが有効になり、描画マネージャーが `freehand` に従うという描画操作の種類が設定されます。 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

次に示すのは、マウスの左ボタンを押したままドラッグすることでマップ上に自由に多角形を描画できる機能を実装するコード サンプルです。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="フリーハンドの描画" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>Free-hand drawing</a>」Pen を参照してください。
</iframe>


### <a name="customizing-drawing-options"></a>描画オプションをカスタマイズする

前の例では、描画マネージャーをインスタンス化するときに描画オプションをカスタマイズする方法を示しました。 `drawingManager.setOptions()` 関数を使用して、描画マネージャーのオプションを設定することもできます。 次に示すのは、setOptions 関数を使用した描画マネージャーのすべてのオプションのカスタマイズをテストするツールです。

<br/>

<iframe height="685" title="描画マネージャーをカスタマイズする" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>Get shape data</a>」Pen を参照してください。
</iframe>


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
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [描画マネージャー](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [描画ツール バー](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
