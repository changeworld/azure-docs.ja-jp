---
title: Azure Maps を使用してマップを作成する | Microsoft Azure Maps
description: この記事では、Microsoft Azure Maps Web SDK を使用して、Web ページにマップをレンダリングする方法について説明します。
author: jingjing-z
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 578abae5b206b31674b00b9d27ef34174b93759f
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988585"
---
# <a name="create-a-map"></a>マップを作成する

この記事では、マップを作成し、マップをアニメーション化する方法を示します。  

## <a name="loading-a-map"></a>マップを読み込む

マップを読み込むには、[Map クラス](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)の新しいインスタンスを作成します。 マップを初期化するときに、マップをレンダリングするための DIV 要素 ID を渡し、マップの読み込み時に使用するオプションのセットを渡します。 `atlas` 名前空間で既定の認証情報が指定されていない場合は、マップの読み込み時にマップ オプションでこの情報を指定する必要があります。 マップでは、パフォーマンス向上のために複数のリソースが非同期的に読み込まれます。 そのため、マップ インスタンス作成後に、`ready` または `load` イベントをマップにアタッチし、マップと対話する追加のコードをイベント ハンドラーに追加します。 `ready` イベントは、プログラムで対話するのに十分なリソースがマップに読み込まれるとすぐに発生します。 `load` イベントは、初期マップ ビューの読み込みが完全に完了した後で発生します。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="基本的なマップの読み込み" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/rXdBXx/'>Basic map load</a>」Pen を表示します。
</iframe>

> [!TIP]
> 同じページに複数のマップを読み込むことができます。 同じページにある複数のマップでは、認証と言語の設定は同じでも異なっていてもかまいません。

## <a name="show-a-single-copy-of-the-world"></a>世界の 1 つのコピーを表示する

ワイド画面でマップをズームアウトすると、世界の複数のコピーが水平方向に表示されます。 このオプションは一部のシナリオには適していますが、他のアプリケーションでは、世界の 1 つのコピーを表示する方が適しています。 この動作は、マップの `renderWorldCopies` オプションを `false` に設定することで実装されます。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false</a>」Pen を表示します。
</iframe>

## <a name="controlling-the-map-camera"></a>マップ カメラの制御

マップのカメラを使用してマップの表示領域を設定する方法が 2 つあります。 マップの読み込み時にカメラのオプションを設定できます。 または、マップが読み込まれた後の任意の時点で `setCamera` オプションを呼び出して、プログラムによってマップ ビューを更新できます。  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>カメラの設定

次のコードでは、[Map オブジェクト](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)が作成され、中心とズームのオプションが設定されます。 中心やズーム レベルなどのマップのプロパティは、[CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) の一部です。

<br/>

<iframe height='500' scrolling='no' title='CameraOptions を介してマップを作成する' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) によって、Pen の <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Create a map via `CameraOptions` </a> を表示します。
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>カメラ境界の設定

次のコードでは、`new atlas.Map()` によって [Map オブジェクト](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)が構築されます。 `CameraBoundsOptions` などの Map プロパティは、Map クラスの [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 関数を介して定義できます。 境界とパディングのプロパティは、`setCamera` を使用して設定します。

<br/>

<iframe height='500' scrolling='no' title='CameraBoundsOptions を介してマップを作成する' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) によって Pen の <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Create a map via `CameraBoundsOptions` </a> を表示します。
</iframe>

### <a name="animate-map-view"></a>マップ ビューをアニメーション化する

次のコードでは、最初のコード ブロックでマップが作成され、中心とズームのマップ スタイルが設定されます。 2 番目のコード ブロックでは、アニメーション ボタンのクリック イベント ハンドラーが作成されます。 このボタンがクリックされると、`setCamera` 関数が呼び出され、[CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) と [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions) にはランダムな値が指定されます。

<br/>

<iframe height='500' scrolling='no' title='マップ ビューをアニメーション化する' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/WayvbO/'>Animate Map View</a>」Pen を表示します。
</iframe>

## <a name="try-out-the-code"></a>コードを実行する

コード サンプルをご覧ください。 **[JS] タブ**内で JavaScript コードを編集し、 **[結果] タブ**でマップ ビューの変更を確認することができます。また、右上隅にある **[Edit on CodePen]\(CodePen で編集\)** をクリックして、CodePen でコードを変更することもできます。

<a id="relatedReference"></a>

## <a name="next-steps"></a>次のステップ

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)

ご利用のアプリに機能を追加するには、次のコード例を参照してください。

> [!div class="nextstepaction"]
> [マップのスタイルを変更する](choose-map-style.md)

> [!div class="nextstepaction"]
> [マップにコントロールを追加する](map-add-controls.md)

> [!div class="nextstepaction"]
> [コード サンプル](https://docs.microsoft.com/samples/browse/?products=azure-maps)
