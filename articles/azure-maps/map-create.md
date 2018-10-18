---
title: Azure Maps を使用してマップを作成する | Microsoft Docs
description: JavaScript マップを作成する方法
author: jingjing-z
ms.author: jinzh
ms.date: 09/14/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9759c4149c6b026837e550dcf3ab0a0156bbb736
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2018
ms.locfileid: "45730011"
---
# <a name="create-a-map"></a>マップを作成する

この記事では、マップを作成する方法について説明します。  

## <a name="understand-the-code"></a>コードの理解

マップを構築する方法は 2 つあります。 中心点とズーム レベルを指定することで、マップのカメラを設定できます。 南西の境界ポイントと北東の境界ポイントを指定して、マップにおけるカメラ境界を設定します。

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>カメラの設定

<iframe height='310' scrolling='no' title='CameraOptions を介してマップを作成する' src='//codepen.io/azuremaps/embed/qxKBMN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) によって、Pen の <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Create a map via `CameraOptions` </a> を表示します。
</iframe>

上記のコードでは、`new atlas.Map()` を介して[マップ オブジェクト](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)が作成されています。 中央やズーム レベルなどのマップのプロパティは、[CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraoptions?view=azure-iot-typescript-latest) の一部です。 `CameraOptions` は、マップコンストラクターで、またはマップ クラスの [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera) 関数を介して定義できます。

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>カメラ境界の設定

<iframe height='310' scrolling='no' title='CameraBoundsOptions を介してマップを作成する' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) によって、Pen の <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Create a map via `CameraBoundsOptions` </a> を表示します。
</iframe>

上記のコードでは、`new atlas.Map()` を介して[マップ オブジェクト](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)が構築されています。 境界ボックスなどのマップのプロパティは、[CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest) の一部です。 `CameraBoundsOptions` は、マップ クラスの [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) 関数によって定義できます。

## <a name="try-out-the-code"></a>コードを実行する

前述のサンプル コードをご覧ください。 左側の **[JS] タブ**で JavaScript コードを編集し、右側の **[結果] タブ**でマップ ビューの変更を確認することができます。 **[Edit on CodePen]\(CodePen で編集\)** ボタンをクリックして CodePen のコードを編集することもできます。

<a id="relatedReference"></a>

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

ご利用のアプリに機能を追加するには、次のコード例を参照してください。

> [!div class="nextstepaction"]
> [マップ スタイルを選択する](choose-map-style.md)

> [!div class="nextstepaction"]
> [マップ コントロールを追加する](map-add-controls.md)
