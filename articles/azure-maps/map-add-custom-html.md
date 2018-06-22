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
ms.openlocfilehash: 3ee1f394536061cecaa5d775650149b30098560e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34600223"
---
# <a name="add-custom-html-to-the-map"></a>マップにカスタム HTML を追加する

この記事では、イメージ ファイルなどのカスタム HTML をマップに追加する方法について説明します。 

## <a name="understand-the-code"></a>コードの理解

<iframe height='466' scrolling='no' title='マップにカスタム HTML を追加する - png' src='//codepen.io/azuremaps/embed/MVoeVw/?height=466&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> 上の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による「<a href='https://codepen.io/azuremaps/pen/MVoeVw/'>Add custom html to a map - png</a>」Pen を表示します。
</iframe>

上記のコードでは、コードの最初のブロックでマップ オブジェクトを作成しています。 作成方法については、「[Create a map (マップの作成)](./map-create.md)」をご覧ください。

コードの 2 番目のブロックでは、イメージから HTML 要素を作成しています。

コードの最後のブロックでは、マップ クラスの [addHtml](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addhtml) 関数を使って、マップ上の指定された位置にイメージを追加しています。

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。 
* [マップ](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addHtml](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addhtml)
    
マップに追加するコード例の詳細については、次の記事を参照してください。 
* [検索結果を表示する](./map-search-location.md)
* [座標から情報を取得する](./map-get-information-from-coordinate.md)

