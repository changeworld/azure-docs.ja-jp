---
title: Azure Maps の座標に関する情報を表示する | Microsoft Docs
description: ユーザーが座標を選択したときにマップ上の住所に関する情報を表示する方法
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
ms.openlocfilehash: bb8644724cc872a0a8bc331e76251218492fd93d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="get-information-from-a-coordinate"></a>座標から情報を取得する

この記事では、住所の逆引き検索を行い、マウスをクリックしたときに、クリックされた位置の住所をポップアップ画面に表示する方法について説明します。 

## <a name="understand-the-code"></a>コードの理解

<iframe height='500' scrolling='no' title='座標から情報を取得する' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>ペンを参照してください <a href='https://codepen.io'>CodePen</a> の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) によって<a href='https://codepen.io/azuremaps/pen/ddXzoB/'>座標の情報を取得</a>します。
</iframe>

上記のコードの最初のコード ブロックでは、マップ オブジェクトが作成されます。 作成方法については、[マップの作成](./map-create.md)に関する記事をご覧ください。

コードの 2 番目のブロックでは、マウス カーソルのスタイルがポインターに更新されます。

コードの 3 番目のブロックでは、ポップアップが作成されます。 手順については、[マップへのポップアップの追加](./map-add-popup.md)に関する記事を参照してください。

最後のコード ブロックは、マウス クリック用のイベント リスナーを追加します。 マウスをクリックすると、[XMLHttpRequest](https://xhr.spec.whatwg.org/) が [Azure Maps Reverse Address Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) に送信されます。 適切に応答するために、ポップアップ クラスの [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#setpopupoptions) 関数を使用して、クリックされた場所の住所を収集し、ポップアップのコンテンツと位置が定義されます。

## <a name="next-steps"></a>次の手順

この記事で使用されているクラスとメソッドの詳細については、以下を参照してください。 
* [住所の逆引き検索](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [マップ](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addeventlistener)
* [ポップアップ](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest)
    * [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#setpopupoptions)
    * [open](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open)
    * [close](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#close)
