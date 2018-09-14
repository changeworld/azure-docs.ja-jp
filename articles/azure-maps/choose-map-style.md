---
title: Azure Maps のマップ スタイル機能 | Microsoft Docs
description: Azure Maps のスタイル関連の機能について説明します。
author: walsehgal
ms.author: v-musehg
ms.date: 08/31/2018
ms.topic: how-to-guides
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b95e3c325160d1c0b775320e25fdd40514b02b9f
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666873"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Azure Maps でマップ スタイルを選択する
Azure Maps には、選択できるマップ スタイルが 4 種類あります。 マップ スタイルの詳細については、[Azure Maps でサポートされているマップ スタイル](./supported-map-styles.md)に関するページを参照してください。 この記事では、スタイル関連の機能を使用して、マップの読み込み時にスタイルを設定する方法、新しいスタイルを設定する方法、スタイル選択コントロールを使用する方法について説明します。

## <a name="setting-style-on-map-load"></a>マップの読み込み時にスタイルを設定する

<iframe height='500' scrolling='no' title='マップの読み込み時にスタイルを設定する' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上で、Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による<a href='https://codepen.io/azuremaps/pen/WKOQRq/'>マップの読み込み時にスタイルを設定する</a>ペンを表示します。
</iframe>

上記のコードでは、スタイルをグレースケールに設定したマップ オブジェクトを作成しています。 マップの作成方法については、「[マップを作成する](./map-create.md)」を参照してください。

## <a name="updating-the-style"></a>スタイルの更新

<iframe height='500' scrolling='no' title='スタイルの更新' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上で、Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による<a href='https://codepen.io/azuremaps/pen/yqXYzY/'>スタイルを更新する</a>ペンを表示します。
</iframe>

上記のコードでは、最初のコード ブロックでスタイルが事前に設定されていないマップ オブジェクトが作成されます。 マップの作成方法については、「[マップを作成する](./map-create.md)」を参照してください。

2 つ目のコード ブロックでは、マップの [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setstyle) メソッドが使用され、マップ スタイルが satellite に設定されます。

## <a name="adding-the-style-picker"></a>スタイル選択機能の追加

<iframe height='500' scrolling='no' title='スタイル選択機能の追加' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上で、Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による<a href='https://codepen.io/azuremaps/pen/OwgyvG/'>スタイル選択機能を追加する</a>ペンを表示します。
</iframe>

上記のコードの最初のコード ブロックでは、スタイルが事前に設定されていないマップ オブジェクトが作成されます。 マップの作成方法については、「[マップを作成する](./map-create.md)」を参照してください。

2 つ目のコード ブロックでは、atlas の [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol?view=azure-iot-typescript-latest#stylecontrol) コンストラクターを使用してスタイル セレクターが構築されます。

スタイル選択機能を使用すると、マップのスタイルを選択できます。 3 つ目のコード ブロックでは、マップの [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) メソッドを使用してスタイル選択機能がマップに追加されます。

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。 
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setstyle)
    * [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol)

* [Atlas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest)
    * [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol?view=azure-iot-typescript-latest#stylecontrol)
    
マップに追加するコード例の詳細については、次の記事を参照してください。
* [マップ コントロールを追加する](./map-add-controls.md)
* [ピンを追加する](./map-add-pin.md)
