---
title: Azure Maps でのトラフィックの表示 | Microsoft Docs
description: Javascript マップ上にトラフィック データを表示する方法
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 55bfc434082b2d5b7de193e969fc34f710657cdb
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638632"
---
# <a name="show-traffic-on-the-map"></a>マップ上にトラフィックを表示する

この記事では、マップ上にトラフィック情報やインシデント情報を表示する方法について説明します。

## <a name="understand-the-code"></a>コードの理解

<iframe height='456' scrolling='no' title='マップ上にトラフィックを表示する' src='//codepen.io/azuremaps/embed/WMLRPw/?height=456&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Show traffic on a map</a>」Pen を表示します。
</iframe>

上記のコードの最初のコード ブロックでは、マップ オブジェクトが作成されます。 作成方法については、[マップの作成](map-create.md)に関する記事を参照してください。

コードの 2 番目のブロックでは、マップの[イベント リスナー](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)関数内で [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 関数を使用して、トラフィック フローとインシデントをマップ上にレンダリングしています。

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

完全なコードのサンプルについては、次の記事を参照してください。

> [!div class="nextstepaction"]
> [コード サンプル ページ](https://aka.ms/AzureMapsSamples)

ユーザー エクスペリエンスを強化します。

> [!div class="nextstepaction"]
> [操作をマウス イベントにマップする](./map-events.md)

> [!div class="nextstepaction"]
> [アクセス可能なマップをビルド](./map-accessibility.md)
