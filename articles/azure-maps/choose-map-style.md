---
title: Azure Maps でマップのスタイルを変更する | Microsoft Azure Maps
description: この記事では、Microsoft Azure Maps Web SDK で使用できるスタイル関連の機能について説明します。
author: philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: b1c5d9b5cd2b6b9bfecf8a0af79699061003eec1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335681"
---
# <a name="change-the-style-of-the-map"></a>マップのスタイルを変更する

マップでは複数の異なる[スタイル オプション](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)がサポートされています。これらのオプションは、マップを初期化するとき、または maps `setStyle` 関数を使用して後で設定することができます。 この記事では、これらのスタイル オプションを使用してマップの外観をカスタマイズする方法について説明します。 マップの読み込み時にスタイルを設定する方法と、スタイル ピッカー コントロールを使用して新しいマップ スタイルを設定する方法を確認します。

## <a name="set-the-style-options"></a>スタイル オプションを設定する 

スタイル オプションは、初期化するとき、または後で maps `setStyle` 関数を使用して更新するときに、マップに渡すことができます。

```javascript
//Set the style options when creating the map.
var map = new atlas.Map('map', {
    renderWorldCopies: false,
    showBuildingModels: true

    //Additional map options.
};

//Update the style options at anytime using setStyle function.
map.setStyle({
    renderWorldCopies: true,
    showBuildingModels: false
});
```

次のツールは、さまざまなスタイル オプションによってマップのレンダリング方法がどのように変わるかを示しています。 3D の建物を表示するには、大都市の近くにズーム インします。 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="マップ スタイル オプション" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> で Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/eYNMjPb'>マップ スタイル オプション</a>」を参照してください。
</iframe>

## <a name="choose-a-base-map-style"></a>基本マップ スタイルを選択する

最も一般的なマップ スタイル オプションの 1 つは、スタイル設定された基本マップのスタイルを変更するために使用されます。 [Azure Maps でサポートされているマップ スタイル](supported-map-styles.md)の多くは、Web SDK で利用できます。 

### <a name="set-base-map-style-on-map-load"></a>マップの読み込み時に基本マップ スタイルを設定する


マップスタイルは、`style` オプションを設定することで、マップを初期化するときに指定できます。 次のコードでは、マップの `style` オプションが初期化時に `grayscale_dark` に設定されます。

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additiona map options
);
```

<br/>

<iframe height='500' scrolling='no' title='マップの読み込み時にスタイルを設定する' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上で、Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による<a href='https://codepen.io/azuremaps/pen/WKOQRq/'>マップの読み込み時にスタイルを設定する</a>ペンを表示します。
</iframe>

### <a name="update-the-base-map-style"></a>基本マップ スタイルを更新する

 マップ スタイルは、`setStyle` 関数を使用し、`style` オプションを目的のマップ スタイルに設定することで更新できます。

```javascript
map.setStyle({ style: 'satellite' });
```

次のコードでは、マップ インスタンスが読み込まれた後、[setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setstyle-styleoptions-) 関数を使用して、マップのスタイルが `road` から `satellite` に更新されます。

<br/>

<iframe height='500' scrolling='no' title='スタイルの更新' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上で、Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による<a href='https://codepen.io/azuremaps/pen/yqXYzY/'>スタイルを更新する</a>ペンを表示します。
</iframe>

### <a name="add-the-style-picker"></a>スタイル選択機能を追加する

スタイル選択コントロールには、ポップアップ パネル付きの使いやすいボタンがあり、エンド ユーザーがマップ スタイルを変更するために使用できます。 スタイル選択機能には、2 つの異なるレイアウト オプションがあります。 既定では、スタイル選択機能では `icons` レイアウトが使用され、すべてのマップ スタイルが横一列に並んだアイコンとして表示されます。 

<center>

![スタイル選択機能アイコンのレイアウト](media/choose-map-style/style-picker-icon-layout.png)</center>

2 番目のレイアウト オプションは `list` と呼ばれ、マップ スタイルのスクロール可能な一覧が表示されます。  

<center>

![スタイル選択機能の一覧のレイアウト](media/choose-map-style/style-picker-list-layout.png)</center>


次のコードで、スタイル選択コントロールのインスタンスを作成し、マップの右上隅に追加する方法を示します。 スタイル選択機能は、黒のスタイルで、リスト レイヤーを使用して選択されたいくつかのマップ スタイルを表示するように設定されています。

```javascript
map.controls.add(new atlas.control.StyleControl({
    mapStyles: ['road', 'night', 'grayscale_dark', 'grayscale_light'],
    layout: 'list',
    style: 'dark'
}), {
    position: 'top-right'
}); 
```

次のコードでは、マップに既定の設定のスタイル選択コントロールを追加しているので、ユーザーは異なるマップ スタイルに簡単に切り替えることができます。 右上隅の近くにあるマップ スタイル コントロールを使用して、マップ スタイルを切り替えます。

<br/>

<iframe height='500' scrolling='no' title='スタイル選択機能の追加' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上で、Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による<a href='https://codepen.io/azuremaps/pen/OwgyvG/'>スタイル選択機能を追加する</a>ペンを表示します。
</iframe>

> [!TIP]
> 既定では、Azure Maps の S0 価格レベルを使用している場合、スタイルの選択ツール コントロールに利用可能なすべてのスタイルが一覧表示されます。 この一覧に表示されるスタイルの数を減らすには、一覧に表示するスタイルの配列をスタイルの選択ツールの `mapStyle` オプションに渡します。 S1 を使用していて、使用できるすべてのスタイルを表示する場合は、スタイル選択ツールの `mapStyles` オプションを `"all"` に設定します。

## <a name="next-steps"></a>次のステップ

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)

> [!div class="nextstepaction"]
> [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol)

> [!div class="nextstepaction"]
> [StyleControlOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.stylecontroloptions)

マップにコントロールを追加する場合は、以下を参照してください。

> [!div class="nextstepaction"]
> [マップ コントロールを追加する](map-add-controls.md)

> [!div class="nextstepaction"]
> [ピンを追加する](map-add-pin.md)
