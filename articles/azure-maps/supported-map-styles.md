---
title: Azure Maps でサポートされているマップ スタイル | Microsoft Docs
description: Azure Maps でサポートされているマップ スタイル
author: walsehgal
ms.author: v-musehg
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: db396ad06bf46cbbaf486696b68393a6a4214c2f
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230832"
---
# <a name="azure-maps-supported-map-styles"></a>Azure Maps でサポートされているマップ スタイル
Azure Maps は、次に説明するように、いくつかの異なる組み込みマップ スタイルをサポートしています。

## <a name="road"></a>道路
**道路**マップは標準マップであり、道路、自然および人工の地物、それらの地物のラベルが表示されます。

![道路](./media/supported-map-styles/road.png)

**該当する API:**
* [マップ画像](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [マップ タイル](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* JS マップ コントロール
* Android マップ コントロール

## <a name="satellite"></a>衛星 
**衛星**スタイルは、衛星画像と航空映像の組み合わせです。

![衛星](./media/supported-map-styles/satellite.png)

**該当する API:**
* [衛星タイル](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* JS マップ コントロール
* Android マップ コントロール

## <a name="satelliteroadlabels"></a>satellite_road_labels
このマップ スタイルは、衛星画像と航空映像の上に道路とラベルが重ねて表示されたハイブリッドです。

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**該当する API:**
* JS マップ コントロール
* Android マップ コントロール

## <a name="grayscaledark"></a>grayscale_dark
**グレースケール ダーク**は、道路マップ スタイルの濃いバージョンです。

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**該当する API:**
* JS マップ コントロール 
* Android マップ コントロール


## <a name="grayscalelight"></a>grayscale_light
**グレースケール ライト**は、道路マップ スタイルの薄いバージョンです。

![グレースケール ライト](./media/supported-map-styles/grayscale_light.png)

**該当する API:**
* JS マップ コントロール
* Android マップ コントロール


## <a name="night"></a>night
**night** は、道路マップ スタイルの色の濃いバージョンで、道路と記号が色付きです。

![night](./media/supported-map-styles/night.png)

**該当する API:**
* JS マップ コントロール
* Android マップ コントロール

## <a name="roadshadedrelief"></a>road_shaded_relief
**道路の影付きレリーフ**は、地球の輪郭で完成する、Azure Maps の主要スタイルです。

![影付きレリーフ](./media/supported-map-styles/shaded-relief.png)

**該当する API:**
* [マップ タイル](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* JS マップ コントロール
* Android マップ コントロール
