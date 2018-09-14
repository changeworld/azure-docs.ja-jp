---
title: Azure Maps の座標に関する情報を表示する | Microsoft Docs
description: ユーザーが座標を選択したときにマップ上の住所に関する情報を表示する方法
author: jingjing-z
ms.author: jinzh
ms.date: 08/31/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 331e687c40f21b0bf6074239969848c632682773
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2018
ms.locfileid: "43667483"
---
# <a name="get-information-from-a-coordinate"></a>座標から情報を取得する

この記事では、住所の逆引き検索を行い、マウスをクリックしたときに、クリックされた位置の住所をポップアップ画面に表示する方法について説明します。

## <a name="understand-the-code"></a>コードの理解

<iframe height='500' scrolling='no' title='座標から情報を取得する (サービス モジュール)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上で、Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による<a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>座標から情報を取得する (サービス モジュール)</a> ペンを表示します。
</iframe>

上記のコードの最初のコード ブロックでは、マップ オブジェクトが作成されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

2 つ目のコード ブロックの行で、サービス クライアントがインスタンス化されます。

3 つ目のコード ブロックでは、マウス カーソルのスタイルがポインターに更新されます。

4 つ目のコード ブロックでは、ポップアップが作成されます。 手順については、[マップへのポップアップの追加](./map-add-popup.md)に関する記事を参照してください。

最後のコード ブロックでは、マウス クリック用のイベント リスナーが追加されます。 マウスをクリックすると、クリックしたポイントの座標を使用した検索クエリが作成されます。 次に、マップの [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.search?view=azure-iot-typescript-latest#getsearchaddressreverse) エンドポイントを使用して、座標のアドレスのクエリを実行します。

適切に応答するために、ポップアップ クラスの [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions) 関数を使用して、クリックされた場所の住所を収集し、ポップアップのコンテンツと位置が定義されます。

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。 
* [住所の逆引き検索](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [マップ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addeventlistener)
* [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
    * [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions)
    * [open](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open)
    * [close](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#close)

マップに追加するコード例の詳細については、次の記事を参照してください。
* [A から B までのルートを表示する ](./map-route.md)
* [トラフィックを表示する](./map-show-traffic.md)
