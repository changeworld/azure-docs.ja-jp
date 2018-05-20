---
title: Azure Maps を使用してマップを作成する | Microsoft Docs
description: JavaScript マップを作成する方法
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
ms.openlocfilehash: 9a7c611860a6d32f82d6714d945c62e6c562f91f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="create-a-map"></a>マップを作成する

この記事では、マップを作成する方法について説明します。  

## <a name="understand-the-code"></a>コードの理解

マップを構築する方法は 2 つあります。 中心点とズーム レベルを指定してマップのカメラを設定する方法と、南西の境界点と北東の境界点を指定して、マップのカメラ境界を設定する方法です。

<a id="setCameraOptions"></a>

### <a name="setting-the-camera"></a>カメラを設定する

<iframe height='310' scrolling='no' title='CameraOptions を介してマップを作成する' src='//codepen.io/azuremaps/embed/qxKBMN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> で Azure LBS (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) を使用し、<a href='https://codepen.io/azuremaps/pen/qxKBMN/'>CameraOptions</a> を介してマップに作成する Pen の例です。
</iframe>

上記のコードでは、`new atlas.Map()` を介して[マップ オブジェクト](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)が作成されています。 中央やズーム レベルなどのマップのプロパティは、[CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/cameraoptions?view=azure-iot-typescript-latest) の一部です。 CameraOptions は、マップコンストラクターで、またはマップ クラスの [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamera) 関数を介して定義できます。

<a id="setCameraBoundsOptions"></a>

### <a name="setting-the-camera-bounds"></a>カメラ境界を設定する

<iframe height='310' scrolling='no' title='CameraBoundsOptions を介してマップを作成する' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> で Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) を使用し、<a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>CameraBoundsOptions</a> を介してマップに作成する Pen の例です。
</iframe>

上記のコードでは、`new atlas.Map()` を介して[マップ オブジェクト](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)が構築されています。 境界ボックスなどのマップのプロパティは、[CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/cameraboundsoptions?view=azure-iot-typescript-latest) の一部です。 CameraBoundsOptions は、マップ クラスの [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds) 関数で定義できます。

## <a name="try-out-the-code"></a>コードを実行する 

前述のサンプル コードをご覧ください。 左側の [JS] タブで JavaScript コードを編集し、右側の [結果] タブでマップ ビューの変更を確認することができます。 [Edit on CodePen]\(CodePen で編集\) ボタンをクリックして CodePen のコードを編集することもできます。 

<a id="relatedReference"></a>

## <a name="next-steps"></a>次の手順

この記事で使われているクラスとメソッドの詳細については、次を参照してください。 
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamera)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds)

