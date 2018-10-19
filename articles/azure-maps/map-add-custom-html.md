---
title: Azure Maps にカスタム HTML を追加する | Microsoft Docs
description: Javascript マップにカスタム HTML を追加する方法
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: e5cfbc7ddc10edf9b21afce73e3b7f8795fcdac9
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "46121959"
---
# <a name="add-custom-html-to-the-map"></a>マップにカスタム HTML を追加する

この記事では、イメージ ファイルなどのカスタム HTML をマップに追加する方法について説明します。

## <a name="understand-the-code"></a>コードの理解

<iframe height='466' scrolling='no' title='マップにカスタム HTML を追加する - png' src='//codepen.io/azuremaps/embed/MVoeVw/?height=466&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/MVoeVw/'>Add custom html to a map - png</a>」Pen を表示します。
</iframe>

上記のコードの最初のコード ブロックでは、マップ オブジェクトが作成されます。 作成方法については、[マップの作成](./map-create.md)に関する記事を参照してください。

コードの 2 番目のブロックでは、イメージから HTML 要素を作成しています。

コードの最後のブロックでは、マップ クラスの [addHtml](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addhtml) 関数を使って、マップ上の指定された位置にイメージを追加しています。

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

マップに追加するコード例の詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [検索結果を表示する](./map-search-location.md)

> [!div class="nextstepaction"]
> [座標から情報を取得する](./map-get-information-from-coordinate.md)