---
title: サポートされているマップ スタイル | Microsoft Azure Maps
description: この記事では、Microsoft Azure Maps でサポートされているさまざまなマップ レンダリング スタイルについて説明します。
author: walsehgal
ms.author: v-musehg
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 92e48486777d82589a56074790dd709c5d525859
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910637"
---
# <a name="azure-maps-supported-map-styles"></a>Azure Maps でサポートされているマップ スタイル
Azure Maps は、次に説明するように、いくつかの異なる組み込みマップ スタイルをサポートしています。

## <a name="road"></a>道路
**道路**マップは標準マップであり、道路、自然および人工の地物、それらの地物のラベルが表示されます。

![道路マップ スタイル](./media/supported-map-styles/road.png)

**該当する API:**
* [マップ画像](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [マップ タイル](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK マップ コントロール
* Android マップ コントロール

## <a name="blank-and-blank_accessible"></a>blank と blank_accessible

**blank** と **blank_accessible** のマップ スタイルでは、データを視覚化する空白のキャンバスが提供されます。 基本マップが表示されていない場合でも、**blank_accessible** スタイルでは、マップが配置されている場所の詳細を含むスクリーン リーダーの更新が引き続き提供されます。

> [!Note]
> Web SDK では、マップの DIV 要素の CSS `background-color` スタイルを設定することによって、マップの背景色を変更できます。

**該当する API:**
* Web SDK マップ コントロール

## <a name="satellite"></a>衛星 
**衛星**スタイルは、衛星画像と航空映像の組み合わせです。

![衛星タイル マップ スタイル](./media/supported-map-styles/satellite.png)

**該当する API:**
* [衛星タイル](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Web SDK マップ コントロール
* Android マップ コントロール

## <a name="satellite_road_labels"></a>satellite_road_labels
このマップ スタイルは、衛星画像と航空映像の上に道路とラベルが重ねて表示されたハイブリッドです。

![satellite_road_labels マップ スタイル](./media/supported-map-styles/satellite_road_labels.png)

**該当する API:**
* Web SDK マップ コントロール
* Android マップ コントロール

## <a name="grayscale_dark"></a>grayscale_dark
**グレースケール ダーク**は、道路マップ スタイルの濃いバージョンです。

![gray_scale マップ スタイル](./media/supported-map-styles/grayscale_dark.png)

**該当する API:**
* [マップ画像](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [マップ タイル](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK マップ コントロール 
* Android マップ コントロール


## <a name="grayscale_light"></a>grayscale_light
**グレースケール ライト**は、道路マップ スタイルの薄いバージョンです。

![グレースケール ライト マップ スタイル](./media/supported-map-styles/grayscale_light.png)

**該当する API:**
* Web SDK マップ コントロール
* Android マップ コントロール


## <a name="night"></a>night
**night** は、道路マップ スタイルの色の濃いバージョンで、道路と記号が色付きです。

![ナイト マップ スタイル](./media/supported-map-styles/night.png)

**該当する API:**
* Web SDK マップ コントロール
* Android マップ コントロール

## <a name="road_shaded_relief"></a>road_shaded_relief
**道路の影付きレリーフ**は、地球の輪郭で完成する、Azure Maps の主要スタイルです。

![影付きレリーフ マップ スタイル](./media/supported-map-styles/shaded-relief.png)

**該当する API:**
* [マップ タイル](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK マップ コントロール
* Android マップ コントロール


## <a name="next-steps"></a>次のステップ

Azure Maps でマップ スタイルを設定する方法について説明します。

> [!div class="nextstepaction"]
> [マップ スタイルを選択する](https://docs.microsoft.com/azure/azure-maps/choose-map-style)
