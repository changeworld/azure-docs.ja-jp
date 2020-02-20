---
title: マップ スタイルの機能 | Microsoft Azure Maps
description: この記事では、Microsoft Azure Maps Web SDK で使用できるスタイル関連の機能について説明します。
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: cabf39f017afe440c883a63db57643c5c5367128
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2020
ms.locfileid: "77189740"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Azure Maps でマップ スタイルを選択する

[Azure Maps でサポートされているマップ スタイル](./supported-map-styles.md)の多くは、Web SDK で利用できます。 この記事では、スタイル関連の機能を使用する方法について説明します。 マップの読み込み時にスタイルを設定する方法と、スタイル ピッカー コントロールを使用して新しいマップ スタイルを設定する方法を確認します。

## <a name="set-style-on-map-load"></a>マップの読み込み時にスタイルを設定する

次のコードでは、マップの `style` オプションが初期化時に `grayscale_dark` に設定されます。

<br/>

<iframe height='500' scrolling='no' title='マップの読み込み時にスタイルを設定する' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上で、Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による<a href='https://codepen.io/azuremaps/pen/WKOQRq/'>マップの読み込み時にスタイルを設定する</a>ペンを表示します。
</iframe>

## <a name="update-the-style"></a>スタイルを更新する

次のコードでは、マップ インスタンスが読み込まれた後、[setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 関数を使用して、マップのスタイルが `road` から `satellite` に更新されます。

<br/>

<iframe height='500' scrolling='no' title='スタイルの更新' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上で、Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による<a href='https://codepen.io/azuremaps/pen/yqXYzY/'>スタイルを更新する</a>ペンを表示します。
</iframe>

## <a name="add-the-style-picker"></a>スタイル選択機能を追加する

次のコードでは、マップに [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) を追加しているので、ユーザーは異なるマップ スタイルに簡単に切り替えることができます。 右上隅の近くにあるマップ スタイル コントロールを使用して、マップ スタイルを切り替えます。

<br/>

<iframe height='500' scrolling='no' title='スタイル選択機能の追加' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上で、Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による<a href='https://codepen.io/azuremaps/pen/OwgyvG/'>スタイル選択機能を追加する</a>ペンを表示します。
</iframe>

> [!TIP]
> 既定では、Azure Maps の S0 価格レベルを使用している場合、スタイルの選択ツール コントロールに利用可能なすべてのスタイルが一覧表示されます。 この一覧に表示されるスタイルの数を減らすには、一覧に表示するスタイルの配列をスタイルの選択ツールの `mapStyle` オプションに渡します。 S1 を使用していて、使用できるすべてのスタイルを表示する場合は、スタイル選択ツールの `mapStyles` オプションを `"all"` に設定します。

## <a name="next-steps"></a>次のステップ

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

マップにコントロールを追加する場合は、以下を参照してください。

> [!div class="nextstepaction"]
> [マップ コントロールを追加する](map-add-controls.md)

> [!div class="nextstepaction"]
> [ピンを追加する](map-add-pin.md)
