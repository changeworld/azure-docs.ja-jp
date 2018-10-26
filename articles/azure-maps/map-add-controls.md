---
title: Azure Maps にマップ コントロールを追加する | Microsoft Docs
description: Azure Maps でズーム コントロール、ピッチ コントロール、回転コントロール、およびスタイルの選択ツールをマップに追加する方法。
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 781e5d71637a1e86a56dee0aad3c1a5e00f1807a
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2018
ms.locfileid: "48885789"
---
# <a name="add-map-controls-to-azure-maps"></a>Azure Maps にマップ コントロールを追加する

この記事では、マップにマップ コントロールを追加する方法について説明します。 すべてのコントロールと[スタイルの選択ツール](https://docs.microsoft.com/azure/azure-maps/choose-map-style#adding-the-style-picker)を備えたマップを作成する方法についても学習します。

## <a name="add-zoom-control"></a>ズーム コントロールを追加する

<iframe height='500' scrolling='no' title='ズーム コントロールの追加' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/WKOQyN/'>Adding a zoom control (ズーム コントロールの追加)</a>」を参照してください。
</iframe>

最初のコード ブロックでは、サブスクリプション キーが設定され、スタイルが事前に設定されていないマップ オブジェクトが作成されます。 マップの作成方法については、「[マップを作成する](./map-create.md)」を参照してください。

ズーム コントロールは、マップをズームイン/ズームアウトする機能を追加します。 2 番目のコード ブロックでは、atlas [ZoomControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.zoomcontrol?view=azure-iot-typescript-latest) を使用してズーム コントロール オブジェクトを作成し、これをマップの [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) メソッドを使用してマップに追加しています。 マップが完全に読み込まれた後に読み込まれるようにするために、ズーム コントロールはマップ **イベント リスナー**内にあります。

## <a name="add-pitch-control"></a>ピッチ コントロールを追加する

<iframe height='500' scrolling='no' title='ピッチ コントロールの追加' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/xJrwaP/'>Adding a pitch control (ピッチ コントロールの追加)</a>」を参照してください。
</iframe>

最初のコード ブロックでは、サブスクリプション キーが設定され、スタイルが事前に設定されていないマップ オブジェクトが作成されます。 マップの作成方法については、「[マップを作成する](./map-create.md)」を参照してください。

ピッチ コントロールは、マップのピッチを変更する機能を追加します。 コードの 2 番目のブロックでは、atlas [PitchControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.pitchcontrol?view=azure-iot-typescript-latest) を使用してピッチ コントロール オブジェクトを作成し、これをマップの [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) メソッドを使用してマップに追加しています。 マップが完全に読み込まれた後に読み込まれるようにするために、ピッチ コントロールはマップ **イベント リスナー**内にあります。

## <a name="add-compass-control"></a>コンパス コントロールを追加する

<iframe height='500' scrolling='no' title='回転コントロールの追加' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/GBEoRb/'>Adding a rotate control (回転コントロールの追加)</a>」を参照してください。
</iframe>

最初のコード ブロックでは、サブスクリプション キーが設定され、スタイルが事前に設定されていないマップ オブジェクトが作成されます。 マップの作成方法については、「[マップを作成する](./map-create.md)」を参照してください。

コードの 2 番目のブロックでは、atlas [Compass Control](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.compasscontrol?view=azure-iot-typescript-latest#compasscontrol) を使用してコンパス コントロール オブジェクトを作成しています。 さらに、マップの [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) メソッドを使用して、コンパス コントロールをマップに追加しています。 マップが完全に読み込まれた後に読み込まれるようにするために、コンパス コントロールはマップ **イベント リスナー**内にあります。

## <a name="a-map-with-all-controls"></a>すべてのコントロールを含むマップ

<iframe height='500' scrolling='no' title='すべてのコントロールを含むマップ' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen「<a href='https://codepen.io/azuremaps/pen/qyjbOM/'>A map with all the controls (すべてのコントロールを含むマップ)</a>」を参照してください。
</iframe>

最初のコード ブロックでは、サブスクリプション キーが設定され、スタイルが事前に設定されていないマップ オブジェクトが作成されます。 マップの作成方法については、「[マップを作成する](./map-create.md)」を参照してください。

2 番目のコード ブロックでは、atlas [CompassControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.compasscontrol?view=azure-iot-typescript-latest#compasscontrol) を使用してコンパス コントロール オブジェクトを作成し、これをマップの [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) メソッドを使用してマップに追加しています。

コードの 3 番目のブロックでは、atlas [ZoomControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.zoomcontrol?view=azure-iot-typescript-latest) を使用してズーム コントロール オブジェクトを作成し、これをマップの [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) メソッドを使用してマップに追加しています。

4 番目のコード ブロックでは、atlas [PitchControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.pitchcontrol?view=azure-iot-typescript-latest) を使用してピッチ コントロール オブジェクトを作成し、これをマップの [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) メソッドを使用してマップに追加しています。

コードの最後のブロックでは、atlas [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol?view=azure-iot-typescript-latest#stylecontrol) を使用してスタイルの選択オブジェクトを作成し、これをマップの [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) メソッドを使用してマップに追加しています。 コントロール オブジェクトはすべて、マップが完全に読み込まれた後に読み込まれるようにするため、マップ **イベント リスナー**内に追加されます。

スクリプト内のコントロール オブジェクトの順序により、マップに表示される順序が決まります。 マップ上のコントロールの順序を変更するには、スクリプト内の順序を変更できます。

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Atlas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest)

完全なコードについては、次の記事を参照してください。

> [!div class="nextstepaction"]
> [ピンを追加する](./map-add-pin.md)

> [!div class="nextstepaction"]
> [ポップアップを追加する](./map-add-popup.md)