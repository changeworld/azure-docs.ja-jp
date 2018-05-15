---
title: Azure Maps にカスタム HTML を追加する | Microsoft Docs
description: Javascript マップにカスタム HTML を追加する方法
services: azure-maps
keywords: ''
author: jinzh-azureiot
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: codepen
ms.openlocfilehash: df50774e1bdca53034d4856f38a6133fe8e89855
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
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

この記事で使われているクラスとメソッドについてさらに詳しく知る。 
* [マップ](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addHtml](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addhtml)
