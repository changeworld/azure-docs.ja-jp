---
title: Azure Maps の座標に関する情報を表示する | Microsoft Docs
description: ユーザーが座標を選択したときにマップ上の住所に関する情報を表示する方法
author: jingjing-z
ms.author: jinzh
ms.date: 11/15/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 20616adf649924a13e80411aa5135889a175f442
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2019
ms.locfileid: "55750190"
---
# <a name="get-information-from-a-coordinate"></a>座標から情報を取得する

この記事では、クリックしたポップアップの場所のアドレスを表示するように、逆アドレスの検索を行う方法を示しています。

逆アドレスの検索を行うには、2 つの方法があります。 1 つ目の方法は、サービス モジュールを介して [Azure Maps Reverse Address Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) にクエリを行う方法です。 もう 1 つの方法では、API への [XMLHttpRequest](https://xhr.spec.whatwg.org/) を作成してアドレスを見つけます。 以下で両方の方法を調べることができます。

## <a name="make-a-reverse-search-request-via-service-module"></a>サービス モジュールを使用して逆方向の検索要求を行う

<iframe height='500' scrolling='no' title='座標から情報を取得する (サービス モジュール)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上で、Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による<a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>座標から情報を取得する (サービス モジュール)</a> ペンを表示します。
</iframe>

コードの最初のブロックでは、マップ オブジェクトが作成されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

2 つ目のコード ブロックの行では、クライアント サービスがインスタンス化されます。

3 つ目のコード ブロックでは、マウス カーソルのスタイルがポインターと [popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) オブジェクトに更新されます。 手順については、[マップへのポップアップの追加](./map-add-popup.md)に関する記事を参照してください。

4 つ目のコード ブロックでは、マウス クリック用の[イベント リスナー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)が追加されます。 マウスをクリックすると、クリックしたポイントの座標を使用した検索クエリが作成されます。 次に、マップの [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.search?view=azure-iot-typescript-latest) エンドポイントを使用して、座標のアドレスのクエリを実行します。

適切に応答するために、ポップアップ クラスの [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) 関数を使用して、クリックされた場所の住所を収集し、ポップアップのコンテンツと位置が定義されます。

座標情報を取得する前にマップの読み込みが完全に行われるように、カーソル、ポップアップ オブジェクト、およびクリック イベントの変更はすべてマップの[ロード イベント リスナー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)で作成されます。

## <a name="make-a-reverse-search-request-via-xmlhttprequest"></a>XMLHttpRequest を使用して逆方向の検索要求を行う

<iframe height='500' scrolling='no' title='座標から情報を取得する' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>ペンを参照してください <a href='https://codepen.io'>CodePen</a> の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) によって<a href='https://codepen.io/azuremaps/pen/ddXzoB/'>座標の情報を取得</a>します。
</iframe>

上記のコードの最初のブロックでは、マップ オブジェクトが作成されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

2 つ目のコード ブロックでは、マウス カーソルのスタイルがポインターと [popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) オブジェクトに更新されます。 手順については、[マップへのポップアップの追加](./map-add-popup.md)に関する記事を参照してください。

3 つ目のコード ブロックでは、マウス クリック用のイベント リスナーが追加されます。 マウスをクリックすると、クリックされた座標アドレスのクエリを実行するために [XMLHttpRequest](https://xhr.spec.whatwg.org/) が [Azure Maps Reverse Address Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) に送信されます。 適切に応答するために、ポップアップ クラスの [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) 関数を使用して、クリックされた場所の住所を収集し、ポップアップのコンテンツと位置が定義されます。

座標情報を取得する前にマップの読み込みが完全に行われるように、カーソル、ポップアップ オブジェクト、およびクリック イベントの変更はすべてマップの[ロード イベント リスナー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)で作成されます。

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

完全なコードの例については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [A から B までのルートを表示する ](./map-route.md)

> [!div class="nextstepaction"]
> [トラフィックを表示する](./map-show-traffic.md)
